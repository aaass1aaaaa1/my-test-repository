# 备援接口错误码与安装分支处置

适用场景：走 `workbuddy_marketplace_skill` 备援路径时（见 SKILL.md Step 3 / Step 6）。
默认路径（`search_and_install_skills`）的错误直接透传即可，不查本表。

## 安装前置分支（据 search 返回的状态字段）

| 前置状态 | 处置 |
|---|---|
| 未安装（`installed === false`） | 直接调用 install |
| 已安装且最新（`installed === true && updateAvailable === false`） | 告知「已是最新 vX，无需重复安装」，跳过 |
| 已安装且有更新（`installed === true && updateAvailable === true`） | 询问用户是否更新，同意后再 install |

## 错误码对照表

| error 前缀 | 含义 | 处置 |
|---|---|---|
| `[CONFIG_ERROR]` | 主进程未配置 endpoint | 提示「推荐市场暂未启用，可能需更新客户端」 |
| `[BUILTIN_MARKET_ERROR]` | 后端业务错误 | 原样展示 error |
| `[UNKNOWN_SKILL_ID]` | install 阶段该 skillId 已不存在 | **不重试同一 id**，重新 search 取新 id 再装 |
| `[DOWNLOAD_ERROR]` | 下载失败 | 提示「下载失败，可能网络或权限问题」 |
| `[EXTRACT_ERROR]` | 解压失败 | 提示「解压失败，可清理技能目录后重试」 |
| `[INSTALL_ERROR]` | 其他未知 | 原样展示 error |

## 通用规则

- `success: false` → 原样透传 `error` 并停止，不自行编造原因。
- `results: []` → 仅表示该检索词无命中，换词重试；全部落空才告知未找到。
