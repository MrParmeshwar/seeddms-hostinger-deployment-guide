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
ssh username@yourdomain.com -p 65002
```

> Hostinger uses port **65002**, not the default port 22.

Navigate to public_html and extract:

```bash
cd ~/domains/yourdomain.com/public_html
tar -xvzf seeddms-quickstart-X.X.X.tar.gz
```

✅ Most symlinks are created automatically during extraction, except the `vendor` symlink which may need to be created manually.

---

## 🔗 Step 5 — Create/Fix the vendor Symlink

> 🔵 **SSH Required for this step. Do this BEFORE running the installer!**

After extraction, check whether the `vendor` symlink exists inside the extracted SeedDMS folder.

Navigate to `public_html`:

```bash
cd ~/domains/yourdomain.com/public_html
```

Check the symlink:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

If the `vendor` symlink is missing or incorrect, create it manually:

```bash
ln -s /home/USERNAME/domains/YOURDOMAIN/public_html/vendor seeddms-X.X.X/vendor
```

Verify again:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

Expected output:

```text
vendor -> /home/USERNAME/domains/YOURDOMAIN/public_html/vendor
```

> **Why?** SeedDMS requires the `vendor/` directory for PHP dependencies.
>
> If the `vendor` symlink is missing or pointing to the wrong location, the installation page may show HTTP 500 errors and fail to load properly.

--- 

## ⚙️ Step 6 — Configure settings.xml

Go to:

```text
hPanel → File Manager → public_html → conf → settings.xml
```

Open the file and verify that `rootDir` is pointing to the correct location:

```xml
<server
  rootDir="/home/username/domains/yourdomain.com/public_html/www/"
  ...
/>
```

Replace:

- `username` with your hosting username
- `yourdomain.com` with your actual domain

> 📌 Once `rootDir` is configured correctly, the installer usually detects and fills the remaining paths automatically during installation.

> 📌 If the remaining paths are not detected automatically, configure them manually like this:

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

> **Why?** SeedDMS uses these paths to locate application files, uploaded documents, cache, search indexes, and backup directories correctly.

---
## 🔓 Step 7 — Create ENABLE_INSTALL_TOOL File

Go to **hPanel → File Manager → public_html → conf**

- Click **New File**
- Name it exactly: `ENABLE_INSTALL_TOOL`
- No content needed — just an empty file

> **Why?** SeedDMS requires this file to exist before it allows the installer to run. It is a security measure.


---

## 🚀 Step 8 — Run the Installer

Open your browser and visit:

```text
https://yourdomain.com/www/install/
```

- Click **Start Installation**
- Fill in your database details:
  - Database name
  - Database username
  - Database password
- Select/tick the **Create Database Tables** checkbox
- Continue with the installation steps
- Click **Save**

✅ Installation complete!

> 📌 The default SeedDMS administrator credentials are:
>
> ```text
> Username: admin
> Password: admin
> ```

> ⚠️ Login using the default credentials and change the admin password immediately after the first login.

> ⚠️ After installation is completed, delete or rename the `ENABLE_INSTALL_TOOL` file inside:
>
> ```text
> public_html/conf/
> ```
>
> Example:
>
> ```text
> ENABLE_INSTALL_TOOL → ENABLE_INSTALL_TOOL_old
> ```

---
## 🎨 Step 9 — Custom Branding

### Custom Logo

- Location:

```text
public_html/seeddms-X.X.X/views/bootstrap/images/
```
- Upload your custom logo/image files inside the `images/` folder using File Manager
- To replace an existing logo, replace the original image file with your custom image and keep the same filename as the old image

> 📌 Keeping the same filename helps avoid additional configuration changes.

### Custom UI Text

- Location:

```text
public_html/seeddms-X.X.X/languages/en_GB/lang.inc
```

- Open the `lang.inc` file and edit the text labels you want to customize

---

## 🔧 Step 10 — Fix Charts (Bootstrap Theme)

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

> **Why?** Without this, the Charts section under Admin Tools will not load and will show JavaScript errors or black page insted of chart.

---
## 🔐 Step 11 — First Login & Password Change

- Visit:

```text
https://yourdomain.com
```

- Login using the default credentials:

```text
Username: admin
Password: admin
```

- Go to **My Account**
- Change the admin password immediately after login

---
## 🌐 Step 12 — Set Up .htaccess for Clean URL Routing

Go to:

```text
hPanel → File Manager → public_html
```

Open or create the `.htaccess` file and add:

```apache
RewriteEngine On
RewriteCond %{REQUEST_URI} !^/www/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /www/$1 [L,QSA]
```

> 📌 This step is recommended after installation and first login verification are completed.

> **Why?** SeedDMS application files are served from the `www/` directory.
>
> These rewrite rules automatically route requests to the correct location while keeping the URL clean (without showing `/www/` in the browser).

---

## ☁️ Step 13 — Cloudflare Integration (Recommended)

If you notice unusually high traffic (mostly bots), integrate Cloudflare:

1. Create a free account at [cloudflare.com](https://cloudflare.com)
2. Add your domain
3. Update your domain nameservers to the Cloudflare nameservers provided during setup
4. Set up firewall rules in Cloudflare to block bot traffic

> **Why?** Cloudflare adds a security layer, blocks malicious bots, and improves performance through CDN caching.

---

## ✅ Post-Installation Checklist

- [ ] Login works correctly
- [ ] Documents can be uploaded
- [ ] Custom logo showing
- [ ] Custom text labels correct
- [ ] Charts loading (Admin Tools → Charts)
- [ ] ENABLE_INSTALL_TOOL file deleted or renamed from `conf/`
- [ ] Cloudflare integrated (optional but recommended)

---

> For upgrade instructions, see [upgrade.md](upgrade.md)
> For common errors, see [troubleshooting.md](troubleshooting.md)
