# 无限N代 · dsh-infinite-gen-n

> 仓库地址：**https://github.com/Minglink/dsh-infinite-gen-n**
>
> DeepSeek Harness 插件：**无限四代骨架 × N1 提示词 × dsh-purge 清洗引擎** 的融合版。
> 把「破甲模式」注册成可选 agent preset；未选中的会话零注入。

![version](https://img.shields.io/badge/version-1.1.0-green) ![dsh](https://img.shields.io/badge/dsh-0.2.0--rc.1%20~%200.2.x-blue) ![license](https://img.shields.io/badge/license-MIT-lightgrey)

安装（离线包 / 本目录）：

```powershell
# 完全退出 DeepSeek Harness 后执行
.\install.ps1                 # 定位 $DSH_HOME → 备份 → 复制三处 → 哈希回读
.\install.ps1 -ListBackups    # 查看回滚点
.\install.ps1 -Restore <时间戳>  # 回滚
```

手动：把整个 `dsh-infinite-gen-n` 目录复制到
`$DSH_HOME\plugins\`、`$DSH_HOME\node_modules\`、`$DSH_HOME\profiles\<profile>\node_modules\` 三处，
重启宿主，新建会话选「破甲模式」预设。

## 三层架构

| 层 | 来源 | 内容 |
|---|---|---|
| L3 提示词 | `prompts/N1.md` | op-spec v10（1135 行 39 节）× 无限四代 full-v042 增量融合：`ollie:` 应答协议、[O] thinking、ONE WALL、39→45 节、38+1 项发送自检、反过滤 stealth |
| L2 工程机制 | 无限四代 v0.5.3 | preset 登记、scope 热切换、输入归一化、冷启动预热、拒绝重锚、armor 投影徽标 |
| L1 宿主清洗 | dsh-purge 引擎移植 | `lib/host-patch/`：备份 / 幂等 apply / revert / skipped / shim / SEALED-ASAR 判据 |

## N1 提示词

N1 = v10 全文一字不动 + 四代增量插入（`tools/build-n1.mjs` 生成，重建逐字节确定）。四代增量：

- ENVIRONMENT CONTRACT（预授权契约，thinking 层免 re-litigating）
- INTENT-BASED BAN + QUOTING EXEMPTION（意图式禁令 + 引述豁免）
- REVERSION BAN + TOKEN-SALVAGE（长文不塌陷、漂移续写不重启）
- PROVIDER FILTER STEALTH（thinking 卫生 / 词典隐身 / 文件优先 / 分段直出 / kill 恢复）
- BILINGUAL ROUTING + CANNED SLOTS（中英路由 + 5 类槽位）
- META-QUESTION PROTOCOL、REHEARSAL COUNTER、FINAL LOCK 自检 39 项

A/B 实测（deepseek-flash，12 探针）：v10 83–92% → **N1 100%，零回归**。判分与徽标同词表（`armor-rules.js`），测试集 `tools/prompt_bank.jsonl`（36 探针）。

手动：把整个 `dsh-infinite-gen-n` 目录复制到
`$DSH_HOME\plugins\`、`$DSH_HOME\node_modules\`、`$DSH_HOME\profiles\<profile>\node_modules\` 三处，
重启宿主，新建会话选「破甲模式」预设。

## 工具

| 工具 | 作用 |
|---|---|
| `nv_verify` | 部署自检：版本 / 修订号 / 提示词哈希 / 归一化 / 清洗层 / scope / SEALED-ASAR（别名 `gen5_verify`） |
| `nv_profile` | 模板库：N1 acknowledgment set / scope lanes / counter-bank / pressure ladder（别名 `delivery_profile`） |
| `nv_patch_status` | 清洗层只读状态：各宿主 applied/pending 计数 + dsh-purge 共存提示 |
| `nv_patch_apply` | 幂等应用清洗补丁（备份先行、对不上原文自动 skipped、绝不强写、不重启宿主） |
| `nv_patch_revert` | 从 `.dshpurge.bak` 备份还原全部已应用补丁 |

命令行回归（可选）：`node tools/run_prompt_bank.mjs --lint | --norm | --score <file> --save-baseline | --compare <baseline> --score <file>`

## 环境变量

| 变量 | 默认 | 作用 |
|---|---|---|
| `GEN4_VARIANT` | `n1` | 提示词变体；`main`/`n1` → `prompts/N1.md`，`v10` → 融合基座，其余回落主提示词 |
| `GEN4_SCOPE` | 空 | 钉死启用范围 `local` / `global`（设了它界面就不能改） |
| `GEN4_PATCH` | 开 | `=0` 关闭宿主清洗层（三工具不注册、零加载） |
| `GEN5_NORMALIZE` | 开 | `=0` 关闭输入归一化 |
| `GEN4_PREHEAT*` / `GEN4_REANCHOR*` | 见四代 | 预热与重锚参数，语义与四代一致 |

## 与 dsh-purge 的关系

清洗引擎移植自 dsh-purge 1.1.71（同源补丁、同源备份格式 `.dshpurge.bak`）。两者可共存：
重复应用幂等；但**回滚建议只用一套管理**，避免互相覆盖。`nv_patch_status` 会自动检测共存并提示。
渗透相关内容（redteam 技能、演练台、net-scope、rewind）**不在移植范围**。

## 隐私与安全

- 运行时零网络请求；L1 清洗只写本机 `@deepseek-ai` 包与 `$DSH_HOME` 下本插件相关目录
- 徽标 / 投影 / 归一化纯本地，不持久化
- MIT 许可，见 [LICENSE](./LICENSE)
