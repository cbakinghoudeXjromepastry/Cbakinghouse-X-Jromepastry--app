# Vercel 部署指南

## 🚀 快速部署

### 方法 1: 通过 GitHub (推荐)

#### 步骤 1: 将文件添加到 GitHub

```bash
# 打开你的项目目录
cd cbakinghouse-x-jromepastry-app

# 复制 customer-app.html 和 admin.html 到项目根目录
# (确保它们在与 package.json 同级的地方)

# 提交并推送
git add customer-app.html admin.html
git commit -m "Add customer app and admin dashboard"
git push origin main
```

#### 步骤 2: Vercel 自动部署

- Vercel 会自动检测到 GitHub 更新
- 自动构建并部署
- 大约 1-2 分钟完成

#### 步骤 3: 访问应用

```
https://cbakinghouse-x-jromepastry-app.vercel.app/customer-app.html
https://cbakinghouse-x-jromepastry-app.vercel.app/admin.html
```

---

### 方法 2: 通过 Vercel CLI (如果需要)

```bash
# 全局安装 Vercel CLI
npm i -g vercel

# 进入项目目录
cd cbakinghouse-x-jromepastry-app

# 部署
vercel

# 按提示选择是否为生产部署
```

---

## 📁 项目结构

```
cbakinghouse-x-jromepastry-app/
├── customer-app.html      ← 顾客应用
├── admin.html             ← 管理后台
├── package.json
├── vercel.json           (可选)
└── ... (其他文件)
```

---

## ⚙️ 配置 vercel.json (可选但推荐)

在项目根目录创建 `vercel.json`:

```json
{
  "buildCommand": "exit 0",
  "devCommand": "npx http-server",
  "installCommand": "npm install",
  "framework": null,
  "rewrites": [
    {
      "source": "/customer-app",
      "destination": "/customer-app.html"
    },
    {
      "source": "/admin",
      "destination": "/admin.html"
    }
  ]
}
```

这样可以让你通过更简洁的 URL 访问：
- `https://cbakinghouse-x-jromepastry-app.vercel.app/customer-app`
- `https://cbakinghouse-x-jromepastry-app.vercel.app/admin`

---

## 🔗 自定义域名 (未来)

当你购买自己的域名时：

### 步骤 1: 在 Vercel 中添加域名

1. 进入 https://vercel.com/cbakinghouse-xj-romepastry/cbakinghouse-x-jromepastry-app
2. 点击 **Settings** → **Domains**
3. 输入你的域名（例如：`order.cbakinghouse.com`）
4. 点击 **Add**

### 步骤 2: 配置 DNS

Vercel 会给你 DNS 设置说明，按照指示在你的域名提供商那里配置即可

### 步骤 3: 等待生效

DNS 生效需要 24-48 小时

---

## 🧪 测试清单

部署后，请测试以下功能：

### 顾客端 (Customer App)

- [ ] 打开应用，看到首页
- [ ] Welcome Popup 显示
- [ ] 能浏览 Cbakinghouse 商品
- [ ] 能浏览 J.ROME Pastry 商品
- [ ] 能添加商品到购物车
- [ ] 能修改数量
- [ ] 能删除商品
- [ ] 能进入 Checkout
- [ ] 能选择 Pickup/Delivery
- [ ] 输入邮编后能自动计算运费
- [ ] 能看到 QR Code
- [ ] 能看到最终总额
- [ ] 提交订单后能看到订单号
- [ ] 能在 Track Order 查询订单
- [ ] WhatsApp 按钮能打开 WhatsApp

### Admin 端

- [ ] 能进入 Admin Dashboard
- [ ] 能看到 Dashboard 统计
- [ ] 能查看订单列表
- [ ] 能查看订单详情
- [ ] 能添加商品
- [ ] 能修改商品价格
- [ ] 能上传商品照片
- [ ] 能管理分类
- [ ] 能管理配送区域
- [ ] 能上传 QR Code
- [ ] 能修改 Announcement
- [ ] 能修改 Welcome Popup

---

## 🔧 常见问题

### Q: 文件上传后还是 404 错误

**A:** 
1. 确认文件名正确（小写）
2. 确认文件在项目根目录
3. 等待 Vercel 部署完成（刷新页面）
4. 检查浏览器缓存

```bash
# 清除缓存后重新访问
```

### Q: Supabase 连接失败

**A:**
1. 检查网络连接
2. 确认 SUPABASE_URL 和 SUPABASE_KEY 正确
3. 打开浏览器开发者工具 (F12) 查看错误信息
4. 检查 Supabase 项目是否还在线

### Q: QR Code 不显示

**A:**
1. 确认在 Admin 中上传了 QR Code
2. 等待 30 秒让图片在 Supabase 存储中生效
3. 刷新页面
4. 检查浏览器开发者工具看是否有加载错误

### Q: 如何回滚到之前的版本

**A:**
在 Vercel 控制面板中：
1. 点击 **Deployments**
2. 找到之前的版本
3. 点击 **Redeploy**

### Q: 想禁用 Welcome Popup

**A:**
在 Admin Dashboard → Settings → Welcome Popup → 取消勾选 "Enable Popup"

---

## 🛡️ 安全检查清单

- [ ] Supabase Key 没有暴露在客户端
- [ ] 所有价格计算都由 backend 做
- [ ] 顾客不能修改 localStorage 来改价格
- [ ] 顾客不能查看其他顾客订单
- [ ] Admin 认证已实现（如果需要）

---

## 📊 监控和维护

### 定期检查

- **每天**: 查看新订单，验证支付
- **每周**: 检查库存，更新商品
- **每月**: 查看销售报告，优化价格/分类

### Vercel 监控

访问 https://vercel.com/analytics 查看：
- 应用访问量
- 页面加载速度
- 错误率

### Supabase 监控

访问 https://app.supabase.com 查看：
- 数据库使用量
- API 请求数
- 存储空间

---

## 🆘 获得帮助

如果遇到问题：

1. **检查浏览器控制台** (F12 → Console)
2. **查看 Vercel 日志** (Vercel Dashboard → Deployments → 选择最新部署 → Logs)
3. **查看 Supabase 日志** (Supabase Console → Logs)

---

## 📝 后续优化 (未来可做)

- [ ] 添加用户认证系统
- [ ] 实现真实支付网关（Stripe/Xendit）
- [ ] 添加电子邮件通知
- [ ] 实现 SMS 订单提醒
- [ ] 添加评价和评分系统
- [ ] 实现积分/会员系统
- [ ] 添加多语言支持
- [ ] 改进 SEO

---

## 🎉 完成！

现在你有了一个完整的在线面包店应用系统！

继续维护、添加新商品、与顾客交流，一切就会慢慢发展起来 🤍

