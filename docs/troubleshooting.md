# 🔧 SeedDMS — Troubleshooting Guide

> Common errors encountered during installation and upgrade, with fixes.

---

## ❌ HTTP 500 Error on install/install.php

**Cause:** The `vendor/` symlink is broken or pointing to wrong location.

**Fix:**
```bash
cd ~/domains/yourdomain.com/public_html/n
unlink seeddms-X.X.X/vendor
ln -s ~/domains/yourdomain.com/public_html/n/vendor seeddms-X.X.X/vendor
```

---

## ❌ "Please create ENABLE_INSTALL_TOOL file"

**Cause:** The `ENABLE_INSTALL_TOOL` file is missing from the `conf/` folder.

**Fix:**

Go to **hPanel → File Manager → public_html → n → conf**
- Click **New File**
- Name it exactly: `ENABLE_INSTALL_TOOL`

Or via SSH:
```bash
touch ~/domains/yourdomain.com/public_html/n/conf/ENABLE_INSTALL_TOOL
```

---

## ❌ Charts Not Showing / "Tablesort is not defined"

**Cause:** The `tablesort` library is missing from the Bootstrap theme folder.

**Fix:**

- **From:** `seeddms-X.X.X/views/bootstrap4/vendors/tablesort`
- **To:** `seeddms-X.X.X/views/bootstrap/vendors/`

Via SSH:
```bash
cd ~/domains/yourdomain.com/public_html/seeddms-X.X.X
cp -r views/bootstrap4/vendors/tablesort views/bootstrap/vendors/
```

> Do this after every upgrade.

---

## ❌ Custom Images / Logo Not Showing After Upgrade

**Cause:** Custom images were not copied to the new version OR Cloudflare cache is serving old files.

**Fix:**
1. Copy your custom image files to `seeddms-X.X.X/views/bootstrap/images/`
2. Go to Cloudflare → Purge Cache → Purge Everything
3. Hard refresh browser: `Ctrl + Shift + R`

---

## ❌ SSH Not Connecting / Connection Gets Stuck

**Cause:** Wrong port. Hostinger uses port `65002`, not the default port `22`.

**Fix:**
```bash
ssh username@yourdomain.com -p 65002
```

---

## ❌ Old Documents Not Visible After Upgrade

**Cause:** `settings.xml` `contentDir` is pointing to wrong data folder path.

**Fix:**

Open `public_html/conf/settings.xml` and make sure `contentDir` is pointing to:
```
/home/username/domains/yourdomain.com/public_html/data/
```

---

## ❌ Site Shows Old Version Even After Upgrade

**Cause:** Cloudflare cache is still serving old files.

**Fix:**
1. Go to Cloudflare dashboard
2. Caching → Purge Cache → Purge Everything
3. Press `Ctrl + Shift + R` in browser

---

## ❌ 403 Forbidden on Root URL

**Cause:** `.htaccess` rewrite rule is missing or incorrect in `public_html`.

**Fix:**

Open `public_html/.htaccess` and make sure it contains:

```apache
RewriteEngine On
RewriteCond %{REQUEST_URI} !^/www/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /www/$1 [L,QSA]
```

---

## ❌ Large .tar.gz File Not Extracting via File Manager

**Cause:** Hostinger File Manager cannot properly extract large `.tar.gz` files — it breaks in between.

**Fix:** Always extract via SSH:

```bash
cd ~/domains/yourdomain.com/public_html
tar -xvzf seeddms-quickstart-X.X.X.tar.gz
```

---

> For installation steps, see [installation.md](installation.md)
> For upgrade steps, see [upgrade.md](upgrade.md)
