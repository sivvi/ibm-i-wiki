---
title: IBM i 7.5 RPG 新特性
created: 2026-04-19
updated: 2026-04-19
type: comparison
tags: [ibm-i, rpg, comparison]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# IBM i 7.5 RPG 新特性

基于 ILE RPG Reference for IBM i 7.5 的变更汇总。

## 版本对比

| 特性 | 7.3/7.4 | 7.5 |
|------|---------|-----|
| `%SPLIT *ALLSEP` | PTF backport | 原生支持 |
| 自然字符处理 | PTF backport | 原生支持 |
| 枚举增强 | 支持 | 增强 |
| JSON BIF | `%JSON` | `%JSON` 增强 |

## 7.5 关键新特性

### 1. %SPLIT 支持 *ALLSEP 选项

默认情况下，%SPLIT 忽略连续的多个分隔符。`*ALLSEP` 让每个分隔符都产生一个子串：

```rpgle
DCL-S parts VARCHAR(50) DIM(*) AUTO_ALLOC;
DCL-S input VARCHAR(50);

input = ',a,,b,c,';
parts = %SPLIT(input : ',' : *ALLSEP);
// 结果: {'' : 'a' : '' : 'b' : 'c' : ''}
```

### 2. 自然字符处理（CHARCOUNT NATURAL）

按**字符**而非字节处理字符串，支持 UTF-8、UCS-2 等变长编码：

```rpgle
// 控制级别关键字
Ctl-opt CHARCOUNTTYPES(*NATURAL : *UTF8 : *UCS2);

// 或文件/计算级别指令
/CHARCOUNT NATURAL
```

### 3. 枚举增强

```rpgle
DCL-ENUM sizes;
   tiny -1;
   small 0;
   medium 1;
   large 2;
END-ENUM;

DCL-ENUM jobMsgQ QUALIFIED;
   noWrap '*NOWRAP';
   wrap '*WRAP';
   prtWrap '*PRTWRAP';
END-ENUM;

// IN 操作符
IF size NOT IN sizes;
    SND-MSG *ESCAPE 'Invalid size';
ENDIF;

// FOR-EACH 迭代
FOR-EACH s IN sizes;
    // s 遍历每个枚举值
ENDFOR;
```

### 4. 程序状态码增强

%STATUS 返回更精确的状态码。

### 5. SQL RPG 增强

- `CRTSQLRPGI` 支持更长行长度
- `PPMINOUTLN` 选项可处理更长的输入文件

## 各版本关键里程碑

### V5R1 — 自由格式 RPG 诞生
- `/FREE ... /END-FREE` 块

### V5R4 — ILE 成熟
- 服务程序成为主流

### V7R1 — 现代数据类型
- 枚举类型
- `%JSON` BIF

### V7R3 — 开发生态
- `*NATURAL` 字符处理
- 更强的 BIFs

### V7R5 — 最新
- `%SPLIT *ALLSEP`
- 自然字符处理原生支持

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[rpg-free-format]] — Free format RPG
- [[ibm-i-platform]] — IBM i 平台
