# 🔧 SeedDMS — Troubleshooting Guide

> Common issues encountered during fresh installation, upgrade, and testing of SeedDMS, along with their solutions.

> 📌 In this guide, `<temporary-folder>` refers to the folder used for upgrade/testing (example: `n/`).

---

## ❌ HTTP 500 Error on install/install.php

### Applies To
- Fresh Installation
- Upgrade
- Testing

### Symptoms

- HTTP 500 error while opening:
  
```text
/install/
/install/install.php
```

- Blank white page during installation or upgrade
- Migration page not loading

### Cause

The `vendor` symlink is missing, broken, or pointing to the wrong location.

### Fix

Navigate to your temporary/testing folder:

```bash
cd ~/domains/yourdomain.com/public_html/<temporary-folder>
```

Check whether the `vendor` symlink exists inside the extracted SeedDMS folder:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

If the symlink is missing or incorrect, create it manually:

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

### Why It Happens

SeedDMS depends on the shared `vendor/` directory for PHP dependencies.  
If the symlink is missing or incorrect, installation and migration pages can fail with HTTP 500 errors.

---
## ❌ "Please create ENABLE_INSTALL_TOOL file"

### Applies To
- Fresh Installation
- Upgrade
- Testing

### Symptoms

- Error message:
  
```text
Please create ENABLE_INSTALL_TOOL file
```

- Installation or migration page does not continue

### Cause

The `ENABLE_INSTALL_TOOL` file is missing from the `conf/` directory.

### Fix

### Using File Manager

Go to:

```text
hPanel → File Manager → public_html → <temporary-folder> → conf
```

- Click **New File**
- Name it exactly:

```text
ENABLE_INSTALL_TOOL
```

### Using SSH

```bash
touch ~/domains/yourdomain.com/public_html/<temporary-folder>/conf/ENABLE_INSTALL_TOOL
```

### Why It Happens

SeedDMS blocks installation and migration access unless the `ENABLE_INSTALL_TOOL` file exists for security reasons.

---


## ❌ Charts Not Showing / "Tablesort is not defined"

### Applies To
- Fresh Installation
- Upgrade

### Symptoms

- Charts page not loading
- Browser console error:

```text
Tablesort is not defined
```

- Admin charts page appears broken or empty

### Cause

The `tablesort` library is missing from the Bootstrap theme vendor directory.

This issue may occur when using the Bootstrap theme in some SeedDMS versions.

### Fix

Copy the `tablesort` folder:

- **From:**

```text
seeddms-X.X.X/views/bootstrap4/vendors/tablesort
```

- **To:**

```text
seeddms-X.X.X/views/bootstrap/vendors/
```

### Using SSH

```bash
cd ~/domains/yourdomain.com/public_html/seeddms-X.X.X
cp -r views/bootstrap4/vendors/tablesort views/bootstrap/vendors/
```

### Why It Happens

Some SeedDMS versions include the `tablesort` library only inside the `bootstrap4` theme folder, while the Bootstrap charts page expects it inside:

```text
views/bootstrap/vendors/
```

Without this library, charts and tables fail to load correctly.

> 📌 Re-check this after upgrades because some versions may overwrite or remove the copied files.


---

## ❌ Custom Images / Logo Not Showing After Upgrade

### Applies To
- Upgrade
- Custom Branding

### Symptoms

- Default SeedDMS logo appears
- Custom branding/images missing
- Website icons or logos not updating
- Old images still visible after upgrade

### Cause

Custom images were not copied to the updated version, or Cloudflare/browser cache is still serving old cached files.

### Fix

1. Copy the entire `images` folder from the old version to the updated version:

```text
From:
seeddms-OLD.VERSION/views/bootstrap/images/

To:
seeddms-NEW.VERSION/views/bootstrap/images/
```

2. Purge Cloudflare cache:

```text
Cloudflare → Caching → Purge Cache → Purge Everything
```

3. Hard refresh the browser:

```text
Ctrl + Shift + R
```

### Why It Happens

SeedDMS stores custom branding images inside the application directory.  
During upgrades, the new version uses its own default `images/` folder unless the old custom images are copied manually.

Cloudflare and browser caching may also continue showing old files until the cache is cleared.

---

## ❌ SSH Not Connecting / Connection Gets Stuck

### Applies To
- Fresh Installation
- Upgrade
- Troubleshooting

### Symptoms

- SSH connection hangs
- Connection timeout
- Unable to connect using default SSH command

### Cause

Hostinger shared hosting uses a custom SSH port (`65002`) instead of the default SSH port (`22`).

### Fix

Use the correct SSH port:

```bash
ssh username@yourdomain.com -p 65002
```

### Why It Happens

Most SSH clients automatically try port `22`.  
Hostinger shared hosting requires connections on port `65002`.

---

## ❌ Old Documents Not Visible After Upgrade

### Applies To
- Upgrade
- Rollback
- Testing

### Symptoms

- Documents missing after upgrade
- Empty folders visible
- Previously uploaded files not appearing
- SeedDMS opens normally but old data is missing

### Cause

The `contentDir` path inside `settings.xml` is pointing to the wrong `data/` directory.

### Fix

Open:

```text
public_html/conf/settings.xml
```

Locate the `contentDir` setting and verify it points to the correct live `data/` folder:

```text
/home/username/domains/yourdomain.com/public_html/data/
```

### Important Notes

- During temporary testing/upgrades, some users may accidentally point `contentDir` to:

```text
/public_html/<temporary-folder>/data/
```

- After the upgrade goes live, the final production path should normally point back to:

```text
/public_html/data/
```

### Why It Happens

SeedDMS stores uploaded documents inside the `data/` directory.

If `contentDir` points to the wrong folder, SeedDMS cannot find existing documents even though the files still exist on the server.

---
## ❌ Site Shows Old Version Even After Upgrade

### Applies To
- Upgrade
- Rollback
- Custom Branding Changes

### Symptoms

- Old website version still appears
- Old logo/images still visible
- UI changes not updating
- Browser still loading old CSS/JS files after upgrade

### Cause

Cloudflare or browser cache is still serving previously cached files.

### Fix

1. Open your Cloudflare dashboard
2. Navigate to:

```text
Caching → Purge Cache → Purge Everything
```

3. Confirm the cache purge
4. Hard refresh your browser:

```text
Ctrl + Shift + R
```

### Why It Happens

Cloudflare and modern browsers cache website files such as:

- Images
- CSS
- JavaScript
- UI assets

After upgrades or branding changes, old cached files may continue loading until the cache is cleared.

---

## ❌ 403 Forbidden on Root URL

### Applies To
- Fresh Installation
- Live Deployment
- URL Routing

### Symptoms

- `403 Forbidden` error on the main domain
- Root URL not opening
- Website only works when `/www/` is added manually
- Blank or forbidden page on homepage

### Cause

The `.htaccess` rewrite rule inside `public_html/` is missing, incorrect, or disabled.

### Fix

Open:

```text
public_html/.htaccess
```

Make sure it contains:

```apache
RewriteEngine On
RewriteCond %{REQUEST_URI} !^/www/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /www/$1 [L,QSA]
```

### Why It Happens

SeedDMS serves the main application from the `www/` directory.

The `.htaccess` rewrite rule automatically redirects requests from the root domain to:

```text
/www/
```

Without this rewrite rule, Apache may block access to the root URL or show a `403 Forbidden` error.

> 📌 During upgrade/testing inside a temporary folder, this `.htaccess` file may temporarily need to be disabled to avoid redirect conflicts.

---

## ❌ Large `.tar.gz` File Not Extracting via File Manager

### Applies To
- Fresh Installation
- Upgrade

### Symptoms

- Extraction stops midway
- Missing files after extraction
- Incomplete SeedDMS folders
- Installation or upgrade errors after extraction
- File Manager extraction fails silently

### Cause

Hostinger File Manager may fail to properly extract large `.tar.gz` archives, especially for bigger SeedDMS packages.

### Fix

Extract the archive using SSH instead of File Manager:

```bash
cd ~/domains/yourdomain.com/public_html
tar -xvzf seeddms-quickstart-X.X.X.tar.gz
```

### Why It Happens

Large archive extraction through browser-based File Managers can fail due to:

- Memory limitations
- Execution time limits
- Browser interruptions

SSH extraction is more reliable and recommended for SeedDMS installation and upgrades.

---

> For installation steps, see [installation.md](installation.md)

> For upgrade steps, see [upgrade.md](upgrade.md)
