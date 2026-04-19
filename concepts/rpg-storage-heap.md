---
title: RPG Storage and Heap Management
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, heap, storage, pointers, dynamic-memory, alloc]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
related:
  - rpg-iv-data-types
  - rpg-subprocedures
  - rpg-error-handling
---

# RPG 动态存储与堆管理（Heap）

RPG 支持动态分配堆存储（heap storage），用于处理大小在编译时未知的数组、链表等数据结构。所有动态存储操作都通过指针进行。

## 堆（Heap）基础

堆是用于动态分配存储的内存区域。程序可以请求任意大小的存储块，使用完毕后再释放。

**两种堆：**
- **默认堆（Default Heap）**：激活组启动时自动创建，同一激活组内所有程序共享
- **用户堆（User-Created Heap）**：通过 ILE API 创建，用于隔离特定程序的动态存储

## 核心操作

### %ALLOC — 分配存储

```rpgle
pointer = %ALLOC(size);
```

在**默认堆**中分配 `size` 字节，返回指向新存储的指针。新存储内容**未初始化**。

参数必须是零小数位的非浮点数。最大大小：
- 使用 teraspace 堆：`4,294,967,295` 字节
- 否则：`16,776,704` 字节

### %REALLOC — 重新分配

```rpgle
pointer = %REALLOC(pointer : newSize);
```

改变已有存储块的大小。数据尽可能保留。返回新指针。

### DEALLOC — 释放存储

```rpgle
DEALLOC pointer;
DEALLOC(N) pointer;  // No action if pointer is *NULL
```

释放之前通过 %ALLOC 或 %REALLOC 分配的存储。**指针操作后，原指针变为悬空指针，不应再使用。**

> `DEALLOC` 和 `REALLOC` 可用于默认堆和用户堆。

### ALLOC / REALLOC 操作码（固定格式）

```rpgle
C                   ALLOC     elemSize      ptr
C                   DEALLOC                 ptr
C                   REALLOC   newSize       ptr
```

## 动态数组示例：链表

```rpgle
 // 链表元素结构
D elem            DS                  BASED(elem@)
D   name@                           *
D   next@                           *
D   name_len                     5U 0
D elemSize        C                   %SIZE(elem)
D first           DS
D                                 *   INZ(*NULL)
D                                 *   INZ(*NULL)
D                                5U 0 INZ(0)
D elem@           S               *   INZ(%ADDR(first))

 // 添加节点
D AddName         PI
D   name                        40A
D newPtr          S               *
 /FREE
 ALLOC elemSize    newPtr;
 elem@ = newPtr;
 EVAL      next@ = *NULL;
 EVAL      name_len = %LEN(%TRIMR(name));
 ALLOC     name_len  name@;
 EVAL      %SUBST(nameVal:1:name_len) = name;
 /END-FREE

 // 遍历链表
D Display         PI
 /FREE
 elem@ = %ADDR(first);
 DOW next@ <> *NULL;
   elem@ = next@;
   // 处理 nameVal ...
   LEAVE;
 ENDDO;
 /END-FREE

 // 释放链表
D Free            PI
D prv@            S               *
 /FREE
 elem@ = %ADDR(first);
 elem@ = next@;
 DOW elem@ <> *NULL;
   DEALLOC name@;
   prv@ = elem@;
   elem@ = next@;
   DEALLOC prv@;
 ENDDO;
 /END-FREE
```

## 动态数组示例：可扩展数组

```rpgle
D arrInfo         DS                  QUALIFIED
D   pArr                          *   INZ(*NULL)
D   numElems                    10I 0 INZ(0)
D   numAlloc                    10I 0 INZ(0)
D arr             S             20A   VARYING DIM(32767)
D                                     BASED(arrInfo.pArr)
 /FREE
 // 初始分配
arrInfo.numAlloc = 2;
arrInfo.pArr = %ALLOC(arrInfo.numAlloc * %SIZE(arr));

// 添加元素
IF arrInfo.numAlloc < arrInfo.numElems + 2;
  arrInfo.numAlloc += 10;
  arrInfo.pArr = %REALLOC(arrInfo.pArr
                         : arrInfo.numAlloc * %SIZE(arr));
ENDIF;
arrInfo.numElems += 1;
arr(arrInfo.numElems) = 'XYZ Electronics';

// 搜索（使用 %LOOKUP）
i = %LOOKUP('XYZ' : arr : 1 : arrInfo.numElems);

// 排序
SORTA %SUBARR(arr : 1 : arrInfo.numElems);

// 释放
DEALLOC arrInfo.pArr;
RESET arrInfo;
 /END-FREE
```

## 用户堆（User-Created Heap）

通过 ILE Bindable API 管理独立堆，实现存储隔离：

```rpgle
D CEECRHP        PR                  EXTPROC('CEECRHP')
D   heapId                      10U 0
D   size                        10U 0 VALUE
D   options                      4A
D CEEGTST        PR                  EXTPROC('CEEGTST')
D   storage                        *   procptr
D   size                        10U 0 VALUE
D   heapId                      10U 0 VALUE
D CEEDSHP        PR                  EXTPROC('CEEDSHP')
D   heapId                      10U 0 VALUE
```

- `CEECRHP`：创建堆
- `CEEGTST`：从指定堆分配
- `CEEDSHP`：销毁堆（释放所有关联存储）

## 堆存储常见错误

> **释放后访问（Use-After-Free）**

```rpgle
Ptr1 = %ALLOC(25);
DEALLOC Ptr1;
// 此后 Fld1 不应再访问——存储已归还堆
Fld1 = *ALL'a';   // 危险！该内存可能被其他分配重用
```

**其他危险模式：**
- 复制指针后原指针在释放/重分配前使用
- 将指针作为参数传递（被调用方可能释放）
- 在 `*INZSR` 中设置指针后用 `RESET` 重置
- **丢失指针**（覆盖或清零后无法释放）——存储直到激活组结束才会回收

## 默认堆的行为

- 同一激活组中所有程序共享默认堆
- 一个程序越界写入可能破坏另一个程序的数据
- 默认堆在激活组结束时自动销毁

## 相关 BIF

- `%ALLOC` — 分配堆存储
- `%REALLOC` — 重新分配堆存储
- `%ADDR` — 获取变量地址
- `%SIZE` — 获取类型大小
- `%SUBARR` — 子数组操作

## 相关页面

- [[rpg-iv-data-types]] — 数据类型与指针
- [[rpg-subprocedures]] — 子过程
- [[rpg-error-handling]] — 错误处理
