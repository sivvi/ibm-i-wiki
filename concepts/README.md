# IBM i RPG Reference

IBM i RPG IV 文档，基于 IBM i 7.5 RPG Reference Manual (SC09-2509-09)。

## 文档结构

```
concepts/
├── rpg-iv-basics.md          # RPG IV 基础概念
├── rpg-iv-bifs.md            # 内置函数 (BIF) — 95 个
├── rpg-iv-data-types.md      # 数据类型
├── rpg-free-format.md        # 自由格式语法
├── rpg-control-specs.md      # 控制规范
├── rpg-definition-specs.md   # 定义规范
├── rpg-file-processing.md    # 文件处理
├── rpg-error-handling.md     # 错误处理
├── rpg-commitment-control.md  #  Commitment Control
├── rpg-debugging.md          # 调试
├── rpg-binding-strategies.md # 绑定策略
├── rpg-java-interop.md       # Java 互操作
├── dds-*.md                   # DDS 文件定义文档
├── ibm-i-*.md                 # IBM i API 相关
└── rpg-opcode-*.md           # 操作码文档 — 120+ 个
```

## 操作码 (Opcode) 文档

覆盖 IBM i RPG Reference v7.5 中的所有操作码，源文件为 `raw/papers/rpg-reference-7.5.txt`。

### 文件命名

- 大驼峰形式：`rpg-opcode-DO.md`、`rpg-opcode-CAT.md`
- 部分 opcode 带后缀：`rpg-opcode-ANDxx.md`、`rpg-opcode-CABxx.md`

### 已知问题

- `rpg-reference-7.5.txt` 中的 opcode 索引按字母排序时存在跳位（同一字母开头的 opcode 遗漏部分条目），已通过全文扫描补充缺失部分。
- 部分 opcode 存在子格式（P/H/F 版本），如 MOVE (P)、MOVE (H)，当前解析策略取主词条完整内容，子格式合并在内。
- Z-ADD、Z-SUB、XML-SAX 在 opcode 索引中未单独列出，但其正文部分有完整文档，已补充。

### 覆盖率

| 类别 | 数量 | 状态 |
|------|------|------|
| Opcode (索引) | 120 | ✅ 完整 |
| Opcode (额外) | 3 (Z-ADD/Z-SUB/XML-SAX) | ✅ 完整 |
| BIF | 95 | ✅ 完整 |

## 来源

- **RPG Reference**: `raw/papers/rpg-reference-7.5.txt` (SC09-2509-09, 595 pages)
- **BIF 参考**: `raw/papers/rpg-iv-bifs.md`
- **其他**: IBM i 7.5 Knowledge Center 及相关技术规范
