---
title: IBM i API Concepts
type: concept
domain: ibm-i
tags: [ibm-i, api, programming-interface, ile]
summary: IBM i 7.5 应用程序编程接口（API）的核心概念、调用方式与架构分类
related:
  - ibm-i-api-reference
  - ibm-i-user-space
  - ibm-i-list-api
sources:
  - title: API overview and concepts (IBM i 7.5)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/apipdf.pdf
    filed: raw/papers/apipdf.txt
created: 2026-04-19
---

# IBM i API 概念

IBM i 应用程序编程接口（API）允许用高级语言程序调用 IBM i 操作系统的特定数据或函数。

## API 与 CL 命令的选择

| 维度 | API | CL 命令 |
|------|-----|---------|
| 用途 | 编程接口，供应用程序调用 | 交互式或 CL 程序中使用 |
| 性能 | 更快，系统开销更小 | 较慢，但功能完整 |
| 灵活性 | 通常提供更多信息 | 功能较为固定 |
| 场景 | 程序化、批量、系统级 | 日常交互管理任务 |

> IBM 不提供 API 来替代 CL 命令，两者共存互补。

## API 类型分类

### 1. Program-Based API（基于程序的 API）

最传统的 API 类型，调用时将参数压栈，由系统直接执行。

- **特点**：简单直接，参数通过调用栈传递
- **限制**：不支持指针参数（OPM 环境）
- **典型示例**：`QUSCRTUS`（创建用户空间）、`QUSRTVUS`（检索用户空间）

### 2. Service-Program-Based API（基于服务程序的 API）

绑定到服务程序（`*SRVPGM`），通过_exported_ 函数签名调用。

- **特点**：支持指针参数、可返回复杂数据结构
- **优势**：可使用更多现代特性，与 ILE 环境深度集成
- **典型示例**：`QsyListObjects`、`QsyCreateObject`

### 3. ILE Common Execution Environment（ILE CEE）API

ILE 语言（C、C++、RPG、COBOL）共享的系统级 API。

- **用途**：内存管理、字符串处理、日期时间、文件 I/O
- **包含于**：CEE.* 系列函数（如 `CEEBCEX`、`CEEDYW`）

### 4. UNIX-Type API（UNIX 类型 API）

遵循 POSIX 标准的文件系统与进程管理 API。

- **命名风格**：小写加下划线（如 `open()`、`read()`、`fork()`）
- **路径格式**：POSIX 风格（`/path/to/file`）
- **适用场景**：与 UNIX/Linux 互操作、流文件处理

## API 调用方式

### OPM RPG 调用 Program-Based API

```rpg
0001      C                   CALL      'QUSCRTUS'
0002      C                   PARM                    USPName  20
0003      C                   PARM      512          USPLen   10I 0
0004      C                   PARM      '*ALL'      USPAuth  10
0005      C                   PARM                    TEXT     50
0006      C                   PARM                    QUSEC    16
```

### ILE C 调用 Service-Program-Based API

```c
#include <qsyinc_h>
#include <qsyolib_h>

int main(int argc, char *argv[]) {
    Qsy_Obj_Handle_t handle;
    Qus_EC_t errcode = {sizeof(Qus_EC_t)};

    QSYOLIBP(&handle, "MYLIB", &errcode);
    if (errcode.Bytes_Available > 0) {
        // error handling
    }
    return 0;
}
```

### ILE RPG 调用 CEE API

```rpg
 DCEEFechaZone...
 D                 S               10I 0
 D tz              DS                  Like(CEE_DATE)
 D
 D                 CALLP     CEEUTCC(zonenbr: CEE_NULL_BASIC: CEE_NULL_BASIC)
```

## 核心概念

### User Space（`*USRSPC`）

用户空间是 IBM i 中用于存储任意用户定义信息的对象。所有 List API 通过 User Space 返回数据。

```rpg
 D USPCA0100     DS                  Based(pUSpace)
 D  USPNBRLE                   133  136B 0  Number List Entries
 D  USPSIZELE                   1    4B 0  Size Each Entry
 D  USPOFFSET                   5    8B 0  Offset List Data
```

### Receiver Variable（接收器变量）

用于接收 API 返回数据的变量，通常置于 User Space 中。

**两种操作模式**：
- **指针模式**：直接操作指针访问 User Space 内容
- **非指针模式**：通过偏移量（Offset）计算字段位置

```rpg
 // 非指针模式：通过偏移量访问字段
 c                   eval      pData = pUSpace + QUSOBA
 c                   eval      nbrEntries = QUSNBRLE
```

### Error Code Parameter（错误码参数）

所有 API 都支持可选的错误码参数（通常为 16 字节结构）：

| 字段 | 偏移 | 类型 | 说明 |
|------|------|------|------|
| `QUSBAVL` | 1-4 | Binary(4) | 可用字节数（0=无错） |
| `QUSBAVE` | 5-8 | Binary(4) | 已提供字节数 |
| `QUSEI` | 9-15 | Char(7) | 异常消息 ID |
| `QUSMID` | 16 | Char(1) | 保留 |

### List API 通用模式

List API 返回一个条目列表，处理流程：

1. 调用 List API，传入 User Space 名称和格式名称
2. 从 User Space 头部读取 `偏移量`、`条目数`、`条目长度`
3. 根据偏移量定位数据区起始位置
4. 循环读取每个条目

```rpg
 // 读取通用列表头
 c                   eval      pData = pUSpace + QUSOBA
 c                   eval      nbr = QUSNBRLE
 c                   eval      size = QUSSIZELE
 c
 c     *LOVAL         DOWLE     nbr
 c                   // 处理条目
 c                   eval      pData = pData + size
 c                   eval      nbr = nbr - 1
 c                   ENDDO
```

### Exit Program（退出程序）

Exit Program 机制允许用户将自己的程序插入系统流程：

| 操作 | API |
|------|-----|
| 注册退出点 | `QWVDEXR` |
| 添加退出程序 | `QWVDAXIT` |
| 检索退出信息 | `QUSREXI` |
| 删除退出程序 | `QWVDRXIT` |

## Generic Library Names（通用库名）

API 调用中可使用特殊值替代具体库名：

| 值 | 说明 |
|----|------|
| `*ALL` | 所有库，包括 QSYS |
| `*ALLUSR` | 所有用户库（Q 开头除外） |
| `*LIBL` | 当前库列表 |
| `*CURLIB` | 当前库 |

## API 信息格式

每条 API 描述包含：

1. **API 名** — 程序或服务程序名
2. **用途** — 功能描述
3. **格式（Format）** — 输入输出数据结构名
4. **参数表** — 每个参数的说明、类型、顺序
5. **错误代码** — 可能的异常条件
6. **示例** — 多语言调用示例

## 兼容性保证

IBM 承诺：
- 现有 API 的行为不变
- 新增参数放在末尾且为可选
- 新增数据结构会创建新格式（`_0200`, `_0300`）
- 列表 API 应使用 `偏移量` + `大小` + `条目数` + `条目长度` 来确保兼容未来版本

## 相关主题

- [[ibm-i-api-reference]] — API 分类与完整列表
- [[ibm-i-user-space]] — User Space 机制详解
- [[ibm-i-list-api]] — List API 处理模式
