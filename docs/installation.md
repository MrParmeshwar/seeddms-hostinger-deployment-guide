# 🛠️ SeedDMS — Installation Guide (Hostinger Shared Hosting)

> First-time setup of SeedDMS on Hostinger Shared Hosting using SSH and hPanel.

---

## ⚠️ Before You Start

| Requirement | Details |
|---|---|
| Hosting | Hostinger Shared Hosting |
| PHP Version | 8.2 or higher |
| Database | MySQL |
| SSH Access | Required for 2 steps only |
| hPanel Access | Required |

> **Note:** SSH is only required for extracting the archive and creating the vendor symlink. Everything else can be done via hPanel File Manager (GUI).

---

## 📥 Step 1 — Download SeedDMS

Download the latest quickstart archive from the official website:

👉 [sourceforge.net/projects/seeddms/files](https://sourceforge.net/projects/seeddms/files)

Download the file named: `seeddms-quickstart-X.X.X.tar.gz` to your computer.

---

## 🗄️ Step 2 — Create a Database

Go to **hPanel → Databases → MySQL Databases**

- Create a new database (e.g. `u624219220_docs`)
- Note down the database name, username, and password
- You do not need to create any tables — the installer will handle that

> **Why?** SeedDMS needs a MySQL database to store all document metadata, users, and permissions.

---

## 📤 Step 3 — Upload Archive to Hostinger

Go to **hPanel → File Manager → public_html**

- Click **Upload**
- Upload the `seeddms-quickstart-X.X.X.tar.gz` file

> **Why not extract via GUI?** Hostinger's File Manager cannot properly extract large `.tar.gz` files — it breaks in between. SSH extraction is required.

---

## 💻 Step 4 — Connect via SSH and Extract

> 🔵 **SSH Required for this step.**

Connect to your server:

```bash
ssh u624219220@yourdomain.com -p 65002
```

> Hostinger uses port **65002**, not the default port 22.

Navigate to public_html and extract:

```bash
cd ~/domains/yourdomain.com/public_html
tar -xvzf seeddms-quickstart-X.X.X.tar.gz
```

✅ All symlinks are created automatically during extraction.

---

## 🔗 Step 5 — Fix the vendor Symlink

> 🔵 **SSH Required for this step. Do this BEFORE running the installer!**

After extraction, the vendor symlink points to a wrong location. Fix it:

```bash
cd ~/domains/yourdomain.com/public_html
unlink seeddms-X.X.X/vendor
ln -s ~/domains/yourdomain.com/public_html/vendor seeddms-X.X.X/vendor
```

Verify it is fixed:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

You should see:
```
vendor -> /home/username/domains/yourdomain.com/public_html/vendor
```

> **Why?** If you skip this step, the installation page will show a fatal HTTP 500 error and installation will fail completely.

---

## ⚙️ Step 6 — Configure settings.xml

Go to **hPanel → File Manager → public_html → conf → settings.xml**

Open the file and update the following values:

```xml
<server
  rootDir="/home/username/domains/yourdomain.com/public_html/www/"
  httpRoot="/"
  contentDir="/home/username/domains/yourdomain.com/public_html/data/"
  stagingDir="/home/username/domains/yourdomain.com/public_html/data/staging/"
  luceneDir="/home/username/domains/yourdomain.com/public_html/data/lucene/"
  cacheDir="/home/username/domains/yourdomain.com/public_html/data/cache/"
  backupDir="/home/username/domains/yourdomain.com/public_html/data/backup/"
  baseUrl="https://yourdomain.com"
  ...
/>
```

Replace `username` and `yourdomain.com` with your actual values.

> **Why?** SeedDMS needs to know where its files and data are located on the server.

---

## 🔓 Step 7 — Create ENABLE_INSTALL_TOOL File

Go to **hPanel → File Manager → public_html → conf**

- Click **New File**
- Name it exactly: `ENABLE_INSTALL_TOOL`
- No content needed — just an empty file

> **Why?** SeedDMS requires this file to exist before it allows the installer to run. It is a security measure.

---

## 🌐 Step 8 — Set Up .htaccess for URL Routing

Go to **hPanel → File Manager → public_html**

Open or create `.htaccess` and add:

```apache
RewriteEngine On
RewriteCond %{REQUEST_URI} !^/www/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /www/$1 [L,QSA]
```

> **Why?** SeedDMS code lives inside the `www/` subfolder. This rule routes all requests to the correct location while keeping URLs clean (no `/www/` visible in browser).

---

## 🚀 Step 9 — Run the Installer

Open your browser and visit:

```
https://yourdomain.com/www/install/
```

- Click **Start Installation**
- Fill in your database details (name, username, password)
- Set admin username and password
- Click **Save**

✅ Installation complete!

---

## 🎨 Step 10 — Custom Branding

### Custom Logo
- Location: `public_html/seeddms-X.X.X/views/bootstrap/images/`
- Replace or add your logo file in this folder via File Manager

### Custom UI Text
- Location: `public_html/seeddms-X.X.X/languages/en_GB/lang.inc`
- Open this file and edit any text labels you want to change

---

## 🔧 Step 11 — Fix Charts (Bootstrap Theme)

The Bootstrap theme is missing the `tablesort` library. Copy it from the Bootstrap4 theme:

**Using File Manager:**
- Navigate to `public_html/seeddms-X.X.X/views/bootstrap4/vendors/`
- Select the `tablesort` folder
- Click **Copy**
- In the window, navigate to `public_html/seeddms-X.X.X/views/bootstrap/vendors/`
- Click **Copy** in the window

**Using SSH:**
```bash
cd ~/domains/yourdomain.com/public_html/seeddms-X.X.X
cp -r views/bootstrap4/vendors/tablesort views/bootstrap/vendors/
```

> **Why?** Without this, the Charts section under Admin Tools will not load and will show JavaScript errors.

---

## 🔐 Step 12 — First Login & Password Change

- Visit: `https://yourdomain.com`
- Login with the admin credentials you set during installation
- Go to **My Account** and change your password immediately

---

## ☁️ Step 13 — Cloudflare Integration (Recommended)

If you notice unusually high traffic (mostly bots), integrate Cloudflare:

1. Create a free account at [cloudflare.com](https://cloudflare.com)
2. Add your domain
3. Replace your Hostinger DNS records with Cloudflare DNS records in hPanel
4. Set up firewall rules in Cloudflare to block bot traffic

> **Why?** Cloudflare adds a security layer, blocks malicious bots, and improves performance through CDN caching.

---

## ✅ Post-Installation Checklist

- [ ] Login works correctly
- [ ] Documents can be uploaded
- [ ] Custom logo showing
- [ ] Custom text labels correct
- [ ] Charts loading (Admin Tools → Charts)
- [ ] ENABLE_INSTALL_TOOL file deleted from conf/
- [ ] Cloudflare integrated (optional but recommended)

---

> For upgrade instructions, see [upgrade.md](upgrade.md)
> For common errors, see [troubleshooting.md](troubleshooting.md)
