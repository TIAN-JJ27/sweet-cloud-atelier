# Sweet Cloud Atelier — CONTENT_SCHEMA

版本：M0 / 0.2 · 2026-10-23 · 本文定义数据合同，不是个人资料，也不包含已批准的经历。
参考：[PRODUCT_SPEC.md](PRODUCT_SPEC.md)、[ASSET_MANIFEST.md](ASSET_MANIFEST.md)。

## 1. 数据与公开原则

内容以结构化公开文件维护，不默认数据库、登录或后台。单份内容同时服务目录、岛屿入口、卡片、详情与简历摘要，不能在组件里手写第二份个人经历。

分离两层：

1. **私有整理/审核材料**：真实姓名原稿、证据、草稿、导师/同事信息、公司内部截图、未批准的翻译。存于仓库之外，由主人保管；本项目不得自动扫描本机简历或其他文件。
2. **公开投影数据**：只包含已批准的字段、文件和翻译，位于未来仓库的 content/public。构建与部署只读取此层。

`.gitignore` 不能保护已经提交过的隐私。不能把 draft 全量 JSON 提交公开仓库，再用前端 published=false 隐藏。浏览器不显示不代表仓库、source map、静态文件或网络请求不可读取。

## 2. 公共实体外壳

字段类型是概念约束，M1 批准后才实现 TypeScript/schema 验证器。R=公开条目必填；O=可选，未提供则不输出 UI；A=必须人工确认。

| 字段 | 类型/约束 | R/O | 人工确认与用途 |
| --- | --- | --- | --- |
| schemaVersion | 固定版本字符串，初始候选 1 | R | 修改时写迁移说明 |
| id | 稳定唯一 ID，ASCII kebab-case | R | 不用手机号、学号或证件号 |
| kind | profile / education / award / research / publication / patent / experience / project / opensource / resume | R | 决定列表和页面模块 |
| slug | 稳定 URL 段，不含语言；有详情的条目必填 | 条件 R | 改名不随意改 slug；改动需 alias 方案 |
| order | 有限整数 | O | 人工选择展示顺序，不暗示排名 |
| featured | boolean，缺省 false | O | A：是否代表希望重点展示的作品 |
| updatedAt | ISO 日期，日精度，仅真实更新日期 | R | A，不用发布日期伪装项目完成时间 |
| translations | 按 zh/en 分别存已批准 LocalizedContent | 首批公开条目 R，两门齐全 | A：每门独立审核，草稿不在此 map |
| links | 经验证的 Link[] | O | A：公开权限、目标地址、标题和用途 |
| assetIds | 指向公开资产清单的 ID[] | O | A：资产存在、授权与隐私都通过 |
| relatedIds | 指向其他已发布条目的 ID[] | O | 禁止悬空和自循环推荐 |
| seo | 每门语言的 title、description、shareImageId、shareImageAlt；canonical 由生产主机名和路由生成，不手填 | 有详情路由则 R | A：不得把未审核指标写入摘要，图片与内容一起批准 |
| release | 公开审核记录摘要 | R | 对应当前内容与资源哈希，见第 10 节 |

日期区间使用 start/end（YYYY 或 YYYY-MM）+ precision(year/month) + ongoing 布尔。ongoing=true 时 end 必须为空；结束不早于开始；所有显示保留精度。不得从年份推断月份。统一 ID 和 slug 不含个人隐私。

Link：label（双语可选）、href（https 或批准 mailto）、kind（repository/demo/paper/certificate/contact/other）、external 布尔。没有 URL 时不创建空按钮；外部资源需可公开访问。相对内部链接必须通过路由表校验。

LocalizedContent 公共字段：title、summary、bodyBlocks、tags、assetAltOverrides（可选）。summary 建议中文 40–100 字、英文 25–60 词；是排版建议，不删减必要事实。正文只采用受控块：paragraph、heading、list、quote、image、link、metric。MVP 不允许任意 HTML、脚本或 JSX/MDX 执行。

## 3. 个人简介 Profile

| 字段 | 类型/要求 | 人工确认 |
| --- | --- | --- |
| displayName | zh/en 展示名称；首批两门均批准，姓名可采用同一合法拼写 | 已批准显示值：zh=TIANJING、en=IRIS；大小写保持不变。此批准不自动包含简历原件里的实名或联系信息 |
| professionalHeadline | 一句话专业定位 | A：不能把求职意向写成已获职位 |
| introduction | 简短简介，受控段落 | A：身份、领域、经历均以主人提供资料为准 |
| focusAreas | 专业兴趣数组 | A：兴趣不冒充熟练技能或研究成果 |
| skills | 名称、类别、说明或关联证据 | A：不自动打百分比/“精通”分数 |
| avatarAssetId | 审核后的照片或原创 IP | A：肖像授权、是否代表本人；无则不显示头像 |
| contactLinks | Link[] | A：每个邮箱/平台单独确认，不默认手机号和家庭地址 |
| availability | 可选求职/合作状态及更新日期 | A：不能从过去资料推断当前状态 |
| locationLabel | 可选城市级文字 | A：不需要精确位置或定位权限 |

首页不是自动生成的简历摘要；每句可公开表达都应由主人确认。公开展示名已确认：中文界面使用 TIANJING，英文界面使用 IRIS。中文界面允许使用拉丁字母名称，不擅自推断汉字姓名；此处确定的是个人展示名，不是女孩 IP 名字，也不是法定权利人身份认证。

当前简历接收状态（2026-10-23）：用户重新提供的 DOCX 已成功读取并去除重复文本框内容；事实条目、中英文建议译文及 OPEN QUESTION 已整理在公开项目目录之外的本地审核区。除已确认的展示名外，内容事实、翻译和逐项公开许可仍待审核；部分教育字段已有用户补充确认，私有审核记录已更新；新增开源项目仓库获准建立，尚不构成已完成作品。上传附件不等于批准全文公开，不把源简历或私人审核稿纳入本项目、GitHub、部署目录及 SEO/分享数据。

## 4. 教育背景 Education

R：institutionDisplayName、degreeOrProgram、fieldOfStudy、dateRange、localizedSummary。O：researchInterests、selectedCourses、honors、supervisorDisplayName、publicInstitutionLink、logoAssetId。

A：学校、学院、学位是否已取得/在读、日期、课程是否实际修读、奖项名称与级别、导师是否愿意公开。GPA/排名为可选且需范围和口径，不推算。学校 logo 未确认授权则使用普通文字。

教育排序按主人确认，不凭“名气”排序；转专业/联合培养如需要可用多个真实条目或明确说明，不杜撰。

## 5. 科研项目 Research

| 字段 | 要求 | A：重点确认 |
| --- | --- | --- |
| title、researchQuestion、context | R | 研究题目是否可公开；问题范围不能泄密 |
| dateRange、status | R；status 为 planned/ongoing/completed | 不以“项目完成”代替“论文发表” |
| role、myContribution | R；本人负责的具体工作 | 不把团队全量成果算作本人独立成果 |
| methods、tools | R/O：方法 R，工具 O | 仅列实际采用方法；模型名称、数据集公开权利 |
| outcomeBlocks | O | 结论强度、局限、未完成部分；无成果可诚实说明 |
| publicationIds / patentIds | O；引用第12节独立实体 | 通过稳定ID关联，不另存第二份作者顺序/状态；逐项确认 |
| collaboratorsDisplay | O | 公开姓名/单位需要确认，不发布私人联系方式 |
| reproducibility | O；代码/数据/环境说明链接 | 数据保密或授权限制明确；不公开敏感数据 |
| covers/figures | O | 图表是否有论文/团队/第三方授权，是否需改绘 |

科研指标 Metric：label、value、unit、populationOrScope、method、period、attribution（本人/团队）、limitations、approvedPublicEvidence（可选）。任何 value 缺少来源与口径时不对外发布；不能只给“提升 30%”而无对象/基线。内部证据引用不导出为网页 URL。

## 6. 实习经历 Experience

R：organizationDisplayName、roleTitle、dateRange、summary、responsibilities、actions、myContribution。

O：teamDisplayName、locationLabel（城市级）、tools、outcomes（Metric 或文字）、relatedProjectIds、publicEvidenceLinks、organizationLogoAssetId。

A 必须逐项确认：

- 公司/部门是否允许公开，岗位名是否准确、是否为实习而非全职。
- 职责与个人贡献的边界，是否涉及客户名、业务数据、内部系统名、产品未发布功能。
- 截图必须去除账号、员工信息、访问令牌、内部地址、订单/客户数据；仅遮 UI 不保证源文件已脱敏。
- 成果量化的基线、样本、周期与团队归属；不把规划目标写成已经实现。
- 保密的工作可以保留允许公开的方法描述，禁止为丰富网页而推断具体业务细节。

未获得公司或主人确认时条目留在私有整理区，不把“机密项目”占位图片作为已批准事实发布。

## 7. 专业/独立项目 Project 与 OpenSource

Project R：title、problem、audienceOrUseCase、dateRange、status（concept/building/released/archived）、role、myContribution、approach、technicalStack（可以为空但需诚实说明）、outcomeSummary。

Project O：decisionsAndTradeoffs、architectureImageId、demoLinks、repositoryLink、lessonsLearned、nextSteps、coverAssetId、metrics、teamContext。是否独立完成必须人工确认；可以是团队项目，不强制叫“独立”。

OpenSource 使用 Project 字段，另外：

| 字段 | 约束/用途 | 人工确认 |
| --- | --- | --- |
| repository | 公开可访问 https GitHub 等地址，R | A：确为本人维护或有贡献的项目 |
| contributionType | creator/maintainer/contributor，R | A：不把 fork 当作原始创作 |
| codeLicense | 许可证名称/标识 + 文件链接，R | A：与仓库一致；未知许可不声称可自由使用 |
| quickStartSummary | 文字/步骤，O | 实测后写，不提供可能泄密命令 |
| contributionGuideLink | O | 真实存在，说明接收贡献方式 |
| releaseLabel | O | 真实版本，不杜撰成熟度 |
| upstreamReferences | O/有上游则必填 | A：保留来源、修改范围和许可证 |

MVP 不调用 GitHub API 自动展示 star/贡献量；若未来加入须注明来源、缓存和时间，不拿快照冒充实时数据。当前 Sweet Cloud Atelier 本身可在上线后成为一项开源作品，但 M0 不能声明已经完成。

## 8. 简历 Resume

R：displayName、locale、headline、approvedProfileId、educationIds、selectedExperienceIds、selectedProjectIds、updatedAt、release；可选 awardIds/publicationIds/patentIds 引用批准成果。其余内容从已批准实体派生或引用，不复制不一致的经历。

O：pdfAssetId、publicContactLinks、skillGroups、printNote。每门语言一个独立简历入口记录；HTML 正文可存在而 PDF 缺失。文件建议 future `resume-zh-YYYYMMDD.pdf` / `resume-en-YYYYMMDD.pdf`，实际文件名发布前确认。

PDF 人工审核：可搜索文字、阅读顺序、没有内部批注/编辑历史、作者/软件元数据无私人信息、链接有效、语言与版本匹配、必要图片权限明确。下载链接可被任何人转发，不能把“没有显示在菜单”当成保护。

## 9. 场景元数据与 UI 翻译

场景模块定义：id、routeSection、labelKey、buildingAssetId、desktopPlacement、mobilePlacement、hotspotBounds、characterAnchor、order。坐标归一化 0..1；模块与 routeSection 一一映射。背景图上不烧录中英文标题。

UI 字典 keys 由语义命名（如 navigation.research、resume.pdfUnavailable），zh/en 必须成对存在。公开字段的翻译映射按稳定内容 ID，而不是依赖标题文本匹配。中文学校/公司名称的英文译法需主人确认，不能未经确认使用机器生成的官方称呼。

内容缺译按 INTERACTION_SPEC I-05 回退。空列表是 UI 状态，不是一个假实体；缺 cover 使用通用原创占位，不拿网上图片替代。

## 10. 人工批准与构建门禁

私有审核记录含 reviewer、日期、待改意见、证据原件位置，不入公开仓库。公开 release 仅保留不含身份的 approvalId、approvedAt、revisionDigest、approvedLocales，以及内容/资源版本引用；不含审核人的私有联系方式。哈希是版本一致性校验，不是法律授权证明。revisionDigest 的规范输入为公开内容字段、批准语言内容与所引用资产的哈希，排除 release 审核记录本身，避免自引用哈希；序列化顺序在实现时统一。

审核粒度：实体字段、每门翻译、每个附件均需通过。修改个人事实、指标、图片、PDF 或翻译后，必须重新核对并批准新 revisionDigest，不能沿用旧 approved 标签绕过门禁。

未来构建规则：

1. 公开目录不得出现 draft/rejected/internal 条目；任何发布文件缺 approvalId/版本匹配时构建失败。
2. 枚举、唯一 ID/slug、日期区间、语言 keys、资产引用、允许 URL 协议全部验证。
3. 未批准语言不导出；首批公开列表只引用两门语言均已批准的条目。
4. 本站内部路由与资产必须存在；相对文件不能跳出 public 范围。
5. 资源同时通过版权与隐私检查；二者任一不通过则不能入 dist。
6. 扫描疑似 token、内部域名、邮箱/电话等风险字段后要求人工确认；自动扫描不宣称能检测全部个人信息。
7. 仓库历史与 dist 同时抽查。文案里“已脱敏”不代替审核记录。

批准步骤：主人提供最少资料 → 私有整理与事实核对 → 双语和资产逐项确认 → 导出公开投影 → 门禁校验 → 私有预览/本地核对 → 主人确认发布 → 公开仓库和 Pages。

## 11. 开发占位与样例政策

M1/M2 开发可用明显合成样例，标记“示例条目，非本人经历”，放 fixtures/test-data，不自动进入公开内容索引。禁止使用看似真实的公司、大学、论文题目、排名和成果数字来装点原型。

首批类别及中英要求已确认，但具体事实、公开版本与译文仍为 OPEN QUESTION（OQ-03/04/05）；本 M0 没有任何可直接发布的个人经历。正式首批投影要求 zh/en 都获批准；缺译回退仅为开发/异常容错，未经主人批准不得以它替代首批完整双语验收。


## 12. 新确认的奖项、论文、专利实体

全部是结构定义，不含本人已经获得/发表的断言。六建筑结构不变：奖项归教育，论文与专利归科研；项目可引用这些成果，简历可汇总，不能多处抄写造成状态不一致。

| 实体 | 必需字段（逐项人工确认） | 可选字段及边界 |
| --- | --- | --- |
| Award | title、issuerDisplayName、awardDate及精度、awardType、levelOrScope、recipientRole、zh/en译文 | rank、teamContext、relatedEducationId/projectIds、publicEvidenceLink、redactedCertificateAssetId；证书不默认公开，不凭名称推断国家级/国际级 |
| Publication | title、authorsOrdered、myAuthorPosition、publicationType、status、venueDisplayName、statusVerifiedAt、zh/en展示摘要 | DOI/URL、year、volume/issue/pages、relatedResearchIds、approvedAbstract、publisherLicense、authorizedFulltextAssetId；原文题目保留，非官方英文译题标注；全文无权不上传 |
| Patent | title、jurisdiction、patentType、status、inventorsOrdered、myInventorPosition、applicantOrAssignee、statusVerifiedAt、zh/en说明 | applicationNumber、publicationNumber、grantNumber及各自日期、publicRegistryLink、relatedResearchIds；申请号/公开号/授权号不能混用，公开发明人信息仍需主人确认展示范围 |

Publication.status：draft/submitted/under-review/accepted/published/withdrawn/retracted；日期只展示有依据的阶段，录用≠发表。Patent.status：filed/published/pending/granted/withdrawn/rejected/expired/revoked；最终按所在法域公开登记核对，授权日期与现时有效性分开，不将专利状态自动推断为有效、独占或可以由本人开放授权。论文摘要、附图或PDF可能另有出版方/单位权利。

实际 detail 路由：research/publications/:slug、research/patents/:slug，与普通 research/:slug 同时存在；科研 slug 禁用 publications/patents；奖项初版作为教育页 awards 锚点，不新增空洞详情页。

## 13. 双语 SEO 与分享字段门禁

每个项目/科研/开源详情及论文/专利详情记录逐语言 seo.title、seo.description、shareImageId、shareImageAlt。description 应准确简明，中文建议 60–100 字、英文 120–160 字符仅为编辑参考，不保证搜索引擎原样展示。

构建器根据批准路由生成 title、meta description、canonical、hreflang、Open Graph（title/description/type/url/image/image:alt/locale）与分享卡元数据；canonical/og:url 为生产 HTTPS URL，图片为可公开抓取的绝对 HTTPS URL。title/description 不是浏览器加载后才补写，必须出现在原始 HTML。

正式双语页分别 canonical 到自身，不把全部英文页指向中文；zh/en 互相 hreflang，x-default 指向语言选择根页。正文内容、SEO 字段和分享图均纳入 revisionDigest；任何一个改动都需重新审核。分享抓取只访问公开投影，不能把受限 PDF、内部图表当分享封面。

抓取器缓存、搜索引擎选择摘要和收录速度不受本站完全控制；验收承诺“原始HTML与图片信息正确且可抓取”，不承诺排名、收录日期或所有平台立即刷新。
