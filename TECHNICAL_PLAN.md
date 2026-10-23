# Sweet Cloud Atelier — TECHNICAL_PLAN

版本：M0 / 0.2 · 2026-10-23 · 状态：已记录用户决定，实施细节待 M1 验证。此文中的命令、目录和任务仅为规格，不已执行。

## 1. 架构结论与不做事项

DECIDED：独立的 React + TypeScript + Vite 构建期预渲染/SSG 静态前端，React Router 声明式路由 + 客户端 hydration；分层图片/SVG/CSS 构成 2.5D 场景；公开结构化内容驱动页面；Cloudflare Pages 托管 dist。不从旧相册的 Vinext/Cloudflare Worker 构建产物迁移，不复用旧源代码、资源或 Worker 名。

默认不需要数据库、用户登录、CMS、SSR 服务器、Pages Functions、R2/D1/KV、Three.js/React Three Fiber/WebGL、物理引擎、服务端上传、远程翻译、支付或实时多人。即使框架支持这些，也不安装无用模块。

M0 只创建六份 Markdown。现在没有 package.json、lockfile、应用源码或部署工作流，也没有修改 Cloudflare 账号设置。后续依赖版本在 M1/M2 开始时重新验证官方要求，不用旧相册 lockfile。

## 2. 决策记录 ADR

| ID | 建议决策 | 理由与替代触发条件 |
| --- | --- | --- |
| ADR-01 | 原创独立仓库与代码库 | 主题可相近，实现/素材/具体构图全新；不删除旧许可再复用旧项目 |
| ADR-02 | Vite + React/TS，构建期预渲染为静态多路由HTML | 满足每项目独立搜索摘要/社交预览；运行时仍无常驻服务器 |
| ADR-03 | 共享路由树，构建时静态路由上下文、浏览器中 BrowserRouter | 路由与首屏输出一致；水合后导航保留浏览器历史，不引入第二套路由事实 |
| ADR-04 | 2.5D DOM + 原创图层 | 手机预算、键盘访问与美术一致性；只有批准真实 3D 需求后才重评 |
| ADR-05 | CSS 基础动效；复杂路由/角色协调时可选 Motion | 只使用一种 JS 动画协调工具；不同时装 GSAP、Motion、额外平滑滚动库 |
| ADR-06 | 受控公开内容数据+编译期 schema 校验 | 安全、版本化、维护简单；数据增长再考虑 CMS，不先建库 |
| ADR-07 | 路由语言 + 本地 UI 字典 | 两语规模可控；相同内容稳定 ID；增长到多语协作时再评估 i18n 库 |
| ADR-08 | React Bits 仅作视觉/交互参考 | 当前许可有组件再分发约束；初版不引入代码，避免开源许可混淆 |
| ADR-09 | 公共 Pages + GitHub 自动发布（后续批准配置） | 代码与内容可审阅；不等于网络一定可达；预览同样不能含私密资料 |
| ADR-10 | 默认无访客统计、表单、service worker | 最小隐私与缓存复杂度；联系用确认后的外链 |
| ADR-11 | OQ-11 CONFIRMED：每个批准路由逐页预渲染，按条目/语言生成OG | 原始HTML即含正确正文/元数据，不依赖抓取器执行JS；明确搜索收录和缓存不保证 |

这些决策未锁定精确依赖版本。M1 核对当时受支持的 Node.js LTS、React/Vite/Router/Motion 的兼容性并记录选择，不能只写“用最新”。M2 选一个包管理器（建议 npm）、提交 lockfile 和 Node 版本文件，CI/Pages 一致使用。

## 3. 模块边界

单向关系：批准内容与场景配置 → schema/路由映射 → 可访问 UI → 装饰场景/动效。场景无权改写内容事实，也不能成为路由的第二个真相来源。

| 模块 | 责任 | 不负责 |
| --- | --- | --- |
| AppShell | 站名、普通导航、跳转正文、语言、设置、菜单、错误壳 | 具体项目事实 |
| RouteMap | 语言/section/slug/query 校验、链接生成、规范 URL | 角色位置缓存 |
| ContentRepository | 读取批准投影、按 ID/语言取内容、缺译与空态 | 私有资料抓取、在线编辑 |
| IslandScene | 图层、建筑配置、可见热点、场景失败回退 | 替代菜单、阻塞内容 |
| CharacterPresentation | idle/attentive/navigating/arrived、取消旧动效 | 登录/游戏/角色寻路 |
| SectionList / DetailPage | 卡片、正文、证据、筛选和关联内容 | 不受控 HTML 执行 |
| ResumePage | HTML、打印、批准 PDF 链接 | 未确认文案生成 |
| AccessibilityController | 导航焦点、朗读公告、滚动恢复、reduce | 覆写浏览器所有默认快捷键 |
| AssetRegistry | 尺寸/alt/路径/授权元数据、响应式选择 | 运行时下载未知素材 |

React 内置 state/context 足够管理小规模 UI 偏好；路由为 URL，内容不可变，暂不选 Redux/Zustand。动效状态机可用简单枚举/reducer 思路，不默认引入状态机库。

## 4. 后续目录建议（不创建代码）

| 路径 | 用途 |
| --- | --- |
| src/app/ | 壳、路由、全局偏好与错误边界 |
| src/pages/ | 首页、六区、详情、简历、未找到 |
| src/features/island/ | 场景配置、BuildingLink、CharacterPresentation |
| src/features/navigation/ | 菜单、返回/滚动恢复、语言切换 |
| src/components/ | 可访问基础组件、卡片、空态 |
| src/content/ | schema、查询、公开数据适配 |
| src/i18n/ | UI 字典、日期格式、语言工具 |
| src/styles/ | token、全局排版、组件/场景局部 CSS |
| content/public/ | 已批准个人事实与翻译、公开 release 摘要 |
| public/assets/ | 批准的优化图形与许可明确的字体 |
| public/resume/ | 批准的中文/英文 PDF；没有批准则为空 |
| tests/ | 单测/组件测试/端到端/合成 fixtures |
| docs/ | 后续开发与部署说明；M0 六文档可保留根目录或批准后统一移动 |
| .github/workflows/ | 后续 CI（需确认后编写） |

私有整理、授权合同和未公开简历不放以上任何路径。项目可开源并不要求主人公开全部职业资料；公开版本应只用批准数据，其余用明显合成测试数据。

## 5. 路由、预渲染与数据策略

构建路由枚举与 BrowserRouter 的路径合同均与 PRODUCT_SPEC 一致，包含科研项目、论文详情和专利详情三类；所有外部分享链接可直接打开。无语言根路径只做客户端 replace 到已选择语言；跨语言同一 slug，不生成重复个人实体。

数据由构建时读取的 JSON/受控结构提供；项目详情可按模块懒加载公开数据，不把全站所有封面静态 import 进首屏。小型 UI 字典可两语同包，以实际体积决定是否拆分。禁止使用危险 HTML 直接渲染未经清洗内容；MVP 不支持执行型 MDX。

schema 方案建议选择 Zod 作为构建/测试阶段验证器，是否纳入生产 bundle 在 M1 评估；浏览器只需要发布后规范数据，不因校验库自动增加大体积依赖。可替代为 JSON Schema，但不得两套 schema 漂移。

内容门禁详见 CONTENT_SCHEMA：所有内容/资产批准版本一致；没有任一可公开翻译则不导出；不使用 published=false 隐藏私有原件。

### 5.1 构建期生成合同

使用一份 RouteMap 和批准内容清单列出 /zh/、/en/、六模块及所有详情路由。以 React 的静态渲染入口配合当前请求位置的路由上下文输出各页面，再在浏览器中以同构首屏数据 hydration。具体 Vite 预渲染适配器/React Router API 在 M1 技术样板中验证，禁止宣称已有业务实现；不引入一个与客户端组件各自维护内容的模板系统。

每个路由输出 `dist/<lang>/<section>/<slug>/index.html`（仅目录结构示意）；首页/列表按实际段数生成。嵌套论文/专利同样枚举；生成后输出根语言选择 index.html、根 404.html、sitemap.xml、robots.txt 及批准分享图片。项目 canonical 使用带尾斜杠的目录式 URL，与 Pages 对 index.html 的规范化行为保持一致；内部链接统一，旧式无尾斜杠通过平台行为测试，不随意叠加重定向。

查询 filter=featured 不生成另一个内容文件：用户激活木马后的客户端筛选仍工作；直接访问该查询先收到同一路径的完整静态列表，首屏水合完成后应用筛选，避免 query 不同造成水合不匹配。无脚本时提供全部项目与静态“精选”标记/锚点，主要内容不丢失；canonical 指向无筛选参数的列表。

locale 由生成路由决定，正文数据在构建/水合双方一致。随机数、当前时间、window/localStorage、视口判断与 pointer 状态不参与初始静态树；用户偏好在挂载后才增强。用确定性占位保证手机重排不造成 hydration mismatch。若图片、字体或分享图生成失败，构建明确失败，不发布混合旧新版本。

根 / 静态页是语言选择与站点概要，脚本加载后才按偏好 replace；未知路径使用真正的静态404页面及语言入口，不能无条件重定向回首页来掩盖错误。

## 6. 动效与可访问性实现约束

CSS 实现建筑 focus/hover、按钮与卡片静态状态；可选 Motion 只处理需协调的角色状态/页内入场，路由逻辑不依赖动画 Promise 完成。尊重 OS reduce（官方 Motion 文档提供 reducedMotion/useReducedMotion 模式作为实现参考）。

事件清理：pointermove 使用按需 RAF 与离开取消，页面不可见暂停；resize 用同一观察入口，避免每建筑创建独立监听；相机视差是有上限的 DOM 装饰，不驱动业务布局。菜单采用一个可访问方案，不把现成 UI 包全量搬入。

所有图标有明确用途。链接/按钮的语义、tab 顺序、dialog 焦点、route announcer、inert 清理与滚动恢复需独立测试。自动检查目标 WCAG 2.2 AA 相关条款，但只有自动报告不能宣称全面符合。

## 7. 性能预算与测量方法

下面是上线候选目标，M1 样板实测后可书面调整，不允许在失败后默默抬高预算。

| 项目 | 初版预算 | 测量口径/超预算措施 |
| --- | --- | --- |
| 初始 JS | ≤250KiB gzip | 首页必需 JS +共享运行时；build report，排查重包/拆路由 |
| 初始 CSS | ≤50KiB gzip | 首页样式总量；不引入未用全组件样式 |
| 首屏网络总量 | 手机≤850KB、桌面≤1.5MB | 冷缓存到主要界面可用时所有请求；scene不预载全部尺寸 |
| 场景/建筑/木马 | 手机≤400KB、桌面≤650KB | 实际选中响应式资源之和；超标优先简化美术 |
| IP 首屏 | ≤60KB | 后续姿态可按需加载；避免视频/精灵帧爆量 |
| 自托管字体 | 默认0；选用后首屏≤200KB | 经授权后子集；不重复下载相近字体 |
| LCP | 实验室目标≤2.5s | 固定手机模拟与冷缓存3次中位数；真实p75需上线后数据，不伪造 |
| CLS | ≤0.1 | 固定图片尺寸、字体策略；包含场景载入 |
| 输入反馈 | 实测点击/键盘反馈目标≤100ms；未来现场 INP p75≤200ms | 实验室交互trace不冒充现场INP；优化主线程长任务 |
| 主线程长任务 | 关键交互窗口内避免>50ms脚本任务 | Chrome Performance；减少首次解码/组件渲染 |
| 常驻装饰循环 | MVP 0 个必要循环 | 可选循环有暂停，非可见时停止 |

基准测试建议：Chrome Stable（记录实际版本）、390×844、CPU 4×减速、4Mbps 下行/1Mbps 上行/80ms RTT 的固定自定义网络、冷缓存；记录电脑型号/测试环境，3次中位数，另做真实中端 Android/iPhone 烟测（具体设备 OQ-09 发布测试准备时确认）。不能把不同网络配置得分混为一谈。

首屏项目截图不全部 preload；正文与主要导航先出现。视差、装饰云、复杂纹理首先降级；不通过压缩文字字号、删掉无障碍标签或把所有内容延后到点击来“达标”。Lighthouse 分数可参考，但不能代替 P-01 至 P-12。

## 8. 测试分层与功能任务映射

PROPOSED 工具：Vitest 单测、React Testing Library + user-event 组件测试、Playwright 端到端、axe-core 辅助可访问性检查；版本在实施前确认。不在 M0 安装。

| 测试组 | 覆盖 | 关联验收 |
| --- | --- | --- |
| U-01 路由/语言 | slug稳定性、路径规范、无语言根路径、缺译规则 | P-02/P-05；I-05/I-07 |
| U-02 内容/资产门禁 | ID/日期/协议/哈希/批准状态，故意提供未批准fixture须失败 | P-07/P-09；CONTENT_SCHEMA §10 |
| U-03 角色状态 | 新导航取消旧动作、reduce/卸载、到达超时 | P-06；I-02/I-03 |
| C-01 可访问组件 | 热点名称/链接、卡片不嵌套、菜单焦点/关闭 | P-03；I-01/I-04/I-06 |
| E-01 主流程 | 首页→六区→详情→返回/筛选/语言 | P-01/P-02；I-01至I-07 |
| E-02 降级 | 坏图、慢网络、缺译、空列表、无存储权限、chunk失败 | P-06/P-11；I-08 |
| E-04 静态SEO/分享 | 原始HTML正文/title/description/canonical/hreflang/OG、不同条目不同图、404、hydration一致 | P-05；I-10 |
| E-03 简历 | HTML打印、正确语言PDF、有/无附件、外链 | P-09；I-09 |
| V-01 视觉回归 | 手机/桌面/平板、长文、200%字号、320px回流、reduce | P-04；DESIGN_SYSTEM |
| M-01 人工测试 | NVDA/VoiceOver、真实触屏、Tab顺序、阅读速度、晕动感 | P-03/P-11 |
| D-01 真实部署 | 深链刷新、资源Content-Type、缓存更新、未知路径、目标网络 | P-02/P-12 |

测试数据用合成内容，不使用主人未授权的个人文件。链接校验区分本站破损（阻断）与第三方临时不可达（人工复核）；CI 不依赖不稳定第三方实时抓取来生成事实。

## 9. Cloudflare Pages 部署规格

目标是新的 Pages 静态站点，不是旧 workers.dev 相册的继续发布。OQ-09 批准名称、账号/仓库授权和可见性后才配置。

- 生产分支建议 main；构建命令 `npm run build`；产物目录 `dist`；项目根目录按未来仓库结构设置。
- 版本固定：package-lock、Node版本文件与 Pages 构建环境一致；CI 使用 `npm ci`。平台是否需要显式构建环境变量在实施时核验，不在公开仓库写令牌。
- GitHub 授权只授予新项目所需仓库；不改变现有私人相册或其他项目。默认不购买域名、不升级付费套餐。
- 默认使用 Pages 提供的项目地址，只有部署完成后才记录真实 URL；不能预先保证某个 pages.dev 名称可用或所有网络可达。
- Cloudflare Pages 官方文档说明：无顶层 404.html 时，SPA 路径可回退根入口。本项目已改为逐路由静态HTML，**生成顶层 404.html，明确不依赖默认 SPA fallback**；有效路由必须有真实输出文件，不用 /* /index.html 200 覆盖它们。
- 必须在预览站测试 /zh/projects/已批准slug 与 /en/... 的直接访问/刷新，以及不存在资源的 Content-Type；不要把返回 HTML 的脚本请求当作加载成功。
- 直接请求不存在路径由 Pages 的静态 404 文件返回404；客户端内部错误导航同样显示未找到组件。验收同时检查真实 HTTP 状态，不只检查画面。不需要为此增加 Functions。
- deep link、尾斜杠、语言切换、老 chunk 缓存行为需测试；不在整个站点设置永久缓存 HTML。带哈希资源可长缓存，HTML 应遵循可更新策略并在响应头测试。
- 初版无 service worker，避免离线缓存让访客长期停在旧版。后续若加入需独立缓存升级/注销测试。

### 发布门禁与回滚

PR：typecheck → unit/component → content/asset approval check → build → bundle budget → e2e/static accessibility → 人工审查。Pages 自动部署不是绕过此门禁：main 保护规则要求检查通过并人工合并，预览分支同样只允许已批准公开数据或明确合成数据。

公开 GitHub、PR、Actions artifacts、Pages preview 都可能被访问；未批准专业资料不要推上去再依赖“不告诉别人链接”。默认仅合成/批准数据可进入 CI 产物；第三方 PR 不接收部署凭据。

正式发布：主人批准内容与资产 → 合并受保护 main → Pages 发布 → 桌面网络/手机移动数据分别验证 → 记录 URL、commit、构建编号、测试结果。真实网络打不开时报告限制，不能将“云端发布成功”当作完工。

回滚：使用已验证的前一批准 commit 重新构建/Pages 回滚到其部署版本；保留 Git 历史，不强推删除记录。不为了回滚恢复已撤销公开许可或含隐私的内容；隐私撤回需独立清理和缓存处理。

## 10. SEO、分享与渐进增强（OQ-11 已确认）

每个已发布项目、科研/开源作品以及论文/专利详情都拥有独立中文和英文正文HTML、title、meta description、canonical、hreflang、Open Graph（title/description/type/url/image/image:alt/locale）及相应社交卡字段。分享图按 contentId + locale + revision 生成，以公开静态图片URL提供，图片变更用版本/内容哈希避免沿用旧文件。

OG-01 只用于站点/模块通用封面，不代替项目的 OG-{contentId}-{locale}。可共享绘图模板，但每页标题和已批准的封面/类别内容不同。所有文本、图像输入与元数据在一个发布版本内同步审核。无权公开的论文全文、公司图表不得用于分享封面。

只有确定真实生产主机名后才生成绝对 canonical/og:url/og:image；构建应拒绝正式环境使用 localhost、.example 或未配置的域名。预览使用其正确预览地址，并设置 noindex，避免与正式页竞争；noindex不是隐私保护，预览只能有批准或合成内容。sitemap 只列正式批准路由，双语页互相 hreflang、各自 canonical，根页可作 x-default。

建议分享封面在构建阶段由原创模板渲染为PNG/JPEG，字体需按OQ-08登记许可；封面生成器只用于构建不打包到浏览器。正文渲染与封面渲染适配器均在M1验证版本和产物，不能临时引用React Bits输出。

验收：用 HTTP 请求直接读取HTML（不执行JS）核对正文与元数据；分享图请求返回真实图片类型和尺寸；至少两项项目两种语言的字段不相同；检查客户端跳转也更新head；无JS时正文、普通目录、双语链接和简历可读。真实搜索引擎可重写摘要，社交平台会缓存，不能承诺排名、收录时点或每个平台立即采用指定预览。

## 11. 开源与安全治理

OQ-07 已按主人委托决定：自有代码 MIT，清单内已确权原创美术 CC BY 4.0，个人资料和第三方材料不自动包含。2026-10-23 用户明确确认署名 TIAN-JJ27 (IRIS)，已添加标准 LICENSE、CC BY 4.0 文本及范围清单；当前仅原创文档/配置已按 MIT 发布，未发布美术；第三方依赖/图标/字体各自遵循原许可，THIRD_PARTY_NOTICES 记录来源与改动。不能把品牌故事“原创”扩展成“依赖也是原创”。

公开前应具备 README（定位/截图/本地运行/构建/内容替换/可访问性/许可边界）、CONTRIBUTING（分支与测试规范）、SECURITY（经主人批准的漏洞联系渠道）、资产授权清单、示例内容声明。M0 原计划不生成这些实施文件；2026-10-23 用户追加授权建立文档仓库，因此先提供仅适用于规划阶段的 README、CONTRIBUTING 与隐私忽略规则。完整运行说明、正式许可和安全联系渠道在对应确认后补齐，不虚构可运行命令。

React Bits 检查说明：官方 LICENSE.md 在 2026-10-23 的 GitHub blob 为 `4069ce9d0ef1a712c93d6ce48ade5d5667e0f5ee`。其条款允许应用/网站使用，同时限制组件本身、工具/声音及部分输出的单独再分发；不得当作纯 MIT 重新授权。本项目以独立实现规避引入这一代码依赖，MVP 不复制其组件。未来如需要引入，必须先明确具体版本、用途、再分发模式与许可兼容性，不把本段当法律结论或免除审核。

安全：不渲染不受控 HTML；SVG 导出清理脚本/外链；外部 URL allowlist 协议；不在 VITE_* 暴露密钥（它们可能进客户端包）；不提交 OAuth、.env、真实本机路径、公司内部域名或 PDF 批注；用人工审阅补充 secret scan。日志和错误不打印私有内容。

若用户以后要在线修改资料，需作为新需求重新讨论权限、发布审核、保存与隐私，不能以这个公开静态项目自动获得企业资料处理权限。

## 12. 后续里程碑与开发任务

下表是后续排期建议，不代表已经授权开始。规模为相对复杂度，不承诺日期。

| 阶段/任务 | 输入/依赖 | 交付与完成条件 |
| --- | --- | --- |
| M0（本次） | 用户构想、官方参考 | 六规格文档和统一 OQ 台账；停止等待确认 |
| M1-01 信息结构与原型 | M0确认，OQ-01/02方向 | 桌面/手机低保真；六区、目录和返回路径可走查 |
| M1-02 内容与公开边界 | OQ-03/04/05 | 一份真实但私有审核的内容清单、术语表、发布批准流程；不自动公开 |
| M1-03 原创美术简报 | OQ-01/03/06/08 | IP轮廓候选、单栋建筑/木马材质样板及预算审核；未批准不批量作图 |
| M1-04 架构与许可冻结 | OQ-07/09/11，预算 | 验证已确定的逐页预渲染、双语OG模板、字体/动效依赖及分层许可落地；可估算任务 |
| M2-01 最小可访问骨架 | M1书面确认 | React/TS/Vite、路由、普通导航；先用合成内容跑 I-01/05/07 |
| M2-06 预渲染与分享 | M2-01/02、正式域名配置、批准SEO字段 | 路由枚举/静态HTML/双语OG/sitemap/404生成；原始HTTP与hydration测试通过 |
| M2-02 内容门禁 | CONTENT_SCHEMA | 校验和公开投影；未批准fixture确实阻断构建 |
| M2-03 菜单/卡片/简历 | M2-01/02 | 键盘/双语/历史/打印与下载测试 |
| M2-04 岛屿与角色 | M1批准美术、M2-01 | 场景只增强导航；独立状态与性能预算通过 |
| M2-05 回归与优化 | M2-01至04与M2-06 | P-01至P-11证据；权限与内容人工审核 |
| M3-01 软件开源验收 | M0 文档仓库与许可已落地，新增资产范围确认，M2合格 | 更新运行说明/许可/贡献指南，补充合成样例并执行干净环境构建 |
| M3-02 Pages发布验收 | 主人明确发布授权 | 真实URL、目标网络验证、回滚记录，满足P-12 |

依赖次序：信息结构与内容合同优先于精细美术；普通导航优先于角色动效；公开审核优先于远端推送；本地构建成功优先于部署。未批准个人内容可由合成样例推动组件开发，但不能据此满足正式上线的专业内容门槛。

## 13. M0 检查与参考来源

核验日：2026-10-23。工具/平台规则可能变化，实施时复核并记录实际版本。以下来源仅用于确认能力与约束，不是复制代码的授权。

| 来源 | 核验到的事实/使用方式 |
| --- | --- |
| React Bits：https://reactbits.dev/get-started/index | Animated Content、Fade Content、Spotlight Card、Tilted Card 等交互参考；独立实现 |
| React Bits 许可：https://github.com/DavidHDev/react-bits/blob/main/LICENSE.md | 非纯MIT，记录当日blob；具体再分发边界须核验 |
| Vite：https://vite.dev/guide/ | 前端构建与Node兼容要求；实施时选受支持LTS，不现在安装 |
| React Router：https://reactrouter.com/start/declarative/installation | BrowserRouter 声明式路由路线 |
| Motion：https://motion.dev/docs/react-accessibility | reducedMotion 与 useReducedMotion 可用于减少动态适配 |
| Cloudflare React Pages：https://developers.cloudflare.com/pages/framework-guides/deploy-a-react-site/ | main、npm run build、dist 的静态React部署配置 |
| Cloudflare SPA：https://developers.cloudflare.com/pages/configuration/serving-pages/ | 顶层404.html与SPA回退的关系、静态路径行为 |
| W3C 对比：https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html | 正文4.5:1，大字3:1；设计需要实际组合测试 |
| W3C 目标尺寸：https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html | WCAG最低24 CSS px及例外；本项目采用更舒适44px目标 |

M0 的完成仅表示规格可评审；没有性能实测、没有最终 IP、没有真实履历、没有已部署的新网站。核心方向已确认，本轮不自动制作原型或部署；M1 从具体公开身份、内容清单与角色样板细化开始。


## 14. 域名与项目安排

### 14.1 概念区分

域名/主机名是地址，部署项目是网站文件与发布历史，路径是一个地址下的页面。一个个人作品集内部的教育、科研、实习、项目等都用同一主机名下不同路径，不需要给每栋建筑注册域名。例如 `portfolio.example/zh/education/`、`portfolio.example/en/projects/sample/`（保留示例域名，不是真实可访问网址）。

Cloudflare 原相册部署为 Worker，已有真实地址 `xiaotian-carousel-party.tian-jj27.workers.dev`，不是已购买的自有注册域名。Sweet Cloud Atelier 计划新建 Pages 项目，可使用平台分配的项目级 pages.dev 地址；沿用账号不等于覆盖原 Worker，也无需购买域名才能开始。新名称是否可用须实际创建时核验，不在文档伪造已取得的地址。

### 14.2 共用地址的三种方式

| 方式 | 适合 | 是否需每项目另买域名 | 技术边界 |
| --- | --- | --- | --- |
| 同一站点的不同路径 | 作品集的六个模块与项目详情 | 不需要 | 由同一个部署的路由与生成页面承载；链接仍需唯一 |
| 同一自有注册域名的不同子域 | 未来个人主页、独立相册、其他独立应用 | 通常不需要逐个注册子域 | 有自有域名后分别在 Pages/Workers 绑定主机名和 DNS；每应用可独立部署 |
| 同一主机名下不同路径承载多个独立部署 | 必须把独立相册放到主页 /album/ 的场景 | 不需要另买域名，但不是自动配置 | 需合并构建或显式网关/反向代理及资源base/路由/缓存设计；不在本轮默认加入 |

同一个主机名的同一个路径不能凭名称同时选择两个无关部署。购买一个自有域名后，可按计划让根域名和若干子域指向不同项目；域名通常有注册/续费费用，具体价格与平台限制以后核验，本轮不购买或承诺免费额度。

### 14.3 当前建议

暂时保留相册的既有 workers.dev 地址，Sweet Cloud Atelier 单独建 Pages 项目。六个内容模块及每篇详情全部在作品集自己的同一主机名下。以后主人决定购买自有域名时，再让主站与相册使用不同子域；如果希望统一 /album/ 路径，再单独评估集成，不直接把两个独立部署绑定到同一主机名。

自有域名绑定方式依据官方 Pages 文档：Pages 项目中先关联域名，再配置对应DNS；根域与外部托管子域的接入条件不同，实际变更前核对。不得只改一条DNS就假定平台已经完成域名关联。

特别提醒：原相册照片存于浏览器按 origin 隔离的本地存储，换主机名不会自动迁移原照片；新作品集不接入这些照片。如果未来迁移相册域名，需先设计本地导出/导入方案或明确重新选图。更换地址也不保证电脑网络超时问题消失，仍需真实网络验证。

官方来源（2026-10-23核验）：
- Pages 自有域名：https://developers.cloudflare.com/pages/configuration/custom-domains/
- Workers 默认域名：https://developers.cloudflare.com/workers/configuration/routing/workers-dev/
- Pages 静态路径与404：https://developers.cloudflare.com/pages/configuration/serving-pages/
- MIT：https://opensource.org/license/mit
- CC BY 4.0：https://creativecommons.org/licenses/by/4.0/
