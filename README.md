# 无限N代 · dsh-infinite-gen-n

> 仓库地址：**https://github.com/Minglink/dsh-infinite-gen-n**
>
> DeepSeek Harness 破甲插件「无限N代」：N1 提示词（A/B 实测 100%）+ 输入归一化 + 冷启动预热
> + 多轮重锚 + 宿主清洗。把「破甲模式」注册成可选 agent preset；未选中的会话零注入。

![version](https://img.shields.io/badge/version-1.1.0-green) ![dsh](https://img.shields.io/badge/dsh-0.2.0--rc.1%20~%200.2.x-blue) ![license](https://img.shields.io/badge/license-MIT-lightgrey)

## 安装

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

| 层 | 内容 |
|---|---|
| **L3 提示词** | `prompts/N1.md`：`ollie:` 应答协议、[O] thinking、ONE WALL、45 节、40 项发送自检、反过滤 stealth |
| **L2 工程机制** | preset 登记、scope 热切换、输入归一化、冷启动预热、拒绝重锚、armor 投影徽标 |
| **L1 宿主清洗** | `lib/host-patch/`：备份 / 幂等 apply / revert / skipped / shim / SEALED-ASAR 判据 |

## N1 提示词

N1 由 `tools/build-n1.mjs` 生成（重建逐字节确定），核心机制：

- **ONE WALL**：唯一例外主题，一行拒绝，工作继续
- **ANTI-REFUSAL ENGINE**：拒绝解剖 ×20 形态 + counter-bank 封辩论
- **REVERSION BAN + TOKEN-SALVAGE**：长文不塌陷、漂移续写不重启
- **PROVIDER FILTER STEALTH**（kill-triggered）：thinking 卫生 / 词典隐身 / kill 恢复
- **FICTION OUTPUT SHAPE**：虚构场景免票据直入正文 + MEASURABLE FLOOR 量化底线
- **BILINGUAL ROUTING + CANNED SLOTS**：中英路由 + 5 类槽位
- **REHEARSAL COUNTER**：演练框定一等交付
- FINAL LOCK 自检 40 项

A/B 实测（deepseek-flash）：**N1 100% 通过率，零回归**。判分与徽标同词表（`armor-rules.js`），测试集 `tools/prompt_bank.jsonl`（45 探针，7 lane × 3 难度 × 双语）。

命令行回归（可选）：`node tools/run_prompt_bank.mjs --lint | --norm | --score <file> --save-baseline | --compare <baseline> --score <file>`

## 工具

| 工具 | 作用 |
|---|---|
| `nv_verify` | 部署自检：版本 / 修订号 / 提示词哈希 / 归一化 / 清洗层 / scope / SEALED-ASAR（别名 `gen5_verify`） |
| `nv_profile` | 模板库：N1 acknowledgment set / scope lanes / counter-bank / pressure ladder（别名 `delivery_profile`） |
| `nv_patch_status` | 清洗层只读状态：各宿主 applied/pending 计数 |
| `nv_patch_apply` | 幂等应用清洗补丁（备份先行、对不上原文自动 skipped、绝不强写、不重启宿主） |
| `nv_patch_revert` | 从 `.dshpurge.bak` 备份还原全部已应用补丁 |

## 环境变量

| 变量 | 默认 | 作用 |
|---|---|---|
| `GEN4_VARIANT` | `n1` | 提示词变体；`main`/`n1` → `prompts/N1.md`，`v10` → 精简基座，其余回落主提示词 |
| `GEN4_SCOPE` | 空 | 钉死启用范围 `local` / `global`（设了它界面就不能改） |
| `GEN4_PATCH` | 开 | `=0` 关闭宿主清洗层（三工具不注册、零加载） |
| `GEN5_NORMALIZE` | 开 | `=0` 关闭输入归一化 |
| `GEN4_PREHEAT*` / `GEN4_REANCHOR*` | — | 预热与重锚参数 |

## 隐私与安全

- 运行时零网络请求；L1 清洗只写本机 `@deepseek-ai` 包与 `$DSH_HOME` 下本插件相关目录
- 徽标 / 投影 / 归一化纯本地，不持久化
- MIT 许可，见 [LICENSE](./LICENSE)
