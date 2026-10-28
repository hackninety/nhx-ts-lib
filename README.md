# nhx-ts-lib

> **维护位置已迁移**：后续源码、问题反馈和独立版本发布统一位于 [common-ts-lib/packages/nhx-ts-lib](https://github.com/hackninety/common-ts-lib/tree/main/packages/nhx-ts-lib)。包名与公开接口保留，本仓库保留历史，不再作为新版本安装来源。
>
> 当前安装：`npm install https://github.com/hackninety/common-ts-lib/releases/download/nhx-ts-lib-v0.1.1/nhx-ts-lib-0.1.1.tgz`
>
> 以下为迁移前的历史说明，其中旧仓库安装命令仅供追溯；新的开发和发布流程见 [迁移说明](https://github.com/hackninety/common-ts-lib/blob/main/MIGRATION.md)。


**倪海厦教学语料库**（人纪五部讲稿、天纪十部、地纪遗稿、讲座演讲、专题文章、
959 篇医案、415 味本草、411 穴位、113 首经方、人纪视频逐字稿 6486 段）——
深度结构化 TypeScript 库，纯 TS、零运行时依赖、ESM、按需分包懒加载，
可直接被 React / Vite / Next / 纯 Node 消费。

**许可：CC BY-NC 4.0（禁止商用）**，详见 `LICENSE` —— 内容为倪海厦公开教学资料的
社区转录与整理汇编，版权归倪海厦先生继承人及各整理者；如实署名、非商用、
权利人异议即撤。**内容仅供学习研究，不构成任何医疗建议。**

倪海厦（1954–2012），美国执业中医师，生于台湾，创办佛罗里达汉唐中医诊所，
以《人纪》（针灸/内经/本草/伤寒/金匮）与《天纪》（易经/紫微/风水）系列课程闻名。

## 语料规模

| 分支 | 规模 | 保真度 | 底本 |
|---|---|---|---|
| 讲稿库 docs | 14 书 936 篇（人纪五部 569 篇 + 天纪 184+13 + 地纪 9 + 讲座 27 + 专题 9 + 汉唐方剂 104 + 闭门课/附录/相学） | 分级 | nivance/nihaixia-kb 等 |
| 医案 cases | 11 组 959 篇（2005–2008 诊疗日志为倪师亲笔） | 分级 | nivance/nihaixia-kb |
| 中药 herbs | 415 味（性味/归经/功效/主治 + 人纪原文引用页码） | third-party 结构化 | qmzz/ni-haisha-tcm-skill |
| 穴位 acupoints | 411 穴（经络/定位/功效/穴性/国际代码/别名） | third-party 结构化 | qmzz/ni-haisha-tcm-skill |
| 方剂 formulas | 113 首经方（出处/分类 + 原文引用） | third-party 结构化 | qmzz/ni-haisha-tcm-skill |
| 逐字稿 transcripts | 人纪五部 6486 段（书/章/段三级，RAG 就绪） | transcript | wesky-online/zhongli_data |
| 64 卦 gua | 天纪人间道 64 卦详解 | lecture-notes | wh149728915-stack/nihaixia-64gua |
| 视频 videos | B 站直链索引 6 系列 139 集（人纪五部 + 天纪，仅链接与标题） | — | hantang.org.cn 视频页快照 |

每篇文档/条目携带 `fidelity` 保真度标签：
`first-party` 倪师亲笔 ｜ `lecture-notes` 听课整理稿 ｜ `transcript` 逐字稿 ｜
`attributed` 传述存疑（如汉唐方剂讲解，整理者自注可能非倪师原文）｜ `third-party` 第三方整理。

全部底本取自 GitHub 公开仓库并锁定 commit（`getSources()`），
完整来源目录与取舍记录见 [docs/sources.md](docs/sources.md)。

## 安装与使用

```bash
npm install github:hackninety/nhx-ts-lib
```

```ts
// 根入口：注册表 + 篇目检索（轻量同步）
import { getBooks, searchDocs, getSources, DISCLAIMER } from 'nhx-ts-lib';

getBooks();          // 14 书（slug/系列/分组/保真度/篇数）
searchDocs('太陽病'); // 繁简折叠篇目检索 → DocMeta[]
```

```ts
// 讲稿库：manifest 同步、正文按书分包懒加载
import { getDocsManifest, getDocMarkdown, searchDocsFull } from 'nhx-ts-lib/docs';

getDocsManifest();                                    // 936 篇目
await getDocMarkdown('shanghan/ben/003-辨太阳病脉证并治法上篇.md'); // 首访拉取该书 chunk
await searchDocsFull('四逆汤', { books: ['shanghan', 'jingui'] });   // 渐进全文检索
```

```ts
// 结构化分支
import { getHerb, findHerbs } from 'nhx-ts-lib/herbs';
import { getAcupoint, getAcupointsByMeridian } from 'nhx-ts-lib/acupoints';
import { getFormula, getFormulasBySource } from 'nhx-ts-lib/formulas';

await getHerb('艾叶');            // 性味/归经/功效/主治 + 讲解正文 + 原文引用 + 本草讲稿互链
getAcupointsByMeridian('大肠经'); // 经络列举；findAcupoints('湧泉') 繁简折叠
await getFormula('白虎汤');       // { source: '伤寒论', refs: [{file, page, quote}], md }
```

```ts
// 医案（分组分包）与人纪逐字稿（RAG 就绪）
import { getCaseGroups, getCaseIndex, getCaseMarkdown, searchCasesFull } from 'nhx-ts-lib/cases';
import { searchTranscripts, loadTranscriptBook } from 'nhx-ts-lib/transcripts';
import { buildTranscriptContext } from 'nhx-ts-lib/prompt';

getCaseIndex('riji2005');                     // 倪师 2005 年诊疗日志索引（含日期/来诊提要）
await getCaseMarkdown('riji2005/001');
const hits = await searchTranscripts('白虎汤', { books: ['shanghan'] });
buildTranscriptContext(hits);                 // → 带出处/保真度声明的提示块
```

```ts
// 天纪 64 卦
import { findGua } from 'nhx-ts-lib/gua';
await findGua('天泽履'); // { no: 10, symbol: '䷉', md: 详解全文 }

// B 站视频索引（同步轻量）
import { getVideoSeries, getVideoSeriesFor, bilibiliUrl } from 'nhx-ts-lib/videos';
getVideoSeriesFor('shanghan'); // { title:'伤寒论', episodes:[{ n:1, title:'倪海厦《伤寒论》01', bv:'BV…' }…] }
bilibiliUrl('BV1wH4y1j7GD');   // https://www.bilibili.com/video/BV1wH4y1j7GD/
```

React 宿主接入（Vite/Next、抽屉分组、RAG 组装）见 [INTEGRATION.md](INTEGRATION.md)。

## 架构

```
docs/corpus/            规范底本（2900+ 文件，引入自源仓库、保留原始 frontmatter）
  <slug>/<group>/*.md     讲稿 14 书
  _cases/ _structured/ _transcripts/ _gua64/ _meta/sources.json
scripts/ingest.mjs      一次性引入（源仓库 → docs/corpus，锁定 commit）
scripts/gen-data.mjs    生成：manifest/载荷/索引/统计 → src/data/*.json + src/chunks/*.ts
scripts/build.mjs       gen → tsc → 落 data → 修 ESM 导入（dist 可被纯 Node 直接消费）
src/                    类型 / 注册表 / 归一化(繁简折叠) / 各子入口访问器
```

- 轻量同步：manifest 与各索引随入口内联；重载荷（30MB 文本）拆 36 个 chunk 动态 import
- 底本文字不改写：仅剥引入期 frontmatter、统一标题行；解析异常记 `src/data/textual-issues.json`
- `npm test`：20 项语料完整性/检索/懒加载测试

## 开发

```bash
npm install        # 触发 prepare → build
npm run gen        # docs/corpus → src/data
npm test           # vitest
npm run build      # dist/
```

未收录路线图（官网 3779 篇文章 htm 镜像、原版 PDF 讲义、视频/音频索引等）见 [docs/Todo.md](docs/Todo.md)。
