# 🐧 SeedDMS — Local Installation on Ubuntu

---

## 🧩 Environment

- Ubuntu Linux
- Apache2 Web Server
- PHP
- MySQL (or MariaDB)
- Localhost deployment

---

## 📦 Step 1 — Install Required Packages

Install all required dependencies:

```bash
sudo apt update
sudo apt install apache2 php mysql-server php-mysql php-xml php-gd php-cli php-curl php-zip
```

> **Why?** SeedDMS requires Apache, PHP, and MySQL along with specific PHP extensions to function correctly.

---

## 📁 Step 2 — Copy SeedDMS Files to Web Root

Move the downloaded archive into Apache’s web directory:

```bash
cd ~/Downloads
sudo cp seeddms-quickstart-6.0.38.tar.gz /var/www/html
```

---

## 📦 Step 3 — Extract the Archive

```bash
cd /var/www/html
sudo tar -xvzf seeddms-quickstart-6.0.38.tar.gz
```

> **Why?** Extraction inside `/var/www/html` requires elevated permissions.

---

## 📂 Step 4 — Rename Extracted Folder

```bash
sudo mv seeddms60x seeddms
```

Final path:

```text
/var/www/html/seeddms
```

> **Why?** Renaming simplifies paths and makes configuration easier.

---

## 🔐 Step 5 — Set Permissions

Assign proper ownership and permissions:

```bash
sudo chown -R www-data:www-data /var/www/html/seeddms
sudo chmod -R 755 /var/www/html/seeddms
sudo chmod -R 775 /var/www/html/seeddms/data
```

> **Why?** Apache needs write access to create files, logs, cache, and store uploaded documents.

> ⚠️ Temporary troubleshooting step (not recommended for production):
```bash
sudo chmod -R 777 /var/www/html/seeddms/data
```

---

## 🔗 Step 6 — Create Vendor Symlink

```bash
cd /var/www/html/seeddms/seeddms-6.0.38
ln -s /var/www/html/seeddms/vendor vendor
```

Verify:

```bash
ls -l
```

Expected output:

```text
vendor -> /var/www/html/seeddms/vendor
```

> **Why?** The application version directory requires access to shared vendor dependencies.

---

## ⚙️ Step 7 — Configure settings.xml

Open the configuration file:

```bash
cd /var/www/html/seeddms/conf
nano settings.xml
```

Update database configuration:

```xml
<database dbDriver="mysql"
dbHostname="localhost"
dbDatabase="DBname"
dbUser="DBUser"
dbPass="DBPassword"
/>
```

Update server configuration:

```xml
<server
rootDir="/var/www/html/seeddms/www/"
httpRoot="/seeddms/www/"
contentDir="/var/www/html/seeddms/data/"
stagingDir="/var/www/html/seeddms/data/staging/"
luceneDir="/var/www/html/seeddms/data/lucene/"
cacheDir="/var/www/html/seeddms/data/cache/"
logFileEnable="true"
/>
```

> 📌 Initially, `httpRoot="/"` may seem correct, but after installation the working value is:
>
> ```text
> /seeddms/www/
> ```

> **Why?** This defines the correct application path for browser access.

---

## 🔄 Step 8 — Restart Apache

```bash
sudo systemctl restart apache2
```

---

## 🧰 Step 9 — Enable Installer

```bash
cd /var/www/html/seeddms/conf
touch ENABLE_INSTALL_TOOL
```

> **Why?** This file enables the SeedDMS web installer.

---

## 🌐 Step 10 — Run Installer

Open browser:

```text
http://localhost/seeddms/www/install/
```

> ⚠️ Accessing `/www/` before installation may show a **500 Internal Server Error**.

---

## 🗄️ Step 11 — Database Setup

- The installer will detect configuration automatically
- Select **Create database tables**
- Click **Apply**

> **Why?** This step creates all required database tables for SeedDMS.

---

## 🔒 Step 12 — Disable Installer

After installation:

- Delete `ENABLE_INSTALL_TOOL` from UI  
OR  
- Remove manually:

```bash
rm /var/www/html/seeddms/conf/ENABLE_INSTALL_TOOL
```

> **Why?** Prevents unauthorized re-installation.

---

## ✅ Step 13 — Test Application

Open:

```text
http://localhost/seeddms/www/
```

Login page should appear and application should work correctly.

---

## ⚠️ Common Issues & Fixes

### Permission Errors
- Fix using `chown` and `chmod`

### 500 Internal Server Error
- Occurs if installation is not completed

### Cannot create directories (mkdir error)
- Ensure `data/` directory is writable

### Vendor issues
- Verify symbolic link is correctly created

---

## 📖 Key Observations

- SeedDMS requires correct file permissions before installation
- `settings.xml` plays a critical role in configuration
- Vendor symlink is necessary for proper dependency loading
- Installer automatically configures paths and database structure
- Direct access before installation leads to errors

---
## 📖 Key Learnings

- File permissions (`chown`, `chmod`) are critical — wrong settings can break installation
- SeedDMS requires writable directories (`data/`, cache, logs) before setup
- Vendor symlink is necessary for proper dependency loading
- `settings.xml` controls database, paths, and application routing
- Incorrect `httpRoot` or paths can break the application
- HTTP 500 errors can occur if installation is incomplete
- Installer modifies configuration automatically — avoid manual changes afterward
- Temporary fixes like `777` help debugging but are not safe for production
