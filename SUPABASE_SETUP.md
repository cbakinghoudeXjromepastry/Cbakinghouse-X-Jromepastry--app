# Supabase 配置指南

## 📋 概览
你的应用需要以下数据库表和存储桶：

- `products` - 商品
- `orders` - 订单
- `categories` - 分类
- `delivery_zones` - 配送区域
- `settings` - 系统设置
- 存储桶：`qr-codes` - QR码和照片存储

---

## 🔑 Supabase 项目信息
- **Project Ref**: `sinkciczsxumobxmuvwa`
- **URL**: `https://sinkciczsxumobxmuvwa.supabase.co`
- **Anon Key**: 已在代码中配置

---

## 📊 创建数据表

### 1. Products 表

```sql
create table products (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  description text,
  brand text not null, -- 'cbh' 或 'jrome'
  category text not null,
  price decimal(10,2) not null,
  image_url text,
  available boolean default true,
  display_order integer default 0,
  created_at timestamp default now(),
  updated_at timestamp default now()
);

-- 创建索引
create index idx_products_brand on products(brand);
create index idx_products_category on products(category);
create index idx_products_available on products(available);
```

### 2. Orders 表

```sql
create table orders (
  id uuid primary key default gen_random_uuid(),
  sequence integer,
  order_number text unique,
  customer_name text not null,
  customer_phone text not null,
  items jsonb not null, -- 存储商品数组
  subtotal decimal(10,2) not null,
  delivery_fee decimal(10,2) default 0,
  total decimal(10,2) not null,
  delivery_type text, -- 'pickup' 或 'delivery'
  address text,
  postcode text,
  note text,
  payment_status text default 'pending', -- 'pending', 'paid', 'rejected'
  payment_proof_url text,
  order_status text default 'order_received', -- 'order_received', 'confirmed', 'preparing', 'ready', 'completed', 'cancelled'
  brand text, -- 'cbh' 或 'jrome'
  created_at timestamp default now(),
  updated_at timestamp default now()
);

-- 创建索引
create index idx_orders_customer_phone on orders(customer_phone);
create index idx_orders_order_number on orders(order_number);
create index idx_orders_payment_status on orders(payment_status);
create index idx_orders_order_status on orders(order_status);
create index idx_orders_created_at on orders(created_at);
```

### 3. Categories 表

```sql
create table categories (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  brand text not null, -- 'cbh' 或 'jrome'
  created_at timestamp default now(),
  unique(name, brand)
);

-- 创建索引
create index idx_categories_brand on categories(brand);
```

### 4. Delivery Zones 表

```sql
create table delivery_zones (
  id uuid primary key default gen_random_uuid(),
  area text not null,
  postcode text not null,
  fee decimal(10,2) not null,
  available boolean default true,
  created_at timestamp default now(),
  updated_at timestamp default now()
);

-- 创建索引
create index idx_delivery_zones_postcode on delivery_zones(postcode);
```

### 5. Settings 表

```sql
create table settings (
  id uuid primary key default gen_random_uuid(),
  key text unique not null,
  value text,
  title text,
  description text,
  image_url text,
  enabled boolean default true,
  created_at timestamp default now(),
  updated_at timestamp default now()
);
```

---

## 💾 创建存储桶

### QR Codes & Images Bucket

1. 进入 Supabase 控制台
2. 点击 **Storage**
3. 创建新 Bucket：
   - **Bucket Name**: `qr-codes`
   - **Public bucket**: 是
4. 点击 **Create**

---

## 📸 初始化示例数据

### 添加示例分类

```sql
-- Cbakinghouse 分类
insert into categories (name, brand) values
  ('Bagels', 'cbh'),
  ('Sea Salt Rolls', 'cbh'),
  ('Bread', 'cbh'),
  ('Sourdough', 'cbh'),
  ('Waffles', 'cbh'),
  ('Other Bakery', 'cbh');

-- J.ROME Pastry 分类
insert into categories (name, brand) values
  ('Basque Cheesecake', 'jrome'),
  ('Daifuku', 'jrome'),
  ('Dubai Chocolate', 'jrome'),
  ('Cakes', 'jrome'),
  ('Desserts', 'jrome'),
  ('Other Desserts', 'jrome');
```

### 添加示例配送区域

```sql
insert into delivery_zones (area, postcode, fee) values
  ('Melaka City', '75000', 8),
  ('Melaka City', '75050', 8),
  ('Melaka City', '75100', 8),
  ('Melaka City', '75150', 8),
  ('Melaka City', '75200', 8),
  ('Melaka City', '75250', 8),
  ('Melaka City', '75260', 8),
  ('Bukit Beruang', '76000', 10),
  ('Bukit Beruang', '76100', 10),
  ('Bukit Beruang', '76200', 10),
  ('Ayer Keroh', '79000', 12),
  ('Ayer Keroh', '79100', 12),
  ('Bemban', '78000', 15);
```

### 添加示例商品

```sql
insert into products (name, description, brand, category, price, available, display_order) values
  ('Pistachio Basque', 'Rich and creamy', 'jrome', 'Basque Cheesecake', 15.90, true, 1),
  ('Matcha Basque', 'Japanese green tea flavor', 'jrome', 'Basque Cheesecake', 15.90, true, 2),
  ('Original Bagel', 'Classic bagel', 'cbh', 'Bagels', 4.50, true, 1),
  ('Chocolate Chip Bagel', 'Bagel with chocolate chips', 'cbh', 'Bagels', 5.50, true, 2);
```

---

## 🔐 行级安全 (RLS) - 可选但推荐

### 设置 Products 表 RLS

```sql
-- 启用 RLS
alter table products enable row level security;

-- 所有人都可以读取
create policy "Products are readable by everyone" on products
  for select using (true);

-- 只有管理员可以写（需要实现认证）
create policy "Products are insertable by admin" on products
  for insert with check (true);

create policy "Products are updatable by admin" on products
  for update using (true);
```

### 设置 Orders 表 RLS

```sql
-- 启用 RLS
alter table orders enable row level security;

-- 顾客只能看到自己的订单
create policy "Users can view their own orders" on orders
  for select using (true);

-- 顾客可以创建订单
create policy "Users can create orders" on orders
  for insert with check (true);
```

---

## 🚀 部署步骤

### 1. 复制 SQL 脚本

1. 进入 Supabase 控制台
2. 点击 **SQL Editor**
3. 创建 **New Query**
4. 复制上面的所有 SQL 代码
5. 执行

### 2. 创建存储桶

按照上面的步骤创建 `qr-codes` 存储桶

### 3. 上传文件到 Vercel

```bash
# 在你的项目根目录
git add customer-app.html admin.html
git commit -m "Add customer app and admin dashboard"
git push origin main
```

Vercel 会自动部署

### 4. 访问应用

- **Customer App**: `https://cbakinghouse-x-jromepastry-app.vercel.app/customer-app.html`
- **Admin Dashboard**: `https://cbakinghouse-x-jromepastry-app.vercel.app/admin.html`

---

## 📝 WhatsApp 号码配置

在 `customer-app.html` 中，找到这部分：

```javascript
const WHATSAPP = {
  cbh: '601110821733',      // 你的 Cbakinghouse WhatsApp
  jrome: '60117634554'       // 你的 J.ROME WhatsApp
};
```

记得把号码中间的 `0` 改成 `60`（国际格式）

---

## 🎯 Melaka 配送区域配置

在 `customer-app.html` 中，找到：

```javascript
const DELIVERY_ZONES = {
  '75000': { name: 'Melaka City', fee: 8 },
  '75050': { name: 'Melaka City', fee: 8 },
  // ... etc
};
```

你可以：
1. 直接修改这里（需要修改代码）
2. **或者** 在 Admin Dashboard 中添加（推荐）

---

## ✅ 检查清单

- [ ] 创建了所有 5 个表
- [ ] 创建了 `qr-codes` 存储桶
- [ ] 添加了示例分类
- [ ] 添加了配送区域
- [ ] 添加了示例商品
- [ ] 上传了 customer-app.html 和 admin.html 到 Vercel
- [ ] 上传了 DuitNow QR 码到 Supabase
- [ ] 测试了顾客流程（浏览 → 购物车 → 结账）
- [ ] 测试了 Admin Dashboard
- [ ] 配置了 WhatsApp 号码

---

## 🆘 常见问题

**Q: 怎样上传 QR 码？**
A: 进入 Admin Dashboard → Settings → QR Code Management → 选择文件上传

**Q: 怎样改配送费？**
A: Admin Dashboard → Delivery Zones → 修改或添加新区域

**Q: 顾客怎样修改订单？**
A: 目前顾客只能通过 WhatsApp 联系你修改。以后可以添加编辑功能。

**Q: 怎样备份数据？**
A: Supabase 会自动备份。也可以导出 CSV。

---

## 🔗 有用的链接

- Supabase 控制台: https://app.supabase.com
- Vercel 控制台: https://vercel.com/dashboard
- 这个项目: https://vercel.com/cbakinghouse-xj-romepastry/cbakinghouse-x-jromepastry-app

