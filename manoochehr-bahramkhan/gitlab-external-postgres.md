
# راه‌اندازی GitLab با دیتابیس PostgreSQL خارجی

این راهنما مراحل کامل نصب GitLab روی یک سرور را نشان می‌دهد، به طوری که دیتابیس PostgreSQL روی یک سرور خارجی قرار دارد.

---

## 🎯 سناریو:

| مورد            | مقدار مثال       |
|-----------------|------------------|
| IP سرور GitLab  | `192.168.1.10`   |
| IP دیتابیس PG   | `192.168.1.20`   |
| دیتابیس         | PostgreSQL 14+   |
| یوزر دیتابیس    | `gitlab`         |
| نام دیتابیس     | `gitlabhq_production` |
| پسورد یوزر      | `StrongPass123!` |

---

## 🔹 مرحله 1: نصب PostgreSQL روی سرور دیتابیس خارجی

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib -y
```

### 📁 پیکربندی PostgreSQL برای دسترسی خارجی:

1. ویرایش فایل:
```bash
sudo nano /etc/postgresql/14/main/postgresql.conf
```
```conf
listen_addresses = '*'
```

2. ویرایش:
```bash
sudo nano /etc/postgresql/14/main/pg_hba.conf
```

افزودن خط:
```conf
host    gitlabhq_production    gitlab    192.168.1.10/32    md5
```

3. ریستارت:
```bash
sudo systemctl restart postgresql
```

### 🛠️ ساخت دیتابیس و یوزر:

```bash
sudo -u postgres psql
```

```sql
CREATE USER gitlab WITH PASSWORD 'StrongPass123!';
CREATE DATABASE gitlabhq_production OWNER gitlab ENCODING 'UTF8';
\q
```

---

## 🔹 مرحله 2: نصب GitLab روی سرور `192.168.1.10`

```bash
sudo apt update && sudo apt install -y curl openssh-server ca-certificates tzdata perl
```

### اضافه کردن مخزن GitLab:

```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```

### نصب اولیه:

```bash
sudo EXTERNAL_URL="http://gitlab.example.com" apt install gitlab-ce
```

---

## 🔹 مرحله 3: اتصال به دیتابیس خارجی

```bash
sudo nano /etc/gitlab/gitlab.rb
```

وارد کردن تنظیمات زیر:

```ruby
postgresql['enable'] = false

gitlab_rails['db_adapter'] = 'postgresql'
gitlab_rails['db_encoding'] = 'utf8'
gitlab_rails['db_host'] = '192.168.1.20'
gitlab_rails['db_port'] = 5432
gitlab_rails['db_database'] = 'gitlabhq_production'
gitlab_rails['db_username'] = 'gitlab'
gitlab_rails['db_password'] = 'StrongPass123!'

# در صورت نیاز به SSL:
# gitlab_rails['db_sslmode'] = 'require'
```

---

## 🔹 مرحله 4: اجرای Reconfigure

```bash
sudo gitlab-ctl reconfigure
```

---

## 🧪 بررسی وضعیت

```bash
sudo gitlab-ctl tail
sudo journalctl -u gitlab-runsvdir -f
```

تست اتصال:

```bash
sudo gitlab-rails dbconsole
```

---

## ✅ پایان


