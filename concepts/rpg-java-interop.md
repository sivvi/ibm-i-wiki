---
title: RPG 与 Java 互调
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, java, interop]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
---

# RPG 与 Java 互调

ILE RPG 通过 JNI（Java Native Interface）调用 JVM 中的 Java 方法，也支持从 Java 调用 RPG 原生程序。

## 调用 Java 的前提条件

IBM i 必须配置 Java（`5770-JV1` 或更高版本）：

```bash
# 检查已安装的 Java 版本
GO Java → 选项 1 或 `java -version`
```

## 从 RPG 调用 Java

### 基本步骤

1. 创建 Java 类/方法
2. 在 RPG 中声明 Java 原型
3. 调用原型

### 声明 Java 原型

```rpgle
**FREE
// 调用 Java String 构造器
DCL-PR newJavaString POINTER EXTPROC('java.lang.String.init');
    DCL-PI *N;
        bytes CHAR(32766) CONST OPTIONS(*STRING : *NOPASS);
    END-PI;
END-PR;

// 调用 String.length()
DCL-PR stringLength INT(10) EXTPROC('java.lang.String.length');
    DCL-PI INT(10);
    END-PI;

// 调用 System.out.println()
DCL-PR printLine INT(10) EXTPROC('java.io.PrintStream.println');
    DCL-PI INT(10);
        msg POINTER;  // java.lang.String 对象
    END-PI;
END-PR;
```

### 创建 JVM

首次调用 Java 前需要初始化 JVM：

```rpgle
**FREE
DCL-DS jvmOptions DIM(10) QUALIFIED;
    option CHAR(512);
END-DS;
DCL-S rc INT(10);
DCL-S jvm POINTER;

// 设置 JVM 选项
jvmOptions(1).option = '-d64';           // 64位
jvmOptions(2).option = '-Xms256m';       // 初始堆
jvmOptions(3).option = '-Xmx1024m';       // 最大堆
jvmOptions(4).option = *BLANKS;           // 结束标记

// 创建 JVM
rc = _JNI_CreateJavaVM(jvm : jvmOptions : 4);
```

### 常见调用示例

```rpgle
**FREE
DCL-PR newString POINTER EXTPROC('java.lang.String.init');
    DCL-PI *N;
        initialString POINTER CONST;
    END-PI;
END-PR;

DCL-PR stringLength INT(10) EXTPROC('java.lang.String.length');
    DCL-PI INT(10);
        javaString POINTER CONST;
    END-PI;
END-PR;

// 使用
DCL-S javaStr POINTER;
DCL-S strLen INT(10);
DCL-S rpgString CHAR(50);

rpgString = 'Hello from RPG';
javaStr = newString(%ADDR(rpgString));
strLen = stringLength(javaStr);
```

## 从 Java 调用 RPG

### PCML（Program Call Markup Language）

PCML 是 IBM i 专有的 XML 格式，用于 Java/外部程序调用 RPG：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<pcml version="1.0">
  <program name="MYPROG" entrypoint="myRPGProc" path="/QSYS.LIB/MYLIB.LIB/MYPROG.PGM">
    <data name="inputData" type="char" length="100" usage="input"/>
    <data name="outputData" type="char" length="100" usage="output"/>
    <data name="returnCode" type="int" length="4" usage="output"/>
  </program>
</pcml>
```

```java
import com.ibm.as400.access.ProgramCall;
import com.ibm.as400.access.ProgramParameter;
import com.ibm.as400.access.AS400;

AS400 system = new AS400("myibm.i");
ProgramCall program = new ProgramCall(system);

// 设置 PCML
program.setPCMLFile("/home/user/myprog.pcml");

ProgramParameter[] params = new ProgramParameter[3];
params[0] = new ProgramParameter(inputData.getBytes(), 100);
params[1] = new ProgramParameter(100);
params[2] = new ProgramParameter(4);

program.setParameterList(params);
program.run();
```

## JNI 注意事项

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 参数类型不匹配 | 原型声明与 Java 签名不符 | 严格按 JNI 类型映射 |
| 对象未释放 | 临时 Java 对象占用堆 | 调用 `DisposeJavaObject()` |
| 异常未处理 | Java 方法抛出异常 | 用 `ExceptionOccurred()` 检查 |

## RPG 原生方法（Native Methods）

RPG 过程可以作为 Java Native Method 被 Java 调用：

```rpgle
**FREE
// 用 PCML 声明导出
// 编译为 *MODULE，再打包进 *PGM
DCL-PR RPG_NativeMethod CHAR(50);
    DCL-PI RPG_NativeMethod;
        input CHAR(100);
    END-PI;
END-PR;

DCL-PROC RPG_NativeMethod;
    DCL-PI RPG_NativeMethod CHAR(50);
        input CHAR(100);
    END-PI;
    RETURN 'Processed: ' + %TRIM(input);
END-PROC;
```

## 资源管理

```rpgle
// 告诉 Java 释放多个对象
callJavaMethod(jvm: 'freeObjectArray' : objArray : numObjects);

// 标记临时对象（自动释放）
callJavaMethod(jvm: 'makePermanent' : javaObj);
```

## 相关页面

- [[rpg-subprocedures]] — 子过程与原型
- [[ile-concepts]] — ILE 架构
- [[rpg-binding-strategies]] — 绑定策略
