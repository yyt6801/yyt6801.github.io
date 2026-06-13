# 代码审查最佳实践


## Review的核心理念
- 代码审查的目的：发现缺陷、知识共享、代码质量提升
- 心态建设：对事不对人

## Review Checklist
### 功能性
- 逻辑是否正确
- 边界条件处理
- 异常处理是否完善

### 可维护性
- 命名是否清晰
- 函数是否单一职责
- 注释是否必要且准确

### 性能与安全
- 有无潜在性能问题
- SQL注入、XSS等安全风险

## PR提交规范
- PR描述模板
- 控制在合理大小
- 关联Issue/Ticket

## Review流程
- 提交→分配→审查→修改→批准→合并
- 何时需要二次Review
- 合并策略选择（merge、rebase、squash）


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/code-review/  

