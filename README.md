# Sweet Cloud Atelier

An original, bilingual interactive portfolio set on a miniature dreamlike baking island.

一个以梦幻烘焙岛、旋转木马和原创女孩 IP 为主题的中英文个人交互式作品集项目。

## 当前状态 / Status

M0 — 规划与规格文档阶段 / Planning and specifications only.

本仓库当前只有设计与开发规格及维护说明。尚无可运行网页、依赖、构建产物或在线演示；没有导入旧相册的代码、模型或其他素材。仓库建立不代表网站已完成或部署成功。

This repository currently contains specifications and maintenance guidance only. There is no runnable application, dependency setup, build output, or live demo yet. Implementation and deployment will follow milestone approval.

## 已确定方向 / Direction

- 2.5D 云朵微缩岛；六栋建筑分别进入教育、科研、实习、项目、开源与简历。
- 梦幻烘焙主题及原创女孩角色，气质甜美、阳光、有活力；角色最终外形尚未定稿。
- 桌面端优先，兼顾移动端、键盘操作及减少动态效果设置。
- 中文与英文切换；专业内容仅使用经过筛选、核对和批准的资料。
- 每个项目独立详情页、搜索摘要和社交分享预览。

A 2.5D island with six destinations: Education, Research, Internships, Projects, Open Source, and Résumé. The experience will support Chinese and English, mobile and keyboard access, reduced motion, and project-specific metadata. Character artwork and detailed visual layouts remain subject to approval.

## 文档目录 / Specifications

| 文件 | 内容 |
| --- | --- |
| [PRODUCT_SPEC.md](PRODUCT_SPEC.md) | 定位、范围、六区映射、验收与待确认决策 |
| [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) | 配色、材质、字体、透视、动效与响应式规范 |
| [INTERACTION_SPEC.md](INTERACTION_SPEC.md) | 建筑、角色、菜单、卡片、语言与浏览器历史行为 |
| [CONTENT_SCHEMA.md](CONTENT_SCHEMA.md) | 内容结构、中英文审核和公开门禁 |
| [ASSET_MANIFEST.md](ASSET_MANIFEST.md) | 素材需求、预算、原创与许可范围 |
| [TECHNICAL_PLAN.md](TECHNICAL_PLAN.md) | 架构、性能、测试与后续开发任务 |
| [CONTRIBUTING.md](CONTRIBUTING.md) | 修改、增加、删除文件及审核方法 |

## 技术规划 / Planned stack

React + TypeScript + Vite，使用路由、构建期预渲染/静态生成及客户端交互；拟部署至独立 Cloudflare Pages 项目。不默认引入数据库、登录系统、实时 3D 引擎或个人内容管理后台。

上述为技术规划，不是已经安装的依赖。M1 确认前请勿根据本仓库尝试 npm install、npm run dev 或构建部署；当前没有 package.json。

The planned stack is React, TypeScript, and Vite, with routing, build-time prerendering/static generation, and client-side interactions. A separate Cloudflare Pages deployment is planned. No database, login system, or real-time 3D engine is assumed. Runtime commands will be documented once implementation exists.

## 如何持续修改 / Editing this project

此仓库不是一次性的附件，后续可以持续增删和修改文件。

1. 小修改：在 GitHub 打开对应 Markdown 文件，进入编辑并提交有意义的修改说明。
2. 新增资料：先完成隐私与权利检查，再新增文件。修改规格时同步更新相互引用的文档。
3. 删除文件：确认没有引用后删除并提交；误改可通过 Git 版本历史恢复。
4. 较大改动：建议新建分支、提交修改并通过 Pull Request 审核后合并。
5. 也可以由维护者继续通过 JoyCode 协助修改；远端推送仍需明确指令和有效授权。

浏览器中的文件编辑与未来网站的内容编辑器不是同一功能。本阶段不会提供后台登录、在线改简历或自动发布系统。删除当前文件不等于清除 Git 历史，请勿提交任何隐私或凭据。

Changes can be tracked through commits and reviewed in pull requests. This is source-file maintenance, not a website CMS. Publishing new personal content requires explicit review; deleting a file does not remove its past revisions from Git history.

## 隐私与原创建设 / Privacy and originality

不要上传源简历、私人审核稿、联系方式原件、证书原件、公司内部资料、未授权图片、凭据或 .env 文件。真实个人内容和公开代码分开审核；本地审核目录不属于此仓库，不能一起打包提交。

本项目独立创作，不以删除他人署名或给他人素材更名的方式建立原创性。第三方依赖、参考资料与素材须记录来源和许可，不将主题灵感等同于源代码或素材授权。

Private résumé drafts, unapproved personal material, internal company information, credentials, and third-party assets without permission must not be committed. Keep private review materials outside the repository.

## 许可状态 / Licensing status

正式许可证尚未落地 / Formal license files are pending.

已选定的方案是：有权授权的原创代码及其配套技术文档拟采用 MIT；逐项确认并纳入清单的原创美术拟采用 CC BY 4.0。权利人署名及具体授权清单仍待确认，当前不声称已完成开放许可发布。个人履历、肖像、论文、专利材料、证书和第三方素材不自动纳入该方案。

The intended scheme is MIT for eligible original code and accompanying technical documentation, and CC BY 4.0 for individually cleared and listed original artwork. Attribution and scope still require confirmation; no formal license files have been issued yet. Personal résumé material and third-party content are not automatically covered.

## 下一阶段 / Next milestone

等待 M1 确认后，先完成六区导航线框、双语内容样板、建筑与角色视觉探索，再逐步实现可独立测试的功能。建立仓库本身不触发完整网页开发或部署。
