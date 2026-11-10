# Cailey's Kitchen

🍳 **https://cailey-kitchen.pages.dev**

[中文说明](#中文说明) · [English](#english)

---

# 中文说明

一个免费、自动备份的私房食谱网站。所有人都能看食谱和用量；输入密码才能编辑。

```
my-kitchen/
├── menu.md            ← 目录：所有分类和菜名（改这里就能加菜、改顺序）
├── recipes/           ← 每道菜一个 .md 文件（完整食谱）
│   └── _模板.md        ← 新菜谱从这里复制
├── images/            ← 成品照片（ai-pan.webp 是 AI 菜谱的默认小平底锅）
├── src/template.html  ← 网站的样子（颜色、字体、动画、小女孩和小猫）
├── build.mjs          ← 把上面的东西变成网站
├── functions/api/     ← 网站后台：检查编辑密码、代你保存到 GitHub、调用 AI
└── tools/             ← 插画原图和制作脚本（网站运行不需要）
```

---

## 一、网站有什么

- **目录和搜索**：首页按分类列出所有菜。右上角放大镜可以按菜名或食材搜索，中文英文都行，多个词用空格隔开。
- **中 / EN**：右上角切换语言，浏览器会记住你的选择。
- **Recipe Lab（食谱实验室）**：目录最下面。任何人输入一个菜名，AI 现场写一份中英文做法。只是看看，不会保存，生成下一道菜时上一道就消失。
- **编辑模式**：右上角小钥匙 → 输入编辑密码，就能直接在网页上改食谱、加菜、删菜、传照片。

---

## 二、编辑模式怎么用

打开网站 → 右上角小钥匙 → 输入编辑密码 → 屏幕下方出现「编辑模式」小条。同一台设备以后会自动进入；想退出点小条上的「退出编辑」。

- **改食谱**：点任何一道菜，详情页会变成表单。改完点 **保存到网站**，大约 1–2 分钟后网站更新。
- **一键生成**：编辑页最上面的 🍳「一键生成」按钮，按菜名自动填好中英文的食材、用量、步骤、小贴士。还没有照片的话，会先用小平底锅图片。AI 写的不一定完全准确，保存前检查一下；不满意可以点「撤销」或者再生成一次。
- **粘贴菜谱**：从网上复制一整篇菜谱，粘贴到「粘贴菜谱，自动填写」框里，点「识别并填入」，时间、份量、食材、步骤、小贴士会自动放到对应位置。
- **调整每一行**：按住一行不动约半秒（出现蓝色边框），上下拖动可以换顺序；往左滑会露出「删除」，点一下删掉这一行。直接点输入框就能改文字。
- **加菜**：首页每个小分类最后有「+ 加菜」。
- **删菜**：在那道菜的编辑页最下面点「删除这道菜」，弹窗里再确认一次。
- **传照片**：在编辑页点「选择照片」，手机可以直接拍或从相册选，会自动压缩到 1200 像素宽。
- **推荐 / 英文名 / 换分类**：在编辑页改。「分类」下拉框可以把这道菜挪到别的小分类，食谱跟着走。
- **小分类**：标题旁的 ✎ 可以改名或删除；删除时里面的菜会整体移到你选的另一个小分类。每个大类最后有「+ 加小分类」。
- **大类**：大标题旁的 ✎ 可以改中英文名、介绍、小标签和插画，或者删除；删除时里面的小分类和菜会移到你选的另一个大类，编号自动重排。最后一页有「+ 加一个大类」。
- **中英文**：上面用中文或英文写都可以，保存时自动翻译出另一种语言。单位统一写成 g、ml、kg、min、h。
- 每次保存都会在 GitHub 留一条记录。改错了，可以在仓库的 **History** 里找回旧版本。

网页里暂时不能做的：给菜改名，调整大类、小分类的先后顺序。这些要在 GitHub 上改 `menu.md`（见第四部分 A）。

---

## 三、Cloudflare 设置（网站后台）

位置：Cloudflare → **Workers & Pages** → `cailey-kitchen` → **Settings**。

### 构建设置
| 选项 | 值 |
|---|---|
| Build command | `node build.mjs` |
| Build output directory | `dist` |

### Variables and Secrets
| 名字 | 类型 | 值 |
|---|---|---|
| `RECIPE_PASSWORD` | Secret 或 Text | 编辑密码（至少 12 位，字母加数字） |
| `GH_TOKEN` | **Secret** | GitHub 访问令牌（见下面） |
| `GH_REPO` | Text | `TabbyMao/my-kitchen` |
| `NODE_VERSION` | Text | `20` |

### Bindings
| 类型 | Variable name |
|---|---|
| Workers AI | `AI` |

Workers AI 是「一键生成」和 Recipe Lab 用的，两个功能共用每天的免费额度（大约够生成二三十道菜）。免费额度用完当天就停，不会扣钱，第二天恢复。

**改了任何设置之后**：到 **Deployments** → 最新一次右边 **⋯ → Retry deployment**，新设置才会生效。

### 生成 GitHub 访问令牌
1. GitHub 右上角头像 → **Settings** → 左侧最下面 **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**。
2. Token name：`kitchen-edit`；Expiration：选最长或 No expiration。
3. Repository access：**Only select repositories** → 只选 `my-kitchen`。
4. Permissions：**+ Add permissions** → 勾 **Contents** → 改成 **Read and write**（Metadata: Read-only 是自动加的）。
5. **Generate token**，复制 `github_pat_...`，填进 Cloudflare 的 `GH_TOKEN`。填完就把备忘录里的那份删掉。

---

## 四、直接在 GitHub 上改（不用编辑模式）

### A. 只加一个菜名
1. 仓库里点开 `menu.md` → 右上角铅笔 **Edit**。
2. 在对应小分类那行后面加：`, 新菜名 / English Name`。想标推荐就写 `★新菜名 / English Name`。
3. **Commit changes**，1–2 分钟后网站自动更新。

### B. 写一篇完整食谱
1. 进入 `recipes` 文件夹 → **Add file → Create new file**。
2. 文件名写 `菜名.md`（必须和 menu.md 里的菜名一字不差，★ 不用写）。
3. 按这个格式填：
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

`## Ingredients / Steps / Tips` 是英文版，可以不写，英文网站会自动翻译。

### C. 加成品照片
1. 用 https://squoosh.app 压缩：宽度 1200，WebP 或 JPG，300KB 以内。
2. 文件名用英文或拼音，例如 `fanqie-niunan.jpg`。
3. 上传到 `images` 文件夹，在食谱里写 `图片: images/fanqie-niunan.jpg`。

### 上传文件的小提醒
- GitHub 网页一次最多传 100 个文件。
- 下载 `[[path]].js` 时，浏览器有时会把文件名改成 `path.js`。上传前要改回 `[[path]].js`，并放在 `functions/api` 文件夹里。

---

## 五、安全

- 谁知道编辑密码，谁就能改网站。密码要够长，不要用生日这类短数字。
- GitHub 和 Cloudflare 两个账号都打开**两步验证（2FA）**。
- 访问令牌只存在 Cloudflare 里，网页和浏览器里都没有。不要截图，也不要发给任何人。
- 在别人的电脑上编辑完，记得点「退出编辑」。
- 担心密码泄露：改 `RECIPE_PASSWORD` → Retry deployment，旧密码马上失效。
- 担心令牌泄露：在 GitHub 删掉 `kitchen-edit`，再生成一个新的填进 `GH_TOKEN` → Retry deployment。
- 仓库建议保持 **Private**。

---

## 六、常见问题

**改编辑密码？** Cloudflare → Settings → Variables and Secrets → 改 `RECIPE_PASSWORD` → Deployments → **⋯ → Retry deployment**。

**备份在哪？** GitHub 保存了每一次修改（任何文件的 **History**）。想再多一份：仓库首页 **Code → Download ZIP**，每月存一次到网盘。

**网站坏了？** Cloudflare → **Deployments** 看最新一次是不是红色 Failed，点进去看日志：
- `在 menu.md 里找不到同名的菜` → 食谱标题和 menu.md 的菜名不一致。
- `图片 … 不存在` → 图片路径或文件名写错了。
- 想回到之前好的版本：在旧的部署右边点 **⋯ → Rollback**。

**编辑模式提示「网站还没设置好编辑功能」？** `GH_TOKEN` 或 `GH_REPO` 没填，或者填完没 Retry deployment。

**一键生成 / Recipe Lab 提示「网站还没打开 AI 功能」？** Bindings 里没加 Workers AI（变量名 `AI`），或者加完没 Retry deployment。

**保存时提示 Bad credentials？** 令牌过期或填错了，重新生成一个替换 `GH_TOKEN`。

**要改设计或加功能？** 把仓库 **Download ZIP** 发给 Claude，说清楚想改什么，再把改好的文件上传覆盖。

**在自己电脑上预览？**（可选）安装 https://nodejs.org ，在文件夹里打开终端运行 `node build.mjs --inline`，然后双击 `dist/preview.html`。预览里不能编辑，也不能用 AI。

---

## 费用
| 项目 | 费用 |
|---|---|
| GitHub 私有仓库 | 免费 |
| Cloudflare Pages 托管 | 免费 |
| Workers AI（一键生成、Recipe Lab） | 每天免费额度内免费 |
| 自定义域名（可选） | 约 10 美元/年 |

<p align="right"><a href="#caileys-kitchen">↑ 回到顶部</a></p>

---

# English

A free, automatically backed-up personal recipe site. Anyone can read the recipes and amounts; editing needs a password.

```
my-kitchen/
├── menu.md            ← Contents: every category and dish (edit here to add dishes or reorder)
├── recipes/           ← One .md file per dish (the full recipe)
│   └── _模板.md        ← Template for new recipes
├── images/            ← Dish photos (ai-pan.webp is the default little pan for AI recipes)
├── src/template.html  ← How the site looks (colours, fonts, animations, the girl and the cat)
├── build.mjs          ← Turns all of the above into the website
├── functions/api/     ← Site backend: checks the edit password, saves to GitHub for you, calls the AI
└── tools/             ← Original artwork and scripts (not needed to run the site)
```

---

## 1. What the site does

- **Contents and search**: the home page lists every dish by category. The magnifier in the top-right corner searches dish names and ingredients, in Chinese or English. Separate words with spaces to match all of them.
- **中 / EN**: switch the language in the top-right corner; the browser remembers your choice.
- **Recipe Lab**: at the bottom of the contents. Anyone can type a dish name and the AI writes a recipe on the spot, in Chinese and English. It's only for looking: nothing is saved, and it's replaced by the next dish you generate.
- **Edit mode**: tap the key in the top-right corner and enter the edit password to change recipes, add or delete dishes and upload photos right on the page.

---

## 2. Using edit mode

Open the site → key in the top-right corner → enter the edit password → an "Edit mode" bar appears at the bottom. The same device signs in automatically next time; tap "Exit editing" on the bar to leave.

- **Edit a recipe**: open any dish and its page turns into a form. Tap **Save to site**; the site updates in about 1–2 minutes.
- **Generate**: the 🍳 "Generate" button at the top of the editor fills in ingredients, amounts, steps and tips in both languages from the dish name. If there's no photo yet, the little pan picture is used. AI can get things wrong, so check before saving; tap "Undo" or generate again if you don't like it.
- **Paste a recipe**: copy a whole recipe from any website, paste it into "Paste a recipe to fill the form" and tap "Fill in". Time, servings, ingredients, steps and tips go into the right places.
- **Rows**: press and hold a row for about half a second (a blue outline appears), then drag up or down to reorder. Swipe a row left to reveal "Delete", then tap it. Tap a field to edit the text.
- **Add a dish**: every subcategory on the home page ends with "+ Add dish".
- **Delete a dish**: "Delete this dish" at the bottom of its editor, then confirm in the pop-up.
- **Photos**: "Choose photo" in the editor; on a phone you can take one or pick from the album. It's resized to 1200px wide automatically.
- **Recommended / English name / category**: change them in the editor. The "Category" menu moves the dish into another section; its recipe goes with it.
- **Sections**: the ✎ next to a section title renames or deletes it; when deleting, its dishes move to another section you choose. Each category ends with "+ Add section".
- **Categories**: the ✎ next to a category title changes its Chinese and English names, intro, tag and illustration, or deletes it; when deleting, its sections and dishes move to another category you choose, and the numbers are renumbered. The last page has "+ Add a category".
- **Languages**: write in Chinese or English; saving translates into the other language. Units are always g, ml, kg, min, h.
- Every save is recorded in GitHub. If something goes wrong, restore an older version from the repository's **History**.

Not possible on the page yet: renaming a dish, or reordering categories and sections. Do those in `menu.md` on GitHub (see section 4A).

---

## 3. Cloudflare settings (site backend)

Where: Cloudflare → **Workers & Pages** → `cailey-kitchen` → **Settings**.

### Build
| Setting | Value |
|---|---|
| Build command | `node build.mjs` |
| Build output directory | `dist` |

### Variables and Secrets
| Name | Type | Value |
|---|---|---|
| `RECIPE_PASSWORD` | Secret or Text | Edit password (12+ characters, letters and numbers) |
| `GH_TOKEN` | **Secret** | GitHub access token (see below) |
| `GH_REPO` | Text | `TabbyMao/my-kitchen` |
| `NODE_VERSION` | Text | `20` |

### Bindings
| Type | Variable name |
|---|---|
| Workers AI | `AI` |

Workers AI powers "Generate" and Recipe Lab. Both share the daily free allowance (roughly 20–30 dishes a day). When it runs out it simply stops for the day, with no charge, and resets the next day.

**After changing any setting**: go to **Deployments** → **⋯ → Retry deployment** on the latest one so the new setting takes effect.

### Creating the GitHub access token
1. GitHub avatar (top right) → **Settings** → **Developer settings** (bottom left) → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
2. Token name: `kitchen-edit`; Expiration: the longest option, or No expiration.
3. Repository access: **Only select repositories** → only `my-kitchen`.
4. Permissions: **+ Add permissions** → tick **Contents** → set it to **Read and write** (Metadata: Read-only is added automatically).
5. **Generate token**, copy the `github_pat_...` string and paste it into `GH_TOKEN` in Cloudflare. Then delete any copy you kept in your notes.

---

## 4. Editing directly on GitHub (without edit mode)

### A. Add just a dish name
1. Open `menu.md` in the repository → pencil icon **Edit**.
2. At the end of the right subcategory line, add `, 新菜名 / English Name`. To mark it as recommended, write `★新菜名 / English Name`.
3. **Commit changes**; the site updates in 1–2 minutes.

### B. Write a full recipe
1. Open the `recipes` folder → **Add file → Create new file**.
2. Name it `菜名.md`, exactly matching the dish name in menu.md (leave out the ★).
3. Use this format:
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
4. **Commit changes**.

`## Ingredients / Steps / Tips` is the English version. It's optional; the English site translates automatically if it's missing.

### C. Add a dish photo
1. Compress it with https://squoosh.app: 1200px wide, WebP or JPG, under 300KB.
2. Use an English or pinyin file name, e.g. `fanqie-niunan.jpg`.
3. Upload it to the `images` folder and add `图片: images/fanqie-niunan.jpg` to the recipe.

### Upload reminders
- GitHub's web page uploads at most 100 files at a time.
- When you download `[[path]].js`, the browser sometimes renames it to `path.js`. Rename it back to `[[path]].js` before uploading, and put it in the `functions/api` folder.

---

## 5. Security

- Anyone who knows the edit password can change the site. Use a long password, not a birthday or another short number.
- Turn on **two-factor authentication (2FA)** for both GitHub and Cloudflare.
- The access token lives only in Cloudflare, never in the page or the browser. Don't screenshot it or send it to anyone.
- After editing on someone else's computer, tap "Exit editing".
- Worried the password leaked? Change `RECIPE_PASSWORD` → Retry deployment; the old one stops working right away.
- Worried the token leaked? Delete `kitchen-edit` on GitHub, create a new one, put it in `GH_TOKEN` → Retry deployment.
- Keep the repository **Private**.

---

## 6. FAQ

**Change the edit password?** Cloudflare → Settings → Variables and Secrets → edit `RECIPE_PASSWORD` → Deployments → **⋯ → Retry deployment**.

**Where are the backups?** GitHub keeps every change (any file's **History**). For an extra copy: repository home → **Code → Download ZIP**, once a month, saved to cloud storage.

**Site broken?** Cloudflare → **Deployments**; if the latest one is a red Failed, open it and read the log:
- `在 menu.md 里找不到同名的菜` → the recipe title doesn't match the dish name in menu.md.
- `图片 … 不存在` → wrong image path or file name.
- To go back to a good version: **⋯ → Rollback** on an older deployment.

**Edit mode says editing isn't set up?** `GH_TOKEN` or `GH_REPO` is missing, or you didn't Retry deployment after adding them.

**Generate / Recipe Lab says AI isn't enabled?** The Workers AI binding (variable name `AI`) is missing, or you didn't Retry deployment after adding it.

**"Bad credentials" when saving?** The token expired or was pasted wrong; create a new one and replace `GH_TOKEN`.

**Want to change the design or add a feature?** Download the repository ZIP, send it to Claude with what you want changed, then upload the updated files.

**Preview on your own computer?** (Optional) Install https://nodejs.org , open a terminal in the folder, run `node build.mjs --inline`, then double-click `dist/preview.html`. The preview can't edit or use the AI.

---

## Costs
| Item | Cost |
|---|---|
| GitHub private repository | Free |
| Cloudflare Pages hosting | Free |
| Workers AI (Generate, Recipe Lab) | Free within the daily allowance |
| Custom domain (optional) | About $10/year |

<p align="right"><a href="#caileys-kitchen">↑ Back to top</a></p>
