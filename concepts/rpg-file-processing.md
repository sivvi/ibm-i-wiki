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

---

## OPEN（打开文件）

### 自由格式语法
```
OPEN{(E)} file-name
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| OPEN (E) | file-name | _ | _ | ER |

### 行为说明

显式 OPEN 操作打开由 file-name 操作数指定的文件。该文件不能被指定为主文件、副文件或表文件。

必须在文件描述规范上指定 **USROPN** 关键字，才能在模块或子过程中首次用显式 OPEN 打开该文件。如果文件已打开后再用 CLOSE 操作关闭，程序员可以用 OPEN 操作重新打开该文件，此时文件描述规范上不需要 USROPN 关键字。

如果文件在描述规范上未指定 USROPN，则全局文件在模块初始化时打开，局部文件在子过程初始化时打开。如果对已打开的文件再执行 OPEN 操作，会发生错误。

处理 OPEN 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## CLOSE（关闭文件）

### 自由格式语法
```
CLOSE{(E)} file-name | *ALL
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| CLOSE (E) | file-name or *ALL | _ | _ | ER |

### 行为说明

显式 CLOSE 操作关闭一个或多个文件或设备，并将其与模块断开。该文件在模块中不能再使用，除非对该文件指定显式 OPEN。对已关闭的文件再执行 CLOSE 操作不会产生错误。

- **file-name**：指定要关闭的文件名。
- **\*ALL**：一次关闭全局文件规范上定义的所有文件。在子过程中指定 CLOSE *ALL 对子过程中的局部文件没有任何影响。要关闭子过程中的所有局部文件，必须为每个文件编写单独的 CLOSE 操作。不能对数组或表文件（位置 18 中标有 T 的文件）指定此操作。

处理 CLOSE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## READ（读取记录）

### 自由格式语法
```
READ{(EN)} name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| READ (E N) | _ | name (file or record format) data-structure | _ | ER, EOF |

### 行为说明

READ 操作从全过程文件（full procedural file）中读取当前指针指向的记录。

**name 操作数**必须指定文件名或记录格式名。记录格式名仅适用于外部描述文件。如果 READ-by-format-name 操作接收到与 name 操作数中指定的格式不同的格式，操作将以错误结束。

**data-structure 操作数**：如果指定了此操作数，记录直接读入数据结果。
- 如果 name 引用程序描述文件，数据结构可以是与文件声明记录长度相同的任何数据结构。
- 如果 name 引用外部描述文件或记录格式，数据结构必须用 `EXTNAME(...:*INPUT or *ALL)` 或 `LIKEREC(...:*INPUT or *ALL)` 定义。

**记录锁**：如果从中读取的文件是更新磁盘文件，可以指定 **N 操作码扩展符**表示读取时不锁定记录。

**EOF 指示器**：可以指定位置 75-76 的指示器来指示 READ 操作是否遇到文件结束。也可以用 `%EOF` 内置函数，返回 '1' 表示 EOF 条件，'0' 表示否则。EOF 条件后必须重新定位文件才能继续处理。

**文件定位**：如果 READ 操作成功，文件定位在满足读取条件的下一条记录。如果有记录锁错误（状态 1218），文件仍定位在该锁定记录上，下一次读取操作将重试该记录。否则，如果发生任何其他错误或 EOF 条件，必须重新定位文件（使用 CHAIN、SETLL 或 SETGT 操作）。

处理 READ 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## READC（读取下一条变更记录）

### 自由格式语法
```
READC{(E)} record-name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| READC (E) | _ | record-name data-structure | _ | ER, EOF |

### 行为说明

READC 操作只能用于外部描述的 WORKSTN 文件，以获取子文件中下一条变更记录。**record-name 操作数**必须指定为子文件的记录格式名（由 DDS 中的 SFILE 关键字定义）。

对于多设备文件，数据从与程序设备关联的子文件记录中读取。

可以指定位置 75-76 的指示器，当子文件中没有更多变更记录时被置为 on。也可以用 `%EOF` 内置函数，返回 '1' 表示没有更多变更记录。

处理 READC 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## READE（按键相等读取）

### 自由格式语法
```
READE{(ENHMR)} search-arg | *KEY name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| READE (E N) | search-arg | name (file or record format) data-structure | _ | ER, EOF |

### 行为说明

READE 操作从全过程文件中检索下一条顺序记录，前提是记录的键与搜索参数匹配。如果记录的键与搜索参数不匹配，发生 EOF 条件，记录不会返回给程序。遇到文件结束时也会触发 EOF 条件。

**search-arg（搜索参数）** 可以是：
- 字段名、字面量、命名常量或虚常量
- KLIST 名称（适用于外部描述文件）
- 用括号括起来的键值列表
- `%KDS`：表示搜索参数是数据结构的子字段
- `*KEY`（或固定格式中省略）：如果下一条记录的完整键值等于当前记录的键值，则检索文件中的下一条记录

**NR 指示器**（位置 71-72）：可指定在搜索参数的键值大于文件中最高键值时被置为 on。也可通过 `%FOUND` 内置函数获取此信息。

**EOF 指示器**：指定位置 75-76 的指示器在发生 EOF 条件时被置为 on（即：未找到键值相等的记录，或遇到文件结束）。也可通过 `%EOF` 内置函数获取此信息。

**记录锁**：如果从中读取的文件是更新磁盘文件，可以指定 **N 操作码扩展符**表示读取时不锁定记录。

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 READE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## READP（向前读取）

### 自由格式语法
```
READP{(EN)} name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| READP (E N) | _ | name (file or record format) data-structure | _ | ER, BOF |

### 行为说明

READP 操作从全过程文件中读取上一条记录。

**name 操作数**必须指定文件名或记录格式名。如果指定了记录格式名，检索到的是指定类型的上一条记录，中间的记录被跳过。

**data-structure**：如果指定，记录直接读入数据结果。

**BOF 指示器**（位置 75-76）：可以指定在文件中没有上一条记录时（文件开始条件）被置为 on。也可通过 `%EOF` 内置函数获取此信息，返回 '1' 表示 BOF 条件。

**记录锁**：如果从中读取的文件是更新磁盘文件，可以指定 **N 操作码扩展符**表示读取时不锁定记录。

**文件重新定位**：如果发生记录锁错误（状态 1218），文件仍定位在该记录上。否则，如果发生任何其他错误或 BOF 条件，必须重新定位文件（使用 CHAIN、SETLL 或 SETGT 操作）。

处理 READP 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## READPE（向前读取键值相等的记录）

### 自由格式语法
```
READPE{(ENHMR)} search-arg | *KEY name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| READPE (E N) | search-arg | name (file or record format) data-structure | _ | ER, BOF |

### 行为说明

READPE 操作从全过程文件中检索上一条顺序记录，前提是记录的键与搜索参数匹配。如果记录的键与搜索参数不匹配，发生 BOF 条件，记录不会返回给程序。遇到文件开始时也会触发 BOF 条件。

**search-arg（搜索参数）** 可以是：
- 字段名、字面量、命名常量或虚常量
- KLIST 名称
- 用括号括起来的键值列表
- `%KDS`
- `*KEY`（或固定格式中省略）

**BOF 指示器**（位置 75-76）：可指定在发生 BOF 条件时被置为 on（即：未找到键值相等的记录，或遇到文件开始）。也可通过 `%EOF` 内置函数获取此信息。

**记录锁**：如果从中读取的文件是更新磁盘文件，可以指定 **N 操作码扩展符**表示读取时不锁定记录。

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 READPE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## CHAIN（随机检索）

### 自由格式语法
```
CHAIN{(ENHMR)} search-arg name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| CHAIN (E N) | search-arg | name (file or record format) data-structure | _ | NR, ER |

### 行为说明

CHAIN 操作从全过程文件中检索一条记录，将记录标识指示器置为 on（如果在输入规范上指定了的话），并将记录中的数据放入输入字段。

**search-arg（搜索参数）** 必须是用于检索记录的键或相对记录号：
- 按键访问时，search-arg 可以是字段名、命名常量、虚常量或字面量。对于外部描述文件，还可以是 KLIST 名、值列表或 `%KDS`。如果用 KLIST 指定键，键字段必须与文件中键的 CCSID 相同。
- 按相对记录号访问时，search-arg 必须是整数字面量或没有小数位的数值字段。

**name 操作数**：指定要读取的文件或记录格式名。如果用文件名并按键访问，检索与搜索参数匹配的第一条记录。如果用记录格式名并按键访问，检索指定记录类型中键与搜索参数匹配的第一条记录。

**data-structure**：如果指定，记录直接读入数据结果：
- 程序描述文件：可以是与文件声明记录长度相同的任何数据结构
- 外部描述文件：必须用 `EXTNAME(...:*INPUT or *ALL)` 或 `LIKEREC(...:*INPUT or *ALL)` 定义

**NR 指示器**（位置 71-72）：可指定在文件中没有记录的键与搜索参数匹配时被置为 on。也可通过 `%FOUND` 内置函数获取此信息，返回 '0' 表示未找到，'1' 表示找到。

**记录锁**：如果文件是更新磁盘文件且未指定 N 扩展符，所有记录都会被锁定。指定 **N 扩展符** 表示读取时不锁定记录。

**文件定位**：CHAIN 操作成功后，文件已定位，使得后续读取操作检索到逻辑上在该记录之后（或之前）的记录。CHAIN 操作未成功完成时（如发生错误或未找到记录），必须重新定位文件才能对该文件执行后续读取操作。

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 CHAIN 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## SETGT（定位到大于）

### 自由格式语法
```
SETGT{(EHMR)} search-arg name
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| SETGT (E) | search-arg | name (file or record format) | _ | NR, ER |

### 行为说明

SETGT 操作将文件定位到下一条键值或相对记录号**大于**指定搜索参数（search-arg）的记录。文件必须是全过程文件。

**search-arg（搜索参数）** 可以是键值、相对记录号，也可以是虚常量如 `*LOVAL`（定位到文件开头）和 `*HIVAL`（定位到文件末尾）。

**NR 指示器**（位置 71-72）：可指定在文件中没有记录的键值或相对记录号大于搜索参数时被置为 on。也可通过 `%FOUND` 内置函数获取此信息。

**特殊值**：
- `*START`：定位到文件开头，与键的排序顺序无关，与空值键字段无关
- `*END`：定位到文件结尾

**文件定位**：SETGT 操作后，文件定位在第一条键值或相对记录号大于搜索参数的记录之前。在读取文件之前，其他作业可能已从文件中删除了记录。

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 SETGT 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## SETLL（定位到大于或等于）

### 自由格式语法
```
SETLL{(EHMR)} search-arg name
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| SETGT (E) | search-arg | name (file or record format) | _ | NR, ER, EQ |

### 行为说明

SETLL 操作将文件定位到下一条键值或相对记录号**大于或等于**指定搜索参数（search-arg）的记录。文件必须是全过程文件。

**search-arg（搜索参数）** 可以是键值、相对记录号，也可以是虚常量如 `*LOVAL`、`*HIVAL`，或特殊值 `*START` 和 `*END`。

**NR 指示器**（位置 71-72）：可指定在搜索参数大于文件中最高键值时被置为 on。也可通过 `%FOUND` 内置函数获取此信息，返回 '0' 表示未找到记录，'1' 表示找到记录。

**EQ 指示器**（位置 75-76）：可指定在存在键值或相对记录号等于搜索参数的记录时被置为 on。也可通过 `%EQUAL` 内置函数获取此信息，返回 '1' 表示找到精确匹配。

**特殊值**：
- `*START`：定位到文件开头
- `*END`：定位到文件结尾

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 SETLL 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## WRITE（创建新记录）

### 自由格式语法
```
WRITE{(E)} name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| WRITE (E) | _ | name (file or record format) data-structure | _ | ER, EOF |

### 行为说明

WRITE 操作向文件写入一条新记录。**name 操作数**必须是程序描述文件的名称或外部描述文件的记录格式名。

**data-structure**：如果指定，记录直接从数据结构写入文件：
- 程序描述文件：必须指定数据结构，且可以是与文件声明记录长度相同的任何数据结构
- 外部描述记录格式：必须用 `EXTNAME(...:*OUTPUT or *ALL)` 或 `LIKEREC(...:*OUTPUT or *ALL)` 定义

**EOF 指示器**（位置 75-76）：可指定在文件已满时（子文件写入时）被置为 on。也可通过 `%EOF` 内置函数获取此信息。

**RECNO**：当使用相对记录号的记录写入文件时，必须更新文件规范上 RECNO 关键字指定的字段名，使其包含要写入记录的相对记录号。

**更新文件**：当用 WRITE 向具有输入或更新能力的 DISK 文件添加记录时，还必须将文件定义为具有输出能力（USAGE 关键字中指定 `*OUTPUT`）。

处理 WRITE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## UPDATE（修改现有记录）

### 自由格式语法
```
UPDATE{(E)} name {data-structure | %FIELDS(name{:name...})}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| UPDATE (E) | _ | name (file or record format) data-structure | _ | ER |

### 行为说明

UPDATE 操作修改从更新磁盘文件或子文件中检索到的最后一条锁定记录。在检索记录的执行输入操作和对文件的 UPDATE 操作之间，不应执行任何其他操作。

**name 操作数**：必须是文件或记录格式名。外部描述文件必须指定记录格式名，且必须是最后从文件读取的记录名；否则发生错误。

**data-structure**：如果指定，记录直接从数据结构更新：
- 程序描述文件：可以是与文件声明记录长度相同的任何数据结构
- 外部描述文件：必须用 `EXTNAME(...:*INPUT, *OUTPUT, or *ALL)` 或 `LIKEREC(...:*INPUT, *OUTPUT, or *ALL)` 定义
- 子文件记录格式：必须指定 `*OUTPUT` 或 `*ALL`

**%FIELDS**：可以使用 `%FIELDS` 指定要更新的字段列表。

**前置条件**：在发出 UPDATE 之前，必须对同一文件或记录发出带锁定的有效输入操作（READ、READC、READE、READP、READPE、CHAIN，或主/副文件）。

**连续更新**：对同一文件或记录的连续 UPDATE 操作无效。必须发出中间成功的读取操作来定位并锁定要更新的记录。

处理 UPDATE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## DELETE（删除记录）

### 自由格式语法
```
DELETE{(EHMR)} {search-arg} name
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| DELETE (E) | search-arg | name (file or record format) | _ | NR, ER |

### 行为说明

DELETE 操作从数据库文件中删除记录。文件必须是具有删除能力的文件（通过在自由格式定义的 USAGE 关键字中指定 `*DELETE`，或在固定格式文件描述规范的位置 17 中指定 U）。被删除的记录永远无法检索。

**不带 search-arg**：删除当前记录（最后检索到的记录）。该记录必须已被之前的输入操作锁定。

**带 search-arg**：按键或相对记录号指定要删除的记录。search-arg 可以是：
- 字段名、命名常量、虚常量或字面量
- KLIST 名（适用于外部描述文件）
- 值列表或 `%KDS`
- 相对记录号：整数字面量或无小数位的数值字段

**NR 指示器**（位置 71-72）：当指定了 search-arg 时，可指定在要删除的记录未找到时被置为 on。也可通过 `%FOUND` 内置函数获取此信息。

**操作码扩展符 H/M/R**：仅在搜索参数是列表或 `%KDS()` 时才允许。

处理 DELETE 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## EXFMT（写然后读格式）

### 自由格式语法
```
EXFMT{(E)} format-name {data-structure}
```

### 固定格式规范

| 代码 | Factor 1 | Factor 2 | 结果字段 | 指示器 |
|------|----------|----------|---------|--------|
| EXFMT (E) | _ | format-name data-structure | _ | ER |

### 行为说明

EXFMT 操作是向同一记录格式执行 WRITE 然后执行 READ 的组合。EXFMT 仅对定义为全过程组合文件的外部描述 WORKSTN 文件有效。

**format-name 操作数**：必须指定要写入然后读取的记录格式名。

**data-structure**：如果指定，记录从数据结构中写出并读入数据结构。数据结构必须用 `EXTNAME(...:*ALL)` 或 `LIKEREC(...:*ALL)` 定义。

**错误处理**：发生错误时，读取部分不处理（记录标识指示器和字段不被修改）。

处理 EXFMT 异常（文件状态码大于 1000）：指定操作码扩展符 'E' 或错误指示器 ER，不能同时指定两者。

---

## 操作码扩展符汇总

| 扩展符 | 含义 |
|--------|------|
| `E` | 启用错误处理。错误时 `%ERROR` 返回 '1'，或错误指示器（ER）被置为 on |
| `N` | 不锁定记录（用于更新磁盘文件） |
| `H` | 半锁定（half lock），用于键列表 |
| `M` | 匹配键（matching keys），用于键列表 |
| `R` | 相对记录号锁定，用于键列表 |

**H/M/R 扩展符** 仅在搜索参数是值列表或 `%KDS()` 时才允许。

---

## 键值查找与文件定位操作对比

| 操作 | 定位方式 | 是否读取记录 | 精度匹配 | 适用场景 |
|------|---------|-------------|---------|---------|
| CHAIN | 按键或相对记录号直接定位 | 是 | 必须相等（找不到则 %FOUND='0'） | 已知键值，精确查找单条记录 |
| SETGT | 定位到键值大于搜索参数的位置 | 否（只定位） | 大于 | 结合 READ 读取多条大于键值的记录 |
| SETLL | 定位到键值大于或等于搜索参数的位置 | 否（只定位） | 大于或等于 | 结合 READ 读取多条大于等于键值的记录 |
| READE | 从当前位置顺序查找相等的键 | 是 | 必须相等（不等则 EOF） | 读取所有键值相等的记录 |
| READPE | 从当前位置向前查找相等的键 | 是 | 必须相等（不等则 BOF） | 向前方向读取所有键值相等的记录 |

---

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
