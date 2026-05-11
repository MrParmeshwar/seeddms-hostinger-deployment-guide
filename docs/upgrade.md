# 🔄 SeedDMS — Upgrade Guide (Hostinger Shared Hosting)

> Step-by-step guide to upgrade SeedDMS to a newer version on Hostinger Shared Hosting.

---

## ⚠️ Important Notes

- SSH is only required for **2 steps**: extracting the archive and fixing the vendor symlink
- Everything else can be done via **hPanel File Manager (GUI)**
- The `data/` folder should **never be touched** during upgrade — all your documents are safe
- Always test the new version inside a temporary folder before moving it live.  
 > In this guide, the temporary folder is named `/n/`, but you can use any folder name you prefer.

---

## 📥 Step 1 — Download the New Version

Download the latest version from the official website:

👉 [sourceforge.net/projects/seeddms/files](https://sourceforge.net/projects/seeddms/files)

Download: `seeddms-quickstart-X.X.X.tar.gz` to your computer.

> **Why?** Without the new version file, there is nothing to upgrade to.

---

## 💾 Step 2 — Backup Existing Files

Go to **hPanel → File Manager → public_html**

- Select and download the current live site files and folders listed below:

```text
composer.json
composer.lock
conf/
data/
seeddms/
seeddms-6.0.39/
vendor/
www/
```

- Click **Download** — a `.zip` file will be downloaded to your computer
- Save this backup file safely

> ⚠️ Also export a backup of your MySQL database before starting the upgrade.
>
> **Using phpMyAdmin:**
> 1. Open **hPanel → Databases → phpMyAdmin**
> 2. Select your SeedDMS database
> 3. Click **Export**
> 4. Choose **Quick** export method
> 5. Click **Export** and save the `.sql` file safely

> **Why?** If anything goes wrong during upgrade, these backups can be used to restore the previous working version.

---

## 📁 Step 3 — Create Temporary Folder and Upload New Version

Go to **hPanel → File Manager → public_html**

- Click **New Folder** and name it `n`
- Open the `n` folder
- Click **Upload** and upload the new version `.tar.gz` file

> 📌 In this guide, the temporary folder is named `n/`, but you can use any folder name you prefer.

> **Why?** The new version will be tested inside this temporary folder before replacing the live site. This helps avoid downtime or issues on the main domain during upgrade testing.
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

## 🔗 Step 5 — Create/Fix the vendor Symlink

> 🔵 **SSH Required for this step. Do this BEFORE running migration!**

Navigate to the temporary upgrade folder:

```bash
cd ~/domains/yourdomain.com/public_html/n
```
Now create the correct symlink:

```bash
ln -s /home/username/domains/yourdomain.com/public_html/vendor seeddms-X.X.X/vendor
```

Verify the symlink:

```bash
ls -la seeddms-X.X.X/ | grep vendor
```

Expected output:

```text
vendor -> /home/username/domains/yourdomain.com/public_html/vendor
```

> **Why?** SeedDMS expects the `vendor` directory to point to the shared `public_html/vendor` location.  
> If this symlink is missing or incorrect, the migration page may show HTTP 500 errors and the upgrade process can fail.
---

## 📋 Step 6 — Copy Old settings.xml

**What to do:** Replace the new `settings.xml` file with the existing live configuration file.

- **From:** `public_html/conf/settings.xml`
- **To:** `public_html/n/conf/settings.xml`

> 📌 After extracting the new SeedDMS version, a default `settings.xml` file will already exist inside `public_html/n/conf/`.  
> Replace/overwrite that file with the current live `public_html/conf/settings.xml`.

### Using File Manager

- Navigate to `public_html/conf/`
- Select `settings.xml`
- Click **Copy** at the top
- In the window, navigate to `public_html → n → conf`
- Click **Copy**
- If prompted, choose **Replace/Overwrite**

### Using SSH

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
> 📌 If a `.htaccess` file already exists inside `public_html/`, temporarily disable or rename it before opening the upgrade URL inside the `n/` folder.
>
> Example:
>
> ```text
> .htaccess → .htaccess_old
> ```
>
> Re-enable it after the upgrade and testing process is completed.

Open your browser and visit:

```text
https://yourdomain.com/n/www/install/
```

- Click **Start Installation**
- Follow the on-screen upgrade steps

The installer will automatically detect your existing database and run only the required upgrade scripts. Your existing documents and database records will NOT be deleted.

> ⚠️ While updating the application, do NOT select/tick the database creation checkbox.
>
> That option is only used during fresh installation and may overwrite the existing database structure/data.

> 📌 If you see an error saying `Please create ENABLE_INSTALL_TOOL file`, create an empty file named `ENABLE_INSTALL_TOOL` inside:
>
> ```text
> public_html/n/conf/
> ```

> **Why?** New SeedDMS versions may include database changes. Running the migration updates the existing database structure required by the new version.

---

## 🖼️ Step 9 — Copy Custom Images

**What to do:** Copy the entire `images` folder from the current live version to the new version.

- **From:** `public_html/seeddms-OLD.VERSION/views/bootstrap/images/`
- **To:** `public_html/n/seeddms-NEW.VERSION/views/bootstrap/images/`

### Using File Manager

- Navigate to the source path above
- Select the entire `images` folder
- Click **Copy**
- In the window, navigate to the destination path above
- Click **Copy** in the window

> 📌 This folder contains website branding assets such as custom logos and other images used on the webpage.

> **Why?** Copying the entire folder ensures all custom website images continue to appear correctly after the upgrade.

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

### Using SSH

```bash
cd ~/domains/yourdomain.com/public_html
cp -f seeddms-OLD.VERSION/languages/en_GB/lang.inc n/seeddms-NEW.VERSION/languages/en_GB/lang.inc
```


> **Why?** Without this, all your custom text labels will reset to default SeedDMS text.

---
## 📊 Step 11 — Fix Charts (tablesort)

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
## 🧪 Step 12 — Test the Updated Version
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

## 📦 Step 13 — Move Current Live Files to Backup Folder

Go to **hPanel → File Manager → public_html**

- Create a new folder called `old_backup`
- Move the current live SeedDMS files and folders listed below into `old_backup`:

```text
composer.json
composer.lock
conf/
seeddms/
seeddms-OLD.VERSION/
vendor/
www/
```

Example:

```text
seeddms-6.0.39/
```

> ⚠️ Do NOT move or modify:
>
> ```text
> data/
> n/
> old_backup/
> ```
>
> The `data/` folder contains all uploaded documents and existing data.

> **Why?** This keeps the previous working version available for rollback while preparing `public_html/` for the updated version.

---

## 🚀 Step 14 — Move the Updated Version Live

Go to **hPanel → File Manager → public_html → n**

- Move the updated SeedDMS files and folders listed below from `n/` to `public_html/`:

```text
composer.json
composer.lock
conf/
seeddms/
seeddms-NEW.VERSION/
vendor/
www/
```

Example:

```text
seeddms-6.0.38/
```

> 📌 Depending on the SeedDMS version, some files or folders may be added, removed, or renamed in future releases.
>
> Always compare the current live files with the upgraded version before moving them live.

> ⚠️ Do NOT move or replace:
>
> ```text
> data/
> old_backup/
> ```
>
> The existing `data/` folder inside `public_html/` must remain unchanged.

> **Why?** This replaces the old application files with the updated version while preserving all existing documents and database-related data..

---

## ✏️ Step 15 — Update rootDir Back to Live Path

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
## ☁️ Step 16 — Purge Cloudflare Cache

Go to your Cloudflare dashboard:

```text
Caching → Purge Cache → Purge Everything → Confirm
```

Then hard refresh your browser:

```text
Ctrl + Shift + R
```

> 📌 If you temporarily disabled or renamed the `.htaccess` file earlier, restore it before testing the live site.
>
> Example:
>
> ```text
> .htaccess_old → .htaccess
> ```

> **Why?** Without purging cache, Cloudflare and the browser may continue serving old cached files, causing the updated website to appear broken or outdated.

---

## ✅ Step 17 — Final Verification

Visit your main domain and verify:

- ✅ Login works
- ✅ All documents and folders are visible
- ✅ Custom images appear correctly
- ✅ Charts load correctly (Admin Tools → Charts)
- ✅ Language/text customizations appear correctly
- ✅ Version Information shows the updated SeedDMS version

> **Why?** Final verification confirms that the updated version is working correctly on the live domain after the upgrade.
---

## 🔁 Rollback — Restore the Previous Version

If something goes wrong after the upgrade:

1. Create a folder named `new_broken` inside `public_html`
2. Move the newly updated SeedDMS files and folders into `new_broken` from `public_html` folder
3. Move the previous live files from `old_backup` back to `public_html`
4. Verify that `conf/settings.xml` is pointing to the correct live path:

```text
rootDir="/home/username/domains/yourdomain.com/public_html/www/"
```

5. Restore the `.htaccess` file if it was temporarily renamed earlier
6. Purge Cloudflare cache
7. Hard refresh the browser:

```text
Ctrl + Shift + R
```

> ⚠️ Do NOT modify or replace the `data/` folder during rollback.
>
> The `data/` folder contains all uploaded documents and existing application data.

> 📌 Depending on the SeedDMS version, file and folder names may vary.
>
> Always compare the previous live version with the updated version before moving files.

---

> For common upgrade issues and fixes, see [troubleshooting.md](troubleshooting.md)
