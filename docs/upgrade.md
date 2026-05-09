# 🔄 SeedDMS — Upgrade Guide (Hostinger Shared Hosting)

> Step-by-step guide to upgrade SeedDMS to a newer version on Hostinger Shared Hosting.

---

## ⚠️ Important Notes

- SSH is only required for **2 steps**: extracting the archive and fixing the vendor symlink
- Everything else can be done via **hPanel File Manager (GUI)**
- The `data/` folder should **never be touched** during upgrade — all your documents are safe
- Always test on the staging folder (`n/`) before going live

---

## 📥 Step 1 — Download the New Version

Download the latest version from the official website:

👉 [sourceforge.net/projects/seeddms/files](https://sourceforge.net/projects/seeddms/files)

Download: `seeddms-quickstart-X.X.X.tar.gz` to your computer.

> **Why?** Without the new version file, there is nothing to upgrade to.

---

## 💾 Step 2 — Backup Existing Files

Go to **hPanel → File Manager → public_html**

- Select all files and folders that belong to your domain
- Click **Download** — a `.zip` file will be downloaded to your computer
- Save this zip file safely

> **Why?** If anything goes wrong during upgrade, this backup is your only way to restore the old version.

---

## 📁 Step 3 — Create Staging Folder and Upload New Version

Go to **hPanel → File Manager → public_html**

- Click **New Folder** and name it `n`
- Open the `n` folder
- Click **Upload** and upload the new version `.tar.gz` file

> **Why?** The `n` folder is your staging area. You test everything here before going live — your live site keeps running without any interruption.

---

## 💻 Step 4 — Extract via SSH

> 🔵 **SSH Required for this step.**

Connect to your server:

```bash
ssh u624219220@yourdomain.com -p 65002
```

Navigate to the `n` folder and extract:

```bash
cd ~/domains/yourdomain.com/public_html/n
tar -xvzf seeddms-quickstart-X.X.X.tar.gz
```

✅ All symlinks are created automatically during extraction.

> **Why SSH?** File Manager cannot extract `.tar.gz` files correctly — symlinks will break. SSH extraction handles everything properly.

---

## 🔗 Step 5 — Fix the vendor Symlink

> 🔵 **SSH Required for this step. Do this BEFORE running migration!**

```bash
cd ~/domains/yourdomain.com/public_html/n
unlink seeddms-X.X.X/vendor
ln -s ~/domains/yourdomain.com/public_html/n/vendor seeddms-X.X.X/vendor
```

Verify:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

> **Why?** If you skip this step, the migration page will show a fatal HTTP 500 error and installation will fail.

---

## 📋 Step 6 — Copy Old settings.xml

**What to do:** Copy settings.xml from old location to new location.

- **From:** `public_html/conf/settings.xml`
- **To:** `public_html/n/conf/settings.xml`

**Using File Manager:**
- Navigate to `public_html/conf/`
- Select `settings.xml`
- Click **Copy** at the top
- In the window, navigate to `public_html → n → conf`
- Click **Copy** in the window

**Using SSH:**
```bash
cd ~/domains/yourdomain.com/public_html
cp -f conf/settings.xml n/conf/settings.xml
```

> **Why?** This carries over your database credentials and all existing settings to the new version.

---

## ✏️ Step 7 — Update rootDir in settings.xml

Open `public_html/n/conf/settings.xml` in File Manager.

Find the `rootDir` line and update it:

**From:**
```
rootDir="/home/username/domains/yourdomain.com/public_html/www/"
```

**To:**
```
rootDir="/home/username/domains/yourdomain.com/public_html/n/www/"
```

Save the file.

> **Why?** This tells SeedDMS where the new version code is located. Without this, SeedDMS will not start correctly.

---

## 🌐 Step 8 — Run Database Migration

Open your browser and visit:

```
https://yourdomain.com/n/www/install/
```

- Click **Start Installation**
- Follow the on-screen steps

The installer will detect your existing database and run only the required upgrade scripts. Your data will NOT be deleted.

> 📌 If you see an error saying "Please create ENABLE_INSTALL_TOOL file", create an empty file named `ENABLE_INSTALL_TOOL` inside `public_html/n/conf/`

> **Why?** New versions often include database changes. Without running migration, the new version will crash or show errors.

---

## 🖼️ Step 9 — Copy Custom Images

**What to do:** Copy your custom logo and image files to the new version.

- **From:** `public_html/seeddms-OLD.VERSION/views/bootstrap/images/`
- **To:** `public_html/n/seeddms-NEW.VERSION/views/bootstrap/images/`

**Using File Manager:**
- Navigate to the From path above
- Select your custom image files
- Click **Copy**
- In the window, navigate to the To path above
- Click **Copy** in the window

> **Why?** Without this, your custom branding will disappear and default SeedDMS logo will show.

---

## 🌐 Step 10 — Copy Custom Language File

**What to do:** Copy the language file that has your custom text labels.

- **From:** `public_html/seeddms-OLD.VERSION/languages/en_GB/lang.inc`
- **To:** `public_html/n/seeddms-NEW.VERSION/languages/en_GB/lang.inc`

**Using File Manager:**
- Navigate to the From path above
- Select `lang.inc`
- Click **Copy**
- In the window, navigate to the To path above
- Click **Copy** in the window

> **Why?** Without this, all your custom text labels will reset to default SeedDMS text.

---

## 🧪 Step 11 — Test on Staging URL

Open your browser and visit:

```
https://yourdomain.com/n/www/
```

Verify:
- ✅ Login works
- ✅ All documents and folders visible
- ✅ Custom images appear correctly
- ✅ Charts load (Admin Tools → Charts)
- ✅ Language/text labels correct

> **Why?** Testing here first means if something is wrong, your live site is still running the old version — no users are affected.

---

## 📦 Step 12 — Move Old Files to Backup Folder

Go to **hPanel → File Manager → public_html**

- Create a new folder called `old_backup`
- Select all existing files and folders of the main domain
- Move them into `old_backup`

> ⚠️ **Do NOT move or touch the `data/` folder at any point.**

> **Why?** This clears public_html for the new version while keeping old files safe in case you need to rollback.

---

## 🚀 Step 13 — Move New Version Files Live

Go to **hPanel → File Manager → public_html → n**

- Select all files and folders inside `n`
- Move them to `public_html` (one level up)

> ⚠️ **Do NOT select or move the `data/` folder.**

> **Why?** This makes the new version go live on your main domain.

---

## ✏️ Step 14 — Update rootDir Back to Live Path

Open `public_html/conf/settings.xml` in File Manager.

Update rootDir back:

**From:**
```
rootDir="/home/username/domains/yourdomain.com/public_html/n/www/"
```

**To:**
```
rootDir="/home/username/domains/yourdomain.com/public_html/www/"
```

Save the file.

---

## 📊 Step 15 — Fix Charts (tablesort)

**What to do:** Copy the tablesort folder from bootstrap4 theme into bootstrap theme.

- **From:** `public_html/seeddms-X.X.X/views/bootstrap4/vendors/tablesort`
- **To:** `public_html/seeddms-X.X.X/views/bootstrap/vendors/`

**Using File Manager:**
- Navigate to `public_html/seeddms-X.X.X/views/bootstrap4/vendors/`
- Select `tablesort` folder
- Click **Copy**
- In the window, navigate to `public_html → seeddms-X.X.X → views → bootstrap → vendors`
- Click **Copy** in the window

**Using SSH:**
```bash
cd ~/domains/yourdomain.com/public_html/seeddms-X.X.X
cp -r views/bootstrap4/vendors/tablesort views/bootstrap/vendors/
```

> **Why?** Without this, the Charts section under Admin Tools will not load. Do this after every upgrade.

---

## ☁️ Step 16 — Purge Cloudflare Cache

Go to your Cloudflare dashboard:

**Caching → Purge Cache → Purge Everything → Confirm**

Then hard refresh your browser: `Ctrl + Shift + R`

> **Why?** Without purging cache, browser and Cloudflare may still serve old cached files making the site look broken.

---

## ✅ Step 17 — Final Verification

Visit your main domain and verify:

- ✅ Login works
- ✅ All documents and folders visible
- ✅ Custom images showing correctly
- ✅ Charts loading (Admin Tools → Charts)
- ✅ Version shows new version number (Admin Tools → Version Information)

---

## 🔁 Rollback — How to Go Back

If something goes wrong:

1. Create a folder `new_broken` in `public_html`
2. Move all new version files there (do NOT touch `data/`)
3. Move all files from `old_backup` back to `public_html`
4. Verify `conf/settings.xml` rootDir is pointing to `public_html/www/`
5. Purge Cloudflare cache
6. Hard refresh browser

> ✅ The `data/` folder is never touched during upgrade or rollback — all your documents are always safe.

---

> For common errors, see [troubleshooting.md](troubleshooting.md)
