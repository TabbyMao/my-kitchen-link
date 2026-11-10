# Cailey's Kitchen 使用手册

🍳 网站地址：**https://cailey-kitchen.pages.dev**

一个永久免费、自动备份的私房食谱网站。所有人都能看食谱和用量，输入密码才能编辑。

```
recipe-site/
├── menu.md          ← 目录：所有分类和菜名（改这里就能加菜、改顺序）
├── recipes/         ← 每道菜一个 .md 文件（完整食谱）
│   └── _模板.md      ← 新菜谱从这里复制
├── images/          ← 成品照片
├── src/template.html← 网站的样子（颜色、字体、动画、小女孩）
├── build.mjs        ← 把上面的东西变成网站
└── functions/       ← 编辑模式的后台：检查密码、代你提交到 GitHub（令牌只存在 Cloudflare）
```

---

## 第一部分：上线（一次性，约 30 分钟，全程免费）

### 第 1 步：注册 GitHub（存放文件 + 自动备份）
1. 打开 https://github.com ，点 **Sign up**，用邮箱注册。
2. 登录后点右上角 **+** → **New repository**。
3. Repository name 填 `my-kitchen`（随便起）。
4. **一定选 Private（私有）**。
5. 点 **Create repository**。

### 第 2 步：把网站文件传上去
1. 解压我给你的 `recipe-site.zip`。
2. 在刚建好的仓库页面，点 **uploading an existing file**（或 **Add file → Upload files**）。
3. 把解压后文件夹**里面的所有东西**（menu.md、recipes 文件夹、images 文件夹、src 文件夹、build.mjs、README.md、.gitignore）一起拖进网页。
   - 注意：拖的是里面的内容，不是外层的 recipe-site 文件夹本身。
4. 最下面点 **Commit changes**。
5. GitHub 网页一次最多 100 个文件。`tools` 文件夹（插画原图和制作脚本，网站运行不需要）文件较多，第一次先不拖它；网站上线后再单独上传一次 `tools` 文件夹作为备份即可。

### 第 3 步：注册 Cloudflare 并发布网站
1. 打开 https://dash.cloudflare.com/sign-up 注册（免费，不用绑卡）。
2. 左侧菜单找 **Workers & Pages** → **Create** → 选 **Pages** 标签 → **Connect to Git**。
3. 授权 GitHub，选 `my-kitchen` 仓库 → **Begin setup**。
4. 填写：
   | 选项 | 填什么 |
   |---|---|
   | Project name | `cailey-kitchen`（会变成网址 cailey-kitchen.pages.dev） |
   | Framework preset | None |
   | Build command | `node build.mjs` |
   | Build output directory | `dist` |
5. 展开 **Environment variables (advanced)**，添加两个：
   | Variable name | Value |
   |---|---|
   | `RECIPE_PASSWORD` | 编辑密码（自己想一个，至少 10 位，字母加数字） |
   | `NODE_VERSION` | `20` |
   | `GH_REPO` | `你的GitHub用户名/my-kitchen` |
   | `GH_TOKEN` | GitHub 访问令牌（见第四部分「生成访问令牌」），类型选 **Secret** |
6. 点 **Save and Deploy**，等 1–2 分钟，出现 **Success** 后点上面的链接，网站就上线了。

> 网址名被占用的话换一个 Project name。想要自己的域名（如 caileykitchen.com），可以在 Cloudflare 买（约 10 美元/年），在项目的 **Custom domains** 里一键绑定。

### 第 4 步：试一下
- 打开网址，所有食谱和用量都能直接看。
- 右上角小钥匙 → 输入编辑密码 → 直接进入编辑模式。同一台设备会记住密码；换手机要再输一次。
- 网站设置了不被搜索引擎收录（noindex），只有知道链接的人能打开。

---

## 第二部分：以后怎么加菜（不用 AI，手机也能做）

### A. 只加一个菜名
1. GitHub 仓库里点开 `menu.md` → 右上角铅笔图标 **Edit**。
2. 在对应小分类那行后面加：`, 新菜名 / English Name`（想标推荐就写 `★新菜名 / English Name`）。斜杠后面是英文版网站显示的名字，不写的话英文版显示中文。
3. 点 **Commit changes**。1–2 分钟后网站自动更新。

### B. 写一篇完整食谱
1. 进入 `recipes` 文件夹 → **Add file → Create new file**。
2. 文件名写 `recipes/菜名.md`（菜名必须和 menu.md 里的一字不差，★不用写）。
3. 打开 `_模板.md` 复制内容粘贴进来，按格式填：
   ```
   # 草莓抹茶拿铁
   英文: Strawberry Matcha Latte
   时间: 10分钟
   份量: 1杯
   Time: 10 min
   Serves: 1 glass
   图片: images/strawberry-matcha.jpg
   评分: 5

   ## 食材
   - 抹茶粉 | 4g
   - 草莓 | 5颗

   ## 步骤
   1. 草莓压成泥加 10g 糖……

   ## 注意
   - 草莓要熟透才甜

   ## Ingredients
   - Matcha powder | 4g
   - Strawberries | 5

   ## Steps
   1. Mash the strawberries with 10g sugar...

   ## Tips
   - Use very ripe strawberries

   ## 评价
   - 2026-11-02 | 5 | 草莓多放一点更好
   ```
4. **Commit changes**。

规则小结：
- `## Ingredients / ## Steps / ## Tips` 是英文版内容，可以不写；不写的话英文网站会显示中文内容并加一行说明。偷懒办法：把中文部分发给任何 AI，说「按同样格式翻译成英文段落」。
- 右上角「中 / EN」切换语言，浏览器会记住上次的选择。
- 食材每行是 `名称 | 用量`。
- 偷懒办法：在网站上点任何一道还没写的浅色菜，页面里有现成模板和「复制模板」按钮。
- 网上找到的食谱：把链接或截图发给任何 AI，说「按这个格式整理成食谱」，粘贴即可。

### C. 加成品照片
1. 拍照后用 https://squoosh.app 压缩：宽度 1200，格式 WebP 或 JPG，控制在 300KB 以内。
2. 文件名用英文或拼音，例如 `fanqie-niunan.jpg`。
3. GitHub 里进入 `images` 文件夹 → **Add file → Upload files** 上传。
4. 在食谱文件里写 `图片: images/fanqie-niunan.jpg`。

### D. 写评价
在食谱最下面 `## 评价` 加一行：`- 日期 | 几颗星 | 感受`。网站会显示星星，并在顶部自动算平均分（如果没填「评分」）。

---

## 第四部分：直接在网页里编辑（编辑模式）

设置好之后，改食谱、加菜、删菜、传照片都可以直接在网站上完成，不用再打开 GitHub。

### 一次性设置：生成访问令牌（约 3 分钟）
1. 登录 GitHub，点右上角头像 → **Settings**。
2. 左侧菜单拉到最下面 → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**。
3. 填写：
   | 选项 | 填什么 |
   |---|---|
   | Token name | `kitchen-edit` |
   | Expiration | 选最长的期限（到期后再生成一个新的就行） |
   | Repository access | **Only select repositories** → 选你的 `my-kitchen` |
   | Permissions → Repository permissions → **Contents** | **Read and write** |
4. 点 **Generate token**，复制那串以 `github_pat_` 开头的字符。它只显示这一次，先粘到备忘录里。

### 把令牌交给 Cloudflare（只做一次）
1. Cloudflare → 你的项目 → **Settings → Variables and Secrets** → **Add**。
2. 添加 `GH_TOKEN`，类型选 **Secret**，值粘贴刚才那串 `github_pat_...`。
3. 添加 `GH_REPO`，值填 `你的GitHub用户名/my-kitchen`（类型 Text 即可）。
4. 到 **Deployments**，最新一次右边 **⋯ → Retry deployment**。

令牌只存在 Cloudflare 的服务器上，网页和浏览器里都没有，别人看不到。

### Recipe Lab（食谱实验室）
目录最下面的 **Recipe Lab**：任何人都能输入一个菜名，AI 现场写一份做法，中英文都有。只是看看，不会保存，生成下一道菜时上一道就消失。它和下面的「一键生成」共用同一个 Workers AI 绑定和每天的免费额度。

### 打开「一键生成食谱」（可选，免费）
编辑页最上面的「✨ 按菜名一键生成食谱」用的是 Cloudflare 自带的 Workers AI，每天有免费额度（大约够生成二三十篇），不用绑卡。
1. Cloudflare → 你的项目 → **Settings → Bindings** → **Add** → 选 **Workers AI**。
2. Variable name 填 `AI`，保存。
3. 到 **Deployments**，最新一次右边 **⋯ → Retry deployment**。

用法：先用「+ 加菜」加好菜名，点开这道菜，点「✨ 按菜名一键生成食谱」，等十几秒，中英文的食材、步骤、小贴士都会填好。AI 写的不一定完全准确，检查一下再保存；不满意可以点「撤销」或者再生成一次。

### 在网站上打开编辑模式
打开网站 → 右上角小钥匙 → 输入编辑密码 → 编辑模式直接打开，屏幕下方出现「编辑模式」小条。同一台设备以后会自动进入。想退出，点小条上的「退出编辑」。

### 怎么用
- **改食谱**：点任何一道菜，详情页会变成表单。食材、用量、步骤、注意、英文版、评分、评价都能改，每行可以上移、下移或删除。改完点 **保存到网站**。
- **写新食谱**：点一道浅色（还没写）的菜，直接填写后保存。
- **加菜**：首页每个小分类最后有「+ 加菜」，填中文名（英文名可选），勾选要不要推荐。
- **删菜**：在那道菜的编辑页最下面点「删除这道菜」，再确认一次。
- **传照片**：在编辑页点「选择照片」，手机可以直接拍或从相册选。网页会自动压缩到 1200 像素宽再上传。
- **推荐 / 英文名**：在编辑页改，会自动同步到 menu.md。
- 每次保存都会在 GitHub 留一条记录，网站大约 1–2 分钟后更新。改错了，可以在 GitHub 仓库的 **History** 里找回旧版本。

### 安全提示
- 谁知道编辑密码，谁就能改网站，所以密码要够长（至少 10 位，字母加数字），不要用生日这类 4 位数字。
- 在别人的电脑上编辑完，记得点「退出编辑」，那台设备就不会再记住密码。
- 担心密码泄露：在 Cloudflare 改 `RECIPE_PASSWORD` 再 Retry deployment，旧密码立刻失效。
- 担心令牌泄露：到 GitHub → Settings → Developer settings → Fine-grained tokens 删掉 `kitchen-edit`，再生成一个新的填进 `GH_TOKEN`。
- 我发给你的「网站预览.html」不能编辑，编辑只能在部署好的网站上用。

### 网页里暂时不能做的
- 给菜改名、新建或删除大类和小分类：这些还是要在 GitHub 上改 `menu.md`（第二部分 A）。

---

## 第三部分：常见问题

**改编辑密码？** Cloudflare → 你的项目 → **Settings → Variables and Secrets** → 改 `RECIPE_PASSWORD` → 到 **Deployments** 点最新一次右边的 **⋯ → Retry deployment**。旧密码立刻失效。

**备份在哪？** GitHub 保存了每一次修改的历史（点任何文件的 **History**）。想再多一份：仓库首页 **Code → Download ZIP** 存到网盘，每月一次即可。

**网站坏了？** Cloudflare → **Deployments** 看最新一次是不是红色 Failed，点进去看日志：
- `在 menu.md 里找不到同名的菜` → 食谱标题和 menu.md 的菜名不一致。
- `图片 … 不存在` → 图片路径或文件名写错了。
- 想回到之前好的版本：在旧的部署右边点 **⋯ → Rollback**。

**要改设计（颜色、字体、加功能）？** 把仓库 **Download ZIP** 发给 Claude，说清楚想改什么，让它改 `src/template.html`，再把改好的文件上传覆盖。这是唯一可能需要 AI 订阅的时候，平时加菜完全不需要。

**想用 AI 画成品图/插画？** 用即梦免费额度。保持画风统一的提示词：
> 扁平手绘插画，奶油色背景，柔和粉彩配色，深棕色细描边，日系可爱风，带腮红的小表情，居中构图，干净留白，无文字 ＋ 主体（例如：一碗番茄牛腩，冒着热气）

生成后下载，按上面「C. 加成品照片」的步骤放进去。

**在自己电脑上预览？**（可选）安装 https://nodejs.org ，在文件夹里打开终端运行 `node build.mjs`，然后双击 `dist/index.html`。没设置密码时默认密码是 `cailey`。

---

## 费用
| 项目 | 费用 |
|---|---|
| GitHub 私有仓库 | 免费 |
| Cloudflare Pages 托管 | 免费（流量不限） |
| 自定义域名（可选） | 约 10 美元/年 |
| AI 改设计（偶尔） | 需要时订一个月 |
