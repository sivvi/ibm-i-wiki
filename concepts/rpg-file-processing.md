---
title: RPG 文件处理
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, file-processing]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG 文件处理

## 文件声明（DCL-F）

```rpgle
**FREE
// 简单顺序读
DCL-F CustomerDisk DISK(*INPUT);

// 带键的文件（Indexed/Keyed）
DCL-F CustomerFile KEYED INFLD(custId) USAGE(*INPUT);

// 多成员文件
DCL-F LogFile DISK(*OUTPUT) USAGE(*OUTPUT) OPTION(*EXTEND);

// 显示文件（Workstation）
DCL-F Screen E DISK EXTFILE('MYLIB/DSPF001') USAGE(*OUTPUT : *INPUT);

// 打印机文件
DCL-F Report PRINTER(132);
```

## 文件操作码

| 操作码 | 说明 | 示例 |
|--------|------|------|
| `OPEN` | 打开文件 | `OPEN CustomerFile;` |
| `CLOSE` | 关闭文件 | `CLOSE CustomerFile;` |
| `READ` | 顺序读下一条记录 | `READ CustomerFile;` |
| `READE` | 按键等于读 | `READE Key CustomerFile;` |
| `READP` | 倒读上一条记录 | `READP CustomerFile;` |
| `READPE` | 按键等于倒读 | `READPE Key CustomerFile;` |
| `CHAIN` | 随机读（键值） | `CHAIN (key) CustomerFile;` |
| `SETGT` | 读到大于键值的位置 | `SETGT Key CustomerFile;` |
| `SETLL` | 设置小于键值的位置 | `SETLL Key CustomerFile;` |
| `WRITE` | 写新记录 | `WRITE RecordFormat;` |
| `UPDATE` | 更新当前记录 | `UPDATE CustomerRecord;` |
| `DELETE` | 删除当前记录 | `DELETE CustomerRecord;` |
| `EXECUTE` | 执行 SQL | `EXECUTE 'SELECT...' ` |

## 顺序处理

```rpgle
DCL-F CustomerFile DISK(*INPUT) USAGE(*INPUT);
DCL-DS CustomerRecord EXTNAME('CUSTOMER') END-DS;

READ CustomerFile;
DOW NOT %EOF(CustomerFile);
    // 处理记录
    IF status = 'A';
        totalBalance += balance;
        count += 1;
    ENDIF;
    READ CustomerFile;
ENDDO;
```

## 键值随机访问

```rpgle
DCL-F CustomerFile KEYED INFLD(custId) USAGE(*INPUT:*OUTPUT:*DELETE);
DCL-DS CustomerRecord EXTNAME('CUSTOMER') END-DS;

custId = 'C001';
CHAIN (custId) CustomerRecord;
IF %FOUND(CustomerFile);
    balance = balance * 1.1;  // 加价 10%
    UPDATE CustomerRecord;
ELSE;
    // 未找到
ENDIF;
```

## 显示文件处理

```rpgle
DCL-F Display E DISK EXTFILE('MYLIB/MAINTF') USAGE(*OUTPUT : *INPUT : *UPDATE);

DCL-DS ScreenRec DIM(24);
    fld1 CHAR(10);
    fld2 CHAR(20);
END-DS;

EXFMT ScreenRec(1);  // 写并读屏幕记录 1
IF response = '03';
    *INLR = *ON;
ENDIF;
```

## 文件状态码

INFDS（信息数据结构）提供文件状态信息：

```rpgle
DCL-DS FileInfo STATIC;
    statusCode CHAR(5) POS(11);
    jobName CHAR(10) POS(12);
    pgmStatus CHAR(5) POS(1);
    fileName CHAR(10) POS(21);
    openStatus CHAR(5) POS(1);
END-DS;

DCL-F MyFile DISK STATIC INFDS(FileInfo);
```

常见状态码：
- `00000` — 正常
- `01211` — 记录未找到
- `01021` — 文件结束
- `01216/01217` — 打开/关闭失败

## 外部描述文件 vs 程序描述文件

### 外部描述（EXTNAME）

```rpgle
DCL-DS Customer EXTNAME('CUSTOMERFILE') END-DS;
// 使用文件中的字段名
Customer.Name = 'Acme';
Customer.Balance = 100;
```

### 程序描述（带格式）

```rpgle
IDCHAR1   E           CUSTOMER
I...........................................................
I          1  10CUSTNO
I         11  20NAME
I         21  35ADDR
```

## 多文件处理

```rpgle
DCL-F File1 DISK(*INPUT);
DCL-F File2 DISK(*INPUT);

READ File1;
READ File2;
DOW NOT %EOF(File1) AND NOT %EOF(File2);
    IF key1 < key2;
        processFile1();
        READ File1;
    ELSE;
        processFile2();
        READ File2;
    ENDIF;
ENDDO;
```

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数（%EOF, %FOUND, %OPEN）
- [[rpg-error-handling]] — 错误处理
- [[ibm-i-platform]] — IBM i 平台
