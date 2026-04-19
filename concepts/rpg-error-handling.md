---
title: RPG 错误处理与例外
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, error-handling]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG 错误处理与例外

## 状态码（Status Codes）

### 文件状态码（INFDS）

记录在信息数据结构的第 1-5 位（POS(1:5)）：

```rpgle
DCL-DS MyFile_Info STATIC;
    statusCode CHAR(5) POS(1);
    jobName CHAR(10) POS(12);
    runID CHAR(10) POS(22);
END-DS;

DCL-F MyFile DISK INFDS(MyFile_Info);
```

常见文件状态码：
- `00000` — 正常完成
- `00001` — 记录已添加到文件但未写入磁盘
- `00002` — 部分字段被截断
- `01211` — CHAIN/READ 未找到记录
- `01021` — 文件结束
- `01216` — 文件打开失败
- `01217` — 文件关闭失败
- `01299` — 其他文件错误

### 程序状态码（PSDDS）

```rpgle
DCL-DS PgmStatus STATIC;
    pgmName CHAR(10) POS(1);
    statusCode CHAR(5) POS(11);
    pgmLib CHAR(10) POS(16);
    exceptionType CHAR(10) POS(26);
    exceptionNumber CHAR(4) POS(37);
END-DS;
```

## 指标错误处理

### *STATUS 指示器

```rpgle
// 在文件声明中使用
DCL-F CustomerFile DISK(*INPUT) USAGE(*INPUT) INFSR(FileError);

DCL-DS FileError;
    status CHAR(5) POS(1);
END-DS;

SUBSRP FileError;
    IF status = '01211';
        // 记录未找到
        *IN50 = *ON;
    ELSEIF status > '00999';
        // 严重错误
        *IN99 = *ON;
    ENDIF;
ENDSR;
```

## E 操作码修饰符

```rpgle
// E = 错误捕获，不置指标但设置 %ERROR
CHAIN (key) File E;
IF %ERROR;
    // 处理错误
ENDIF;

// 普通
CHAIN (key) File;
IF NOT %FOUND;
    // 未找到
ENDIF;
```

## 文件异常/错误子例程（INFSR）

```rpgle
DCL-F MyFile DISK INFSR(FileError);

SUBSRP FileError;
    DCL-S status CHAR(5) POS(1);
    IF status = '01216';
        // 文件未找到
    ELSEIF status = '01211';
        // 记录未找到
    ELSE;
        // 其他错误
    ENDIF;
ENDSR;
```

## 监视器（MONITOR）

```rpgle
**FREE
MONITOR;
    result = 100 / divisor;
    WRITE MyFile;
ON-ERROR;
    // 捕获任何错误
    errCode = %STATUS;
    errMsg = 'Error occurred';
ON-ERROR(1211:1211);  // 特定状态码
    // 只捕获 1211
END-MONITOR;
```

## FOR-EACH 错误处理

```rpgle
DCL-S item VARCHAR(100);
DCL-S items VARCHAR(500) = 'a,b,c';
DCL-S parsed VARCHAR(100) DIM(*) AUTO_ALLOC;

parsed = %SPLIT(items : ',');
FOR-EACH item IN parsed;
    IF item = *BLANKS;
        ITER;  // 跳过空项
    ENDIF;
    // 处理 item
ENDFOR;
```

## 例外消息

### SND-MSG（发送消息）

```rpgle
DCL-S msgId CHAR(7);
DCL-S msgData CHAR(50);

msgId = 'CPF9897';
msgData = '严重错误发生';
SND-MSG MSGID(msgId) MSGF(QCPFMSG) MSGDTA(%CHAR(msgData)) TOQ(*SAMEQ);
```

### SND-USRSPC（发送用户空间消息）

用于与系统日志集成。

## %STATUS BIF

```rpgle
DCL-S lastStatus INT(5);
lastStatus = %STATUS(MyFile);     // 文件状态
lastStatus = %STATUS(*PROGRAM);   // 程序状态
```

## 最佳实践

1. **始终检查 %EOF / %FOUND** — 不要假设计录存在
2. **使用 MONITOR 包装外部调用** — 防止异常扩散
3. **INFDS 记录详细信息** — 便于诊断
4. **不要用指标做业务逻辑** — 用 IND 类型变量
5. **设置 LR 之前先关闭文件** — 避免资源泄漏

## 相关页面

- [[rpg-iv-bifs]] — 内置函数（%STATUS, %ERROR）
- [[rpg-file-processing]] — 文件处理
- [[rpg-subprocedures]] — 子过程
- [[ile-concepts]] — ILE 架构
