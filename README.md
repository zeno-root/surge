# Surge Rules

一组简洁、可读、可远程引用的 Surge 分流规则，覆盖 AI、Apple、中国大陆直连、常用海外服务与自定义 IP 规则。

这个仓库适合希望自己维护 Surge 策略组、但不想把所有域名规则都塞进主配置文件的人。规则按用途拆分，便于审阅、订阅和逐步调整。

## 特性

- **按场景拆分**：AI、Apple、Apple CN、中国大陆、代理服务和 IP 规则分别维护。
- **Surge 原生格式**：使用 `DOMAIN`、`DOMAIN-SUFFIX`、`DOMAIN-KEYWORD` 等 Surge 兼容规则。
- **远程引用友好**：每个 `.list` 文件都可以通过 GitHub Raw URL 在 Surge 中直接引用。
- **轻量可审计**：当前规则集保持小而清晰，方便按自己的网络环境增删。

## 规则列表

| 文件 | 用途 | 规则数量 |
| --- | --- | ---: |
| `rules/aigc.list` | AI 与大模型相关服务，例如 OpenAI、Claude、OpenRouter 等 | 9 |
| `rules/apple.list` | 建议走代理或独立策略的 Apple / iCloud 相关服务 | 12 |
| `rules/apple_cn.list` | 适合中国大陆直连的 Apple 服务 | 14 |
| `rules/china.list` | 中国大陆常用站点与服务 | 19 |
| `rules/proxy.list` | 常用海外服务、开发者工具、新闻、社交、流媒体等 | 115 |
| `rules/ip.list` | 自定义 IP / CIDR 规则预留文件 | 0 |

## 快速使用

在 Surge 配置文件中，通过 `RULE-SET` 引用本仓库的规则文件：

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/aigc.list,AI
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/apple_cn.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/apple.list,Apple
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/china.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/proxy.list,Proxy
RULE-SET,https://raw.githubusercontent.com/zeno-root/surge/main/rules/ip.list,Proxy
GEOIP,CN,DIRECT
FINAL,Proxy
```

其中 `AI`、`Apple`、`Proxy` 需要替换成你自己 Surge 配置里已经存在的策略组名称。如果你没有单独的 AI 或 Apple 策略组，也可以直接把它们改成 `Proxy`。

## 推荐策略顺序

规则顺序会影响最终命中结果。推荐按下面的优先级组织：

1. 更明确、更需要单独处理的服务优先，例如 `aigc.list`。
2. Apple 规则拆成 `apple_cn.list` 与 `apple.list`，先处理中国大陆直连项，再处理其他 Apple 服务。
3. 中国大陆常用服务走 `DIRECT`。
4. 海外常用服务走代理策略。
5. 最后使用 `GEOIP,CN,DIRECT` 与 `FINAL,Proxy` 兜底。

## 文件说明

### `aigc.list`

面向 AI 工具和大模型服务，适合绑定到单独的 AI 策略组，便于选择低延迟、稳定的节点。

### `apple_cn.list`

面向在中国大陆访问体验更好的 Apple 服务，例如 App Store、Apple 官网、天气、股票、更新 CDN 等，通常建议直连。

### `apple.list`

面向 iCloud、证书校验、CloudKit、Apple DNS 等服务，可根据自己的实际访问情况绑定到 `Apple`、`Proxy` 或 `DIRECT`。

### `china.list`

面向中国大陆常用服务和站点，通常建议直连。

### `proxy.list`

面向常见海外服务，包含 Google、YouTube、Microsoft、X / Twitter、Facebook、Netflix、开发者工具、新闻、社交、设计、支付、交易平台等。

### `ip.list`

预留给 IP-CIDR、IP-CIDR6 等规则。当前为空，适合放置你希望独立维护的 IP 段规则。

## 维护原则

- 只加入明确需要分流的域名，避免规则集无限膨胀。
- 优先使用稳定域名后缀，例如 `DOMAIN-SUFFIX`。
- 对容易误伤的规则谨慎使用 `DOMAIN-KEYWORD`。
- 同类服务放在同一注释分组下，方便审阅和回滚。
- 新增规则后建议在 Surge 的最近请求或规则匹配日志中验证命中情况。

## 贡献

欢迎提交 Issue 或 Pull Request。提交规则时建议说明：

- 需要新增或调整的域名。
- 该域名对应的服务或场景。
- 推荐走 `DIRECT`、`Proxy`、`AI` 还是其他策略组。
- 如果是修复误匹配，请提供具体影响。

## 免责声明

本项目仅提供个人维护的 Surge 分流规则参考。不同地区、网络运营商、节点质量和 Surge 配置都会影响实际效果，请根据自己的使用环境调整。
