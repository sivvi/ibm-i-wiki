---
title: RPG 参数传递机制
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, subprocedures]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
---

# RPG 参数传递机制

## 传递方式对比

| 方式 | 修饰符 | 说明 | 性能 | 安全性 |
|------|--------|------|------|--------|
| **常量传递（CONST）** | `CONST` | 副本，只读 | 好（内部优化） | 高（防止意外修改） |
| **值传递（VALUE）** | `VALUE` | 副本，可修改（不影响原值） | 较差（复制） | 中 |
| **引用传递** | （默认） | 指针传递 | 好 | 低（可改原值） |

## 原型参数声明

```rpgle
**FREE
// 常量传递（推荐大多数场景）
DCL-PR processOrder INT(10) EXTPROC('ORDCALC');
    orderId CHAR(10) CONST;
    quantity PACKED(5:0) CONST;
    unitPrice PACKED(9:2) CONST;
END-PR;

// 值传递
DCL-PR incrementValue;
    DCL-PI incrementValue INT(10);
        value INT(10) VALUE;  // 副本，过程内修改不影响原变量
    END-PI;
    value += 1;
    RETURN value;
END-PR;
```

## CONST vs VALUE 的实际区别

```rpgle
**FREE
DCL-S counter INT(10) INZ(5);
DCL-PR incByConst INT(10);
    DCL-PI incByConst INT(10);
        n INT(10) CONST;
    END-PI;
    RETURN n + 1;
END-PR;

DCL-PR incByValue INT(10);
    DCL-PI incByValue INT(10);
        n INT(10) VALUE;
    END-PI;
    n += 1;      // 过程内修改
    RETURN n;    // 但这是副本，不影响外层 counter
END-PR;

Dsply %Char(incByConst(counter));   // 显示 6，counter 不变
Dsply %Char(incByValue(counter));   // 显示 6，counter 仍不变（因为是副本）
Dsply %Char(counter);               // 显示 5
```

## 省略参数（Omitting Parameters）

### *OMIT

传递 `*OMIT` 表示跳过某可选参数：

```rpgle
DCL-PR optionalProc;
    DCL-PI optionalProc;
        required CHAR(10);
        optional1 CHAR(10) OPTIONS(*OMIT);
        optional2 CHAR(10) OPTIONS(*OMIT);
    END-PI;
END-PR;

// 调用
optionalProc('REQ' : *OMIT : 'VAL');  // optional1 跳过，optional2='VAL'
```

### *NOPASS（不传递）

```rpgle
DCL-PR logMessage;
    DCL-PI logMessage;
        msg CHAR(255) CONST;
        level INT(10) CONST OPTIONS(*NOPASS);  // 可选
    END-PI;
END-PR;

// 调用
logMessage('Info message');              // 只传必需参数
logMessage('Warning' : 2);              // 传可选参数
```

### 检测实际传递的参数数量

```rpgle
DCL-PR flexibleProc;
    DCL-PI flexibleProc;
        p1 CHAR(10);
        p2 CHAR(10) OPTIONS(*NOPASS);
        p3 CHAR(10) OPTIONS(*NOPASS);
    END-PI;
END-PR;

DCL-S numParms INT(10);
numParms = %PARMS;           // 返回实际传递的参数数
IF numParms >= 2;
    // 处理 p2
ENDIF;
```

## 传递文件参数

RPG 过程可以接收文件对象作为参数：

```rpgle
DCL-PR processFile;
    DCL-PI processFile;
        filename CHAR(10) CONST;
    END-PI;
    DCL-F inputFile DISK(*INPUT) KEYED;
END-PR;
```

## 参数传递方向

| 用途 | RPG 关键字 | CL 方向 |
|------|-----------|---------|
| 输入 | `USAGE(*INPUT)` | `PARM` |
| 输出 | `USAGE(*OUTPUT)` | `PARM` |
| 输入/输出 | `USAGE(*INPUT:*OUTPUT)` | `PARM` |

## 过程接口（Procedure Interface）

在过程内部，PI 定义实际参数列表，与原型必须完全匹配：

```rpgle
DCL-PR myProc EXTPROC('MYLIB/MYSRV');
    DCL-PI myProc;
        inputData CHAR(100) CONST;
        outputData CHAR(100);
        returnCode INT(10);
    END-PI;
END-PR;

// 实现
DCL-PROC myProc;
    DCL-PI myProc;
        inputData CHAR(100) CONST;
        outputData CHAR(100);
        returnCode INT(10);
    END-PI;

    outputData = %UPPER(%TRIM(inputData));
    returnCode = 0;
END-PROC myProc;
```

## 常见错误

- `RNF5408`：原型参数类型/数量与调用不匹配
- `RNF5409`：PI 与 PR 参数不一致
- 参数拼写错误：大小写敏感

## 相关页面

- [[rpg-subprocedures]] — 子过程与原型
- [[ile-concepts]] — ILE 架构
- [[rpg-binding-strategies]] — 绑定策略
