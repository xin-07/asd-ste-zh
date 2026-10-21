# asd-ste-zh

把含糊、啰嗦、容易被误读的**中文技术文本**改写成受控中文的 agent skill。

> 一句一动作、主动语态、一词一义、去虚动词 / 套话 / 营销形容词 / 翻译腔。

适用于：工具描述、报错信息、agent 间指令、system prompt、状态汇报、README、PR 描述、日志，以及给领导或同事看的正式文书。目标读者是「没有追问机会的读者」——另一个 agent、翻译管线、非母语读者、半年后的自己。

## 合规声明

本规范**不是** ASD-STE100 官方标准的翻译。ASD-STE100 没有官方中文版，其正文与词表受版权保护，不可自由再分发。

本仓库只复述其**公开描述的规则类属**，并按中文的失败模式（虚动词、套话、同义轮换、分号串联、翻译腔）重新组织。仓库不含 ASD-STE100 的正文，也不含其受控词表。需要航空级认证文档时，请去 [asd-ste100.org](https://www.asd-ste100.org/) 申请官方英文原文。

## 安装

把 `SKILL.md`、`evals/`、`scripts/` 三个条目复制到你的 agent skills 目录：

```text
~/.agents/skills/asd-ste-zh/          # OpenCode / 通用
~/.claude/skills/asd-ste-zh/          # Claude Code
```

或直接克隆后复制：

```bash
git clone https://github.com/xin-07/asd-ste-zh.git
cp -r asd-ste-zh ~/.agents/skills/asd-ste-zh
```

装好后无需配置。说「这段话很绕，帮我改清楚」或「给这段报错消歧义」即可自动触发。

## 用法

默认只输出改写后的正文：

```text
把下面这段话翻一下让它不容易被别的 agent 误读：

基于当前配置的情况下，对于超时的请求而言，系统会进行重试操作。
```

输出：

```text
配置里超时的请求会被重试。
```

要求看 diff 时，输出「违反的规则 / 原文 / 改写后」三列表。

### 两档模式

- **Strict**：agent 间指令、system prompt、报错——目标读者是机器或解析管线。
- **中文-顺**：状态汇报、README、周报——目标读者是人，保留必要的可读性。

档位留在内部，用户要规则表时才说明。

## 机械首查

改写完成后跑捆绑的 linter（仅 Python 标准库，3.8+）：

```bash
python scripts/ste-lint-zh.py <文件>
python scripts/ste-lint-zh.py --selftest   # 自检
```

硬违规（分号、长句、名词化、营销形容词、套话、同义轮换）超阈值退出码 1；建议类（被动语态、破折号等）只报告。linter 永不标记情态词——置信度是内容，不是风格。

## 评测结果

用 [skill-creator](https://github.com/anthropics/skill-creator) 的 eval 循环验证，3 个用例（工具描述消歧义 / agent 指令改写 / README 去营销话）× 14 条断言：

| 配置 | 断言通过率 |
|---|---|
| 有 skill | **14/14（100%）** |
| 无 skill（裸模型） | 7/14（50%） |

基线的典型失败：编造原文没有的规则参数、残留营销形容词、分号串联、名词化。

description 触发优化：20 条评测集（10 正例 + 10 近失配负例）盲评 **20/20**——正例全触发、负例零误触发。

`evals/evals.json` 是评测用例与断言定义，改 skill 后可重跑回归。

## 目录结构

```text
asd-ste-zh/
├── SKILL.md                  # skill 主体（frontmatter 触发描述 + 规范全文 + 前后对照附录）
├── 受控中文技术写作规范.md     # 规范源文档（给人读的版本，与 SKILL.md 正文同源）
├── evals/
│   └── evals.json            # 3 个评测用例 + 14 条断言
└── scripts/
    ├── ste-lint-zh.py        # 机械检查器（仅标准库）
    └── LICENSE-ste-lint-zh.txt  # linter 的 MIT 许可证
```

## 致谢

- 规则类属与英文版参考：[danyuchn/asd-ste100-skill](https://github.com/danyuchn/asd-ste100-skill)（MIT）
- linter 源自：[lemonhall/asd-ste100-skill-zh](https://github.com/lemonhall/asd-ste100-skill-zh)（MIT，见 `scripts/LICENSE-ste-lint-zh.txt`）
- 评测流程：[anthropics/skill-creator](https://github.com/anthropics/skill-creator)

## License

本仓库新增内容（SKILL.md、规范源文档、README、evals）以 [MIT](LICENSE) 发布。`scripts/ste-lint-zh.py` 保留其原 MIT 许可证与版权声明。
