---
title: 服务程序（Service Programs）
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, ile, service-programs]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
---

# 服务程序（Service Programs）

服务程序（`*SRVPGM`）是 ILE 中最重要的代码复用机制——多个模块打包为一个可共享的对象，类似于 Linux 的 `.so` 或 Windows 的 `.dll`。

## 为什么用服务程序

| 对比项 | 直接绑定模块 | 服务程序 |
|--------|------------|---------|
| 内存占用 | 每程序一份副本 | 多程序共享一份 |
| 更新逻辑 | 需重编所有调用程序 | 替换服务程序即可 |
| 加载速度 | 首次慢 | 首次慢，之后缓存 |
| 版本控制 | 绑定时锁定 | 通过签名管理 |

## 创建服务程序

### Step 1: 编写并编译模块

```rpgle
**FREE
// module: UTILSTR
DCL-PROC toUpper;
    DCL-PI toUpper VARCHAR(256);
        input VARCHAR(256) CONST;
    END-PI;
    DCL-S result VARCHAR(256);
    result = %UPPER(input);
    RETURN result;
END-PROC toUpper;
```

```bash
CRTRPGMOD MODULE(MYLIB/UTILSTR) SRCFILE(MYLIB/QRPGLESRC)
```

### Step 2: 编写 Binder Language 源

在 `QSRVSRC` 文件中创建 binder 语言成员：

```rpgle
*================================================================*
STRPGMEXP PGMLVL(*CURRENT) SIGNATURE('UTILSTR_V1')
  EXPORT SYMBOL('toUpper')
ENDPGMEXP
```

### Step 3: 创建服务程序

```bash
# 方式 A: 用 EXPORT(*SRCFILE)
CRTSRVPGM SRVPGM(MYLIB/UTILSTR) MODULE(*SRVPGM) +
          EXPORT(*SRCFILE) SRCFILE(MYLIB/QSRVSRC) SRCMBR(UTILSTR)

# 方式 B: 用绑定目录
CRTSRVPGM SRVPGM(MYLIB/UTILSTR) BNDDIR(MYLIB/UTILBND)
```

## 程序绑定到服务程序

### 方式 1: BNDSRVPGM 参数

```bash
CRTPGM PGM(MYLIB/MYAPP) MODULE(MYLIB/MAINMOD) +
      BNDSRVPGM(MYLIB/UTILSTR)
```

### 方式 2: 绑定目录

```bash
# 绑定目录包含服务程序
ADDBNDDIRE BNDDIR(MYLIB/APPBND) OBJ((*LIBL/UTILSTR))
```

### 方式 3: 原型 EXTPROC

```rpgle
DCL-PR toUpper VARCHAR(256) EXTPROC('UTILSTR/toUpper') END-PR;
```

## 更新服务程序

服务程序更新后，绑定到它的程序**无需重编**，运行时自动使用新版本：

```bash
# 重新编译模块
CRTRPGMOD MODULE(MYLIB/UTILSTR) SRCFILE(MYLIB/QRPGLESRC)

# 重建服务程序（不改变签名）
CRTSRVPGM SRVPGM(MYLIB/UTILSTR) MODULE(*SRVPGM) +
          EXPORT(*SRCFILE) SRCFILE(MYLIB/QSRVSRC) SRCMBR(UTILSTR)
```

如需改变导出接口，使用新签名：

```rpgle
STRPGMEXP PGMLVL(*CURRENT) SIGNATURE('UTILSTR_V2')
  EXPORT SYMBOL('toUpper')
  EXPORT SYMBOL('toLower')   // 新增导出
ENDPGMEXP
```

## 签名（Signature）机制

签名是服务程序暴露给外部的"接口版本"：

```
CRTPGM 时记录 → 运行时验证
调用方签名 === 服务程序签名 → 允许运行
调用方签名 !== 服务程序签名 → MCH3601 错误
```

### 管理多个签名

```rpgle
* 当前签名
STRPGMEXP PGMLVL(*CURRENT) SIGNATURE('UTIL_V2')

* 接受旧签名兼容性
STRPGMEXP PGMLVL(*PRV) SIGNATURE('UTIL_V1')
  EXPORT SYMBOL('toUpper')
ENDPGMEXP
```

## 调试服务程序

```bash
# 模块必须用 DBGVIEW(*LIST 或 *ALL) 编译
CRTRPGMOD MODULE(MYLIB/UTILSTR) SRCFILE(MYLIB/QSRVSRC) DBGVIEW(*LIST)

# 然后用 STRDBG
STRDBG PGM(MYLIB/MYAPP) UPDPROD(*YES)

# 设置断点
BREAK UTILSTR/toUpper
```

## 常见问题

- **MCH3601**：签名不匹配，检查 BNDSRVPGM 和服务程序版本
- **RNX0204**：找不到导出符号，检查 `EXPORT SYMBOL` 拼写
- **性能**：首次调用慢（冷启动），之后快（服务程序常驻内存）

## 相关页面

- [[ile-concepts]] — ILE 架构
- [[rpg-binding-strategies]] — 绑定策略
- [[rpg-subprocedures]] — 子过程与原型
- [[rpg-debugging]] — 调试
