# AI Data Copilot

一个面向数据开发团队的 AI 数据质量与安全 SQL Copilot。

```text
表结构/样本数据 -> AI/规则引擎 -> 质量规则建议 -> 人工审核 -> 数据平台执行
自然语言/SQL -> 安全审查器 -> 只读查询边界 -> 下游分析
```

## 能力

- 根据表结构、样本行和业务描述生成质量规则建议
- 默认使用无需 API Key 的可重复启发式 Provider
- 可切换 OpenAI-compatible Provider
- SQL 只读、单语句、允许表范围和系统表检查
- Copilot 请求审计记录
- Prompt 版本化，便于评测和回归

## 启动

```bash
mvn test
mvn spring-boot:run
```

默认端口 `8081`，默认 Provider 为 `heuristic`。

使用模型 Provider 时通过环境变量配置：

```powershell
$env:AI_PROVIDER="openai"
$env:AI_API_KEY="your-key"
$env:AI_BASE_URL="https://api.openai.com/v1"
$env:AI_MODEL="gpt-4o-mini"
mvn spring-boot:run
```

不要把 API Key 提交到 GitHub。

## API

生成质量规则：

```json
POST /api/v1/copilot/suggest-rules
{
  "tableName": "orders",
  "columns": [
    {"name":"id","type":"BIGINT","nullable":false,"unique":true},
    {"name":"amount","type":"DECIMAL","nullable":false,"unique":false}
  ],
  "sampleRows": [{"id":1,"amount":99.9}],
  "businessDescription": "支付订单"
}
```

审查 SQL：

```json
POST /api/v1/sql/review
{"sql":"SELECT id, amount FROM orders WHERE amount > 0","allowedTables":["orders"]}
```

以下语句会被拒绝：

```sql
DELETE FROM orders;
SELECT * FROM information_schema.tables;
SELECT * FROM orders; DROP TABLE orders;
```

## 面试亮点

- Provider 抽象如何支持 OpenAI、Azure、通义、Ollama？
- AI 输出为什么还需要规则校验和人工审核？
- 如何防止 SQL 注入、越权表访问和多语句执行？
- 如何设计 Prompt 版本、离线评测集和回归测试？
- 如何记录模型成本、延迟、失败率和输出质量？

## Roadmap

- [x] 结构化质量规则建议
- [x] AI Provider 抽象和启发式兜底
- [x] 安全 SQL 审查器
- [x] 请求审计和 Prompt 版本
- [ ] 接入 data-platform-lite 数据目录
- [ ] JSON Schema 结构化输出校验
- [ ] 人工审核工作流和历史规则检索
- [ ] 模型成本、延迟和质量指标

## License

MIT
