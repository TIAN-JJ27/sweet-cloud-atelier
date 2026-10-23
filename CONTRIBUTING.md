# Contributing to Sweet Cloud Atelier

## 项目阶段

当前为 M0 文档仓库，无业务代码、运行时依赖或构建命令。当前技术文档与配置按 ASSET_LICENSES.md 的清单采用 MIT，署名为 TIAN-JJ27 (IRIS)。新增代码或素材须注明来源并更新授权清单；原创美术只有确权并登记后才适用 CC BY 4.0。请先讨论较大变动，不把个人附件纳入代码许可。

## 修改与版本记录

- 修改已有内容：直接编辑相应 Markdown；保留仍有效的决策，不抹去 OPEN QUESTION。
- 新增文件：选择清晰文件名，在 README 中补充入口，并检查与现有规格是否重复或冲突。
- 删除文件：先确认入链与引用，再提交删除说明；不要删除版权/许可通知以规避原条款。
- 大改动建议独立分支与 Pull Request；小改动可以由仓库维护者直接提交。
- 每次提交只处理一组相关修改，说明原因与影响。无需为了文档更新安装依赖。

如需撤销已经发布的变更，优先通过新的修正提交或 git revert 保留审计记录。不要对共享主分支使用未经协商的强制推送。删除敏感文件不能消除历史泄露；发现误提交时停止继续分发并由维护者处理，凭据应及时撤销或轮换。

## 提交前检查

- [ ] 只有本次有意修改的文件，没有私人审核稿、源简历、内部附件或凭据。
- [ ] Markdown 链接与文件名对应；重命名/删除后引用已更新。
- [ ] 产品范围、交互、内容结构和技术计划保持一致。
- [ ] 事实、翻译、日期、贡献归属和公开权限已经区分，不把草稿写成成果。
- [ ] 截图、模型、字体和插画有明确来源与许可；未批准素材不提交。
- [ ] 文档没有声称尚未实现的网页、测试、部署或功能已经完成。

## 个人内容

个人内容先在仓库外审核，通过后只提交获准字段与资源。网站不默认提供访客上传、登录或在线内容编辑功能。公开数据结构和内容维护方案由 CONTENT_SCHEMA.md 定义；不把设置 draft=false 当作隐私措施。

## English summary

This repository is at the documentation-only M0 milestone. Keep changes focused, update references, and review larger changes in pull requests. Do not commit private drafts, credentials, internal materials, or unapproved personal content. Only claim implemented features or verified results when evidence exists. Current original documentation and configuration are licensed under MIT with attribution to TIAN-JJ27 (IRIS). New files require a scope and provenance review; personal materials are excluded. Runtime, testing, and build instructions will be added after implementation is approved.
