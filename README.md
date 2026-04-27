# 身心营养红绿灯 · 15 题膳食自测 H5

讲座现场互动工具：观众扫码自测 → 老师大屏看实时汇总（红黄绿分布、最普遍的红灯习惯、逐题"符合"比例）。

- **观众端 H5**：`/docs/index.html` → `https://caoqiyuancao-create.github.io/diet-h5/`
- **大屏端**：`/docs/screen.html` → `https://caoqiyuancao-create.github.io/diet-h5/screen.html`
- **后端**：腾讯云 CloudBase（环境 ID 在 `docs/config.js`）

## 文件结构

```
diet-h5/
├── docs/                 ← GitHub Pages root
│   ├── index.html        观众端：15 题问卷 + 红黄绿结果 + 标准膳食清单
│   ├── screen.html       大屏端：实时 KPI / 环形图 / Top3 红灯习惯 / 逐题排行
│   ├── config.js         CloudBase env id 配置
│   ├── tweaks-panel.jsx  大屏的浮动控制面板（演示模式 / 暂停 / 重置 / 视图切换）
│   └── uploads/          图片资源
└── archive/              不上线，备份留底
    ├── 膳食健康自测.html              单机版（无后端，备用）
    ├── 膳食健康自测_离线版.html       完全离线版
    ├── 膳食健康自测_standalone_src.html 构建源
    └── 原作者部署说明.md              作者原版用 Firebase 的部署说明（仅参考）
```

## CloudBase 后端配置（一次性）

环境 ID：`cloudbase-d0gi12o758d35105a`（与 DCT 共用，DIET 用独立集合 `diet_submissions`）

控制台 → 该环境下需要的配置：

1. **Web 安全域名** 加入：
   - `caoqiyuancao-create.github.io`
   - `localhost:8080`（仅本地测试时用，测完删）
2. **匿名登录** 已开启（DCT 已配，复用即可）
3. **数据库 → diet_submissions 集合**：
   - 权限：「读取全部数据，修改本人数据 [READONLY]」（新版控制台命名；语义=匿名用户能创建自己提交 + 读所有人提交，但改不了别人的）
   - 字段：`ts`, `answers[15]`, `verdict`, `R/Y/G/P`, `id`

如果换新 CloudBase 环境，改 `docs/config.js` 的 `env` 字段。

## 本地开发

```bash
cd docs
python -m http.server 8080
```

- 观众端：http://localhost:8080/
- 大屏：http://localhost:8080/screen.html

## 现场使用

1. **讲座前一晚**：手机扫 `https://caoqiyuancao-create.github.io/diet-h5/` 完整自测一遍，确认能写入；老师电脑 Chrome 全屏 `https://caoqiyuancao-create.github.io/diet-h5/screen.html`，确认实时收到。
2. **讲座现场**：
   - PPT 第 8 页放观众端 URL 的二维码（任意 QR 工具，比如 cli.im）
   - 老师电脑全屏大屏 → 右下角 Tweaks 浮动面板 **关闭"演示模式"** 切到真实数据
   - 观众扫码 → 答 15 题 → 大屏实时聚合
3. **讲座后**：CloudBase 控制台 → 数据库 → `diet_submissions` 集合 → 导出 CSV

## 大屏 Tweaks 面板（右下角浮动控件）

- **演示模式（模拟数据）**：现场前调试用；正式开始关掉切到真实数据
- **模拟速度**：演示模式下数据生成速度
- **暂停接收新数据**：现场临时冻结画面（讲解时用）
- **重置 / 清空全部数据**：清空当前 store（不删 CloudBase 库里的数据）
- **视图模式**：dashboard（标准）/ big-pie（大饼图）/ big-rank（仅排行）
- **红灯主色**：现场配色微调

## 改动历史

- 2026-04-27：从作者 Firebase 原版改造为腾讯 CloudBase 后端，避免 gstatic.com 大陆访问问题。React/Babel CDN 切 jsDelivr 主 + unpkg 备双源。
