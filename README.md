# assistant-skills

私有技能库：给 Grok / Codex / OpenClaw 以及通用助手共用。

## 目录

- `grok/` — Grok Bot 专用技能
- `codex/` — Codex 专用技能
- `openclaw/` — OpenClaw 专用技能
- `shared/` — 跨助手通用技能

每个技能一个子目录，建议至少包含 `SKILL.md`。

## 约定

- 一个技能一个文件夹（kebab-case）
- 密钥、订阅 token、cookie 不要提交；用环境变量或本地配置
