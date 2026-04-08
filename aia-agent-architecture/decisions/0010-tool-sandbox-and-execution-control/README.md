---
title: "0010 工具沙盒与执行控制 - 对比材料"
status: draft
---

# 本目录用途

本目录用于补充 `0010-tool-sandbox-and-execution-control.md` 顶层 ADR 中的设计思路，沉淀更细粒度的实现方案与对比材料。例如：

- 不同工具类型（shell、数据库、HTTP、内部微服务）的沙盒实现模式。
- 不同隔离级别（进程级、容器级、VM 级、serverless）的优缺点与选型建议。
- 针对高风险操作的审批流、dry-run 模式、脚本草案机制等实践案例。

> 顶层决策请参见：`aia-agent-architecture/decisions/0010-tool-sandbox-and-execution-control.md`。

# 计划中的子文档

- `shell-and-filesystem-sandbox.md`：
  - 约束文件系统访问和 shell 命令执行的实现模式（如 chroot、容器、受限命令白名单等）。
- `http-and-network-sandbox.md`：
  - 对外 HTTP/网络访问的白名单、限流、重试与审计设计。
- `database-and-internal-service-sandbox.md`：
  - 针对数据库与内部微服务的只读/读写策略、事务与回滚机制设计。

后续可根据实际项目需要逐步补充这些文档，并在顶层 ADR 中引用关键结论。
