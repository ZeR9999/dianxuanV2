# ⚡ 电选 · DIANXUAN

> 全品类电器选型平台：把「买哪台」拆成可比较的数字，先算账，再下单。
>
> iOS 风格 / 白 + 蓝 / 零前端依赖 / Node 全栈 / 数据真实留存。

---

## 1. 这是什么

一个面向三类买家的电器选型站点：

- **个人 / 家庭**：按户型、面积、预算挑家电
- **工厂 / 车间**：按工况、功率、电费账算回本周期
- **门店 / 企业**：按场地、客流、合规要求配设备

每个产品都有：价格区间、适用规模、完整参数、优缺点、选型建议、年运行电费与 5 年总成本；
所有产品都能进「对比台」逐行比较，并由选型向导给出**可解释的匹配排序**。

与普通产品站的区别：

| 普通展示站 | 电选 |
| --- | --- |
| 一件产品一段介绍 | 每个产品都有成本模型（功率 × 时长 × 天数 × 电价） |
| 「推荐」没有依据 | 六维评分 + 规模适配 + 预算覆盖，逐条给出理由与风险 |
| 图片写死在代码里 | 后台独立上传通道，上传后即时生效、可设封面 |
| 表单发到第三方 | 线索写入本地 SQLite，可筛选、可导出 CSV |

**90 款设备（三大场景各 30 款） · 19 个品类 · 9 项对比维度 · 4 步选型向导。**

站点包含：首页、产品库、产品详情、对比台、选型向导、关于电选（选型方法与数据隐私）、需求登记页、后台管理。
首屏没有任何装饰性英文标签，图片未到位处统一显示「实拍图敬请期待」。

## 预览

| 首页 | 产品库 |
| --- | --- |
| ![首页](preview/home.png) | ![产品库](preview/catalog.png) |

| 产品详情 | 对比台 |
| --- | --- |
| ![产品详情](preview/product.png) | ![对比台](preview/compare.png) |

| 选型向导 | 关于电选 |
| --- | --- |
| ![选型向导](preview/wizard.png) | ![关于电选](preview/about.png) |

| 移动端 | |
| --- | --- |
| ![移动端](preview/mobile.png) | |

---

## 1. 这是什么

## 2. 技术选型（为什么没有 npm install）

当前环境无法访问 npm registry，所以整套系统按「**零第三方依赖**」设计，反而更适合长期维护：

- **后端**：Node.js 24 内置 `node:http` + `node:sqlite` + `node:crypto`
- **前端**：原生 ES Module + 手写模板引擎 / 路由 / 状态容器，无打包器、无框架
- **选型引擎**：`shared/engine.mjs` 前后端同构——浏览器即时算，服务端用同一套逻辑留存结果
- **绘制**：产品示意图全部是内联 SVG（约 30 种线稿），不依赖任何图片文件

> 要求 Node ≥ 22.5（`node:sqlite` 内置）。推荐 Node 24。
> 没有任何 `dependencies`，`package.json` 只为提供命令别名。

---

## 3. 目录结构

```
dianxuan-v2/
├── server/                  # 后端
│   ├── index.mjs            # HTTP 服务：静态资源、SPA 回落、SEO 注入、路由分发
│   ├── config.mjs           # 环境变量、目录、密钥、口令
│   ├── db.mjs               # SQLite 建表 / 种子同步 / 查询
│   ├── api.mjs              # REST API 与业务校验
│   ├── auth.mjs             # HMAC 签名会话、口令恒定时间比较、登录限流
│   └── upload.mjs           # multipart 解析 + 图片头嗅探 + 尺寸读取
├── shared/                  # 前后端共享
│   ├── taxonomy.mjs         # 场景 / 分类 / 需求意图 / 对比维度
│   ├── catalog.mjs          # 90 款产品（核心 38 + 扩展 52，首次启动写入数据库）
│   └── engine.mjs           # 评分、TCO、对比矩阵（纯函数、同构）
├── web/                     # 前端（无需构建，直接由服务端托管）
│   ├── index.html           # 站点外壳
│   ├── admin.html           # 后台外壳（独立入口，noindex）
│   └── assets/
│       ├── css/app.css      # 设计系统（约 1900 行）
│       ├── js/core.mjs      # 模板 / store / fetch / 路由 / 抽屉 / Toast
│       ├── js/ui.mjs        # 图标、产品线稿、卡片、对比托盘、表单
│       ├── js/state.mjs     # 产品库与统计的全局状态
│       ├── js/app.mjs       # 站点入口
│       ├── js/admin.mjs     # 后台入口（登录、概览、线索、图片、产品）
│       └── js/pages/        # 首页 / 产品库 / 详情 / 对比 / 向导 / 关于
├── scripts/
│   ├── make_assets.py       # 生成 favicon、图标、OG 分享图
│   └── smoke.mjs            # 41 项端到端冒烟测试
├── 启动电选.bat             # Windows 一键启动并打开浏览器
├── 停止电选.bat             # 停止本地服务
├── preview/                 # README 用的页面截图
├── data/                    # 运行时生成（已 gitignore）
│   ├── dianxuan.db          # SQLite 主库
│   ├── uploads/YYYYMM/      # 上传的实拍图
│   ├── .secret              # 会话签名密钥（自动生成）
│   └── .admin-token         # 未配置环境变量时的后台口令
├── .env.example
└── package.json
```

---

## 4. 启动

### 最省事的方式（Windows）

双击项目根目录的 **`启动电选.bat`**：会自动检测 Node、启动服务并打开浏览器。
用完后双击 **`停止电选.bat`** 关闭服务。

### 命令行方式

```bash
# Node 24（或 ≥22.5）
node server/index.mjs
```

打开 <http://127.0.0.1:5180>，后台在 <http://127.0.0.1:5180/admin>。

首次启动会自动：

1. 创建 `data/` 目录与 SQLite 表
2. 把 `shared/catalog.mjs` 里的 90 款产品写入数据库（只补新增，不覆盖后台修改）
3. 生成 `data/.secret` 与 `data/.admin-token`
4. 在控制台打印后台口令

```text
  ⚡ 电选 · DIANXUAN
  站点      http://127.0.0.1:5180
  后台      http://127.0.0.1:5180/admin
  口令      dx-xxxxxxxxxx   （可用环境变量 DX_ADMIN_TOKEN 覆盖）
```

可用命令：

```bash
npm start        # = node server/index.mjs
npm run dev      # node --watch，改后端代码自动重启
npm run smoke    # 冒烟测试（需先启动服务）
npm run assets   # 重新生成图标与 OG 图（需要 Python + Pillow）
```

---

## 5. 后台与图片上传通道（重点）

### 5.1 登录

访问 `/admin`，输入启动日志里的口令（或 `DX_ADMIN_TOKEN`）。会话是 HMAC 签名的 HttpOnly Cookie，**7 天有效**，服务端不存会话表。

安全措施：

- 口令用 SHA-256 摘要 + 恒定时间比较，避免时序侧信道
- 连续 6 次失败锁定 10 分钟（按 IP）
- Cookie `HttpOnly` + `SameSite=Lax`，所有后台接口二次校验
- 后台页面 `X-Robots-Tag: noindex`

### 5.2 上传实拍图

1. 后台 → **图片库**
2. 拖拽图片到虚线框，或点击选择（支持多选）
3. 上传完成后点击图片下方 **「设为封面」**
4. 选择对应产品 → 确认

**立即生效**：产品卡片、详情大图、社交分享图（OG）都会改用实拍图；数据库里同时保存了真实宽高，前端可避免布局跳动。

上传规则（服务端强制）：

- 仅接受 **jpg / png / webp**，按文件头识别真实类型，改扩展名无效
- 单张 ≤ 12MB（`DX_MAX_UPLOAD` 可改），单次最多 10 张
- 最小尺寸 64×64，建议 1600×1200 以上、4:3 横构图
- 文件名由内容哈希 + 随机串组成，避免覆盖与路径猜测
- 删除图片会自动解除产品封面引用

> 没有图片时，站点使用内联 SVG 线稿占位（`web/assets/js/ui.mjs` 中的 `deviceArt`）。
> 这条通道是「独家」的：公开 API 只能读 `/api/catalog`，写图片必须持有后台会话。

### 5.3 批量抓取演示图（可选）

后台逐张上传适合正式产品图。做演示或临时铺量时，也可以走「检索 → 打分 → 上传」三步：

```bash
# 1. 按产品名从百度图片检索，下载候选到 data/stock-candidates/
node scripts/fetch-product-images.mjs --missing

# 1b.（可选）工业/商业设备再用 360 图源补一轮候选
node scripts/fetch-360-images.mjs --missing

# 2. 用白底比例、构图比例、清晰度打分，挑出可用的一张
python scripts/pick_stock_images.py

# 3. 把选中的图片上传并设为封面
node scripts/fetch-product-images.mjs --attach-choice
```

说明：

- 只挑选**白底、接近方形、长边 ≥600px** 的图，带价格/促销文字的海报会被过滤
- 选不出来的产品会保持线稿，不会强行配错图
- 图片来自公开搜索结果，**版权归原作者所有，仅适合本地演示**；对外上线请换成品牌授权图
- 打分阈值在 `scripts/pick_stock_images.py` 顶部的 `MIN_WHITE` / `MIN_LONG`

### 5.4 线索与需求单

客户可以直接打开 **`/request`** 填写完整需求单：使用场景、需求类型、设备/品类、公司、规模、预算、期望时间、地点、具体描述与联系方式。提交后进入后台同一套「需求线索」，扩展字段保存在 `leads.extra`，并在详情与 CSV 中展开。

- 首页 / 产品详情的「获取方案」仍是轻量表单，只收联系方式与一句需求
- 后台「需求线索」详情会显示：公司、需求类型、期望时间、地点、设备/品类
- 每张需求单有编号（如 `#12`），回复客户时可以直接引用
- 需求登记页右侧提供链接复制，方便发给客户或放进公众号/微信群
- 状态：待跟进 / 已联系 / 已报价 / 已成交
- `导出 CSV`：带 BOM，Excel 直接打开不乱码，含需求单扩展列

### 5.5 产品管理

- 改名、改品牌、改卖点、改价格区间、改简介
- 一键上架 / 下架（下架后前台与 API 立即不可见）
- 保存即生效，无需重启

### 5.6 新需求微信提醒（Server酱）

客户提交需求后，除了进后台，还会通过微信「Server酱」推给你。

1. 打开 https://sct.ftqq.com/ ，微信扫码登录，复制 SendKey（`SCT` 开头）
2. 把根目录的 `notify.example.json` 复制为 `data/notify.json`，填入 SendKey：

   ```json
   { "serverchan": { "sendkey": "SCTxxxxxxxx", "enabled": true } }
   ```

3. 测试：`node scripts/notify-test.mjs`，微信收到测试消息即配置成功
4. 之后每次有人提交需求表单，微信会收到：编号、场景、需求类型、设备、规模预算、时间地点、联系方式和需求描述

说明：

- 没配置或填错时静默跳过，**不影响表单正常提交**
- 修改 `data/notify.json` 后不用重启，下一次提交自动生效
- 想临时关掉把 `enabled` 改成 `false`
- SendKey 等同于发送权限，不要发到公开仓库；`data/notify.json` 已在 `.gitignore` 中

---

## 6. 数据留存

全部落在本地 SQLite（`data/dianxuan.db`），WAL 模式：

| 表 | 用途 |
| --- | --- |
| `products` | 产品主数据（JSON）+ 状态 + 排序 |
| `media` | 图片库：路径、宽高、大小、sha256、标题 |
| `product_media` | 产品与图片的关联（cover / gallery） |
| `leads` | 需求线索：联系方式、场景、规模预算、备注、选型快照、状态、UA/IP |
| `events` | 行为事件：浏览、对比、选型、上传、留资 |
| `comparisons` | 已保存的对比方案（可分享链接） |
| `settings` | 预留的键值配置 |

写入时机：

- 产品详情页 → `view` 事件（含 visitor id）
- 对比台 → `compare` 事件（对比了哪几款）
- 向导出结果 → `selection` 事件（输入参数 + Top 3 分数）
- 表单提交 → `leads` + `lead` 事件
- 上传图片 → `upload` 事件

备份：直接复制 `data/` 目录即可（`.db` 与 `uploads/` 一起带走）。恢复时放回原位再启动。

---

## 7. API

公开接口：

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/health` | 健康检查 |
| GET | `/api/bootstrap` | 品牌、场景、分类、意图、对比维度、公开统计 |
| GET | `/api/catalog?scenario=&category=&q=` | 产品列表（含封面） |
| GET | `/api/products/:slug` | 产品详情 + 图库 |
| POST | `/api/selection` | 选型计算（留存输入与结果） |
| POST | `/api/compare` | 生成对比矩阵，`save:true` 返回可分享 id |
| GET | `/api/compare/:id` | 读取已保存的对比方案 |
| POST | `/api/leads` | 提交需求（限流 + 蜜罐） |
| POST | `/api/events` | 上报行为事件 |
| GET | `/api/stats` | 公开统计 |

后台接口（需会话 Cookie）：

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| POST | `/api/admin/login` / `logout` | 登录 / 退出 |
| GET | `/api/admin/session` | 会话状态 |
| GET | `/api/admin/overview` | 看板数据 |
| GET | `/api/admin/leads` · `leads.csv` | 线索列表 / 导出 |
| PATCH | `/api/admin/leads/:id` | 更新跟进状态 |
| GET | `/api/admin/media` | 图片库 |
| POST | `/api/admin/media` | 上传（multipart） |
| DELETE | `/api/admin/media/:id` | 删除图片 |
| GET | `/api/admin/products` | 全部产品（含草稿） |
| PATCH | `/api/admin/products/:slug` | 修改产品 |
| POST | `/api/admin/products/:slug/media` | 绑定封面 / 图库 |

错误统一为：

```json
{ "error": { "code": "invalid_contact", "message": "请填写有效的联系方式（微信 / 手机 / 邮箱）" } }
```

---

## 8. 选型引擎

`shared/engine.mjs`，纯函数、零依赖，前后端共用。

**评分**（0–100）：

```
score = 100 × ( 0.30 × 规模适配
              + w_price × 预算覆盖
              + w_energy × 能效 + w_durable × 耐用
              + w_quiet × 静音 + w_smart × 智能 )
```

- 规模适配：比较用户填写值与产品的适用区间，超出后按差距衰减
- 预算覆盖：预算 ≥ 价格上限得满分；低于入门价按比例衰减
- 权重：五档预设（均衡 / 预算 / 省电 / 静音 / 耐用），也可用滑块自己调
- 结果附带 `reasons[]`（推荐理由）与 `risks[]`（风险提示），不做黑箱

**成本模型**：

```
年耗电 = 功率(kW) × 每天小时 × 每年天数
年电费 = 年耗电 × 电价（默认 0.68 元/kWh，可在对比台切换 0.55 / 0.68 / 1.05）
5 年总成本 = 购机中位价 + 年电费×5 + 年维护×5 + 安装费
```

**对比矩阵**：9 个维度（价格、年电费、5 年总成本、能效、耐用、静音、维护、安装难度、智能），逐行标注最优/最差，综合分 = 能力 55% + 成本 45%。

---

## 9. 部署

单机最小部署：

```bash
HOST=0.0.0.0 PORT=5180 DX_PUBLIC_ORIGIN=https://your-domain.com node server/index.mjs
```

建议放在 Nginx 后面：

```nginx
server {
  listen 443 ssl http2;
  server_name your-domain.com;

  client_max_body_size 20m;        # 图片上传

  location / {
    proxy_pass http://127.0.0.1:5180;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

systemd 示例：

```ini
[Unit]
Description=Dianxuan site
After=network.target

[Service]
WorkingDirectory=/opt/dianxuan-v2
Environment=PORT=5180
Environment=HOST=127.0.0.1
Environment=DX_PUBLIC_ORIGIN=https://your-domain.com
Environment=DX_ADMIN_TOKEN=change-me-please
ExecStart=/usr/bin/node server/index.mjs
Restart=always

[Install]
WantedBy=multi-user.target
```

上线检查清单：

- [ ] 设置强口令 `DX_ADMIN_TOKEN`（≥ 12 位随机串）
- [ ] 配置 `DX_PUBLIC_ORIGIN`（影响 canonical、OG、sitemap、分享链接）
- [ ] 备份 `data/`（含 `uploads/`）
- [ ] 替换示例品牌 / 参数为真实产品数据
- [ ] 接入真实线索通知（可在 `server/api.mjs` 的 `/api/leads` 处加 webhook）

---

## 10. 常见改动

| 想改什么 | 位置 |
| --- | --- |
| 产品文案 / 参数 / 价格 | 后台「产品管理」；或改 `shared/catalog.mjs` 后删除旧产品记录再重启 |
| 新增产品 | 在 `shared/catalog.mjs` 里加一条，重启即可自动入库 |
| 场景 / 分类 / 需求意图 | `shared/taxonomy.mjs` |
| 电价默认值 | `DX_ELEC_PRICE` 或 `shared/engine.mjs` |
| 评分权重 | 向导页滑块 / `WEIGHT_PRESETS` |
| 对比维度 | `shared/taxonomy.mjs` 的 `COMPARE_DIMS` + `engine.mjs` 的 `dimValue` |
| 颜色与圆角 | `web/assets/css/app.css` 顶部的 `:root` 变量 |
| 产品示意图 | `web/assets/js/ui.mjs` 的 `deviceArt` |
| 首页精选产品 | `web/assets/js/pages/home.mjs` 的 `FEATURED` |

---

## 11. 验证

```bash
npm start            # 终端 A
npm run smoke        # 终端 B
```

覆盖 41 项：页面 SEO 注入、gzip、CSP、共享模块、产品数量、选型结果、对比矩阵与保存、
线索写入、后台登录/会话/统计、图片上传与宽高识别、封面生效与删除、CSV 导出、匿名访问拒绝。

---

## 12. 说明

- 站内参数、价格与品牌均为**公开资料整理的示例数据**，用于演示选型逻辑，采购前请以厂商最新参数与现场勘测为准。
- 没有产品实拍图时显示线稿与「实拍图敬请期待」；上传实拍图后会自动替换，不需要改代码。
- 仓库内附带批量抓图脚本（百度 + 360 双图源 + 白底打分 + 黑底转白底），90 款产品全部已配实拍图；找不到合适图的会保留线稿，不会硬配错图。
- 站点为浅色主题（白 + 蓝），跟随 iOS 的圆角、毛玻璃、大标题与弹簧缓动，尊重 `prefers-reduced-motion`。
- 品牌闪电与图标沿用原始 logo 的黄橙红渐变，底色为深蓝渐变；重新生成图标运行 `npm run assets`。# dianxuanV2
