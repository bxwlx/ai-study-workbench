# 主题包规范（content.js Schema）

工作台骨架只认七个数据槽，**换领域只需换一份 `content.js`**，页面代码一行不用改。

## 文件约定

- 文件名：`content.js`，必须与 `工作台.html` 同目录
- 格式：JSONP —— `window.__WB_CONTENT__ = { ... };`
- 加载：`<script src="content.js"></script>`（天然绕开 `file://` 下的 CORS 限制，双击即可用）
- 本地资料正文：`content.js` 中的 `file` 字段是**相对路径**（相对资料根目录），应用/公网模式下会尝试读取同部署的镜像

## 顶层结构

```js
window.__WB_CONTENT__ = {
  app: "your-theme-name",
  version: 1,
  updatedAt: "2026-09-03",

  theme: {           // 界面文案与品牌（骨架读取；缺省回退中文默认值）
    appName, slogan, greeting, greetingSub,
    stageNames,      // 各阶段显示名，如 ["入门","进阶","高级"]
    contentLabel,    // 每日内容的叫法，如 "专栏" / "文章" / "打卡卡"
    unitMinutes,     // 每日单元时长（默认 60）
    templates,       // 首页「已沉淀模板」统计基线
    quickLinks,      // 首页快捷入口（可选）
    mineQuickLinks   // 「我的」页附加入口（可选，带 hint 显示引导标签）
  },

  profile: { stage, stageDay, streak, dailyMinutes },   // 用户进度初始值

  modules: [ { id, code, stage, name, goal, learn, practice, record, accept,
               level, status, tasks:[], paths:{plan,manual,prompt,tutorial} } ],
               // tasks = 每模块 3-5 个可开工真实任务；paths 指向资料文件（相对路径）
               // stage 必须为数字；骨架按 stage 自动聚合成「阶段切换」

  daily:   [ { id, date, moduleCode, title, minutes, source, content:[{h,p}], quiz:[{q,a}] } ],
               // 每日内容：date 唯一升序；moduleCode 关联 modules.code；5-7 小节 + 2-3 quiz

  manuals: [ { id, cat, name, desc, scene, file, reuse } ],   // 手册/SOP（reuse: copy|adapt|understand）
  prompts: [ { id, cat, name, desc, file, reuse } ],          // 提示词库
  glossary:[ { id, cat, name, desc, file, reuse } ],          // 术语/判断工具
  tutorials:[ { id, cat, name, stage, moduleCode, sections, minutes, outcome, file } ],  // 教程导航
  platforms:[ { id, cat, name, logo, desc, scene, tips:[], link, verifiedAt } ]          // 工具平台
};
```

## 字段要点（易错项）

| 字段 | 约束 |
|---|---|
| `daily[].date` | **唯一且升序**，首页按日期匹配今日内容（重复会错乱） |
| `daily[].moduleCode` | 必须在 `modules[].code` 中存在 |
| `platforms[].verifiedAt` | 必填：标注时效信息核实日期 |
| `modules[].stage` | 数字；阶段可任意多个（不限于 4） |
| `file` 路径 | 相对路径、正斜杠 `/`；可留空（页面自动隐藏对应按钮） |
| `version` | 每次内容更新 +1 |

## 生成自己的主题包（推荐流程）

1. 复制 `content.blank.js` 为 `content.js`
2. 填 `theme`（名字/标语/各阶段名/入口）
3. 填 `modules`：列出你的主题的阶段与模块（参考示例包的结构）
4. 用每日专栏生成提示词（见示例包注释）让 AI 生成 `daily` 的 14 天内容
5. 放手册/提示词/教程条目（可先留空数组，页面自动显示空态）
6. 双击 `工作台.html` 预览 → 满意后导出备份

## 内容生成提示词（AI 辅助）

见项目 README 的「用 AI 生成内容」一节；模板已在 `每日专栏生成模板` 中沉淀（选题 → 生成 → 事实核查 → 周复盘）。
