# instalasi forgejo

## Langkah 1: Instalasi dan Konfigurasi MariaDB

1. Instal package MariaDB dan lakukan inisialisasi direktori data:
   ```bash
   sudo pacman -S mariadb --noconfirm
   ```
   ```
   sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
   ```

2. Aktifkan dan jalankan service MariaDB:
   ```bash
   sudo systemctl enable --now mariadb
   ```
   
3. Masuk ke prompt MariaDB untuk membuat database dan user khusus Forgejo:
   ```bash
   sudo mariadb -u root -p
   ```

4. Jalankan perintah SQL berikut di dalam prompt MariaDB (ganti `password_kamu` dengan password yang kuat):
   ```sql
   CREATE DATABASE forgejo CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
   CREATE USER 'forgejo'@'localhost' IDENTIFIED BY 'password_kamu';
   GRANT ALL PRIVILEGES ON forgejo.* TO 'forgejo'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

---

## Langkah 2: Instalasi dan Konfigurasi Awal Forgejo

Instal package Forgejo:
```bash
sudo pacman -S forgejo --noconfirm
```
```
sudo systemctl enable forgejo 
```

```
sudo systemctl start forgejo
```

## langkah terakhir akses melalu web
```
https://localhost:3000
```

# Dokumentasi Custom Theme & Layout Forgejo — Yuros Guideline

## Cara Menerapkan Perubahan (Deployment)

Masuk sebagai *root* terlebih dahulu:

```bash
sudo su

```

Buat kerangka folder awal (hanya dilakukan sekali jika belum ada):

```bash
mkdir -p /var/lib/forgejo/custom/public/assets/css
```
```
mkdir -p /var/lib/forgejo/custom/templates/custom
```


## cloning repo dari github 
```
git clone https://github.com/KiruyaaR26/forgezo-project.git
```
```
sudo cp -r forgezo-project/templates/*  /var/lib/forgejo/custom/templates
```
```
sudo cp -r forgezo-project/assets/css/custom.css /var/lib/forgejo/custom/public/assets/css
```
```
sudo mv /var/lib/forgejo/custom/templates/headers.tmpl /var/lib/forgejo/custom/templates/custom/extra_links.tmpl
```


### Update CSS

```bash
sudo chown -R forgejo:forgejo /var/lib/forgejo/custom
```
```
sudo systemctl restart forgejo
```

### Update/Tambah Template Layout

Berikut adalah 2 contoh penerapan berdasarkan lokasi file sumber di GitHub:

**Contoh 1: Template berada DI DALAM sub-folder (Contoh: `explore/repo_list.tmpl`)**

1. Buat folder `explore` di dalam templates terlebih dahulu:
```bash
sudo mkdir -p /var/lib/forgejo/custom/templates/explore

```


2. Buat file templatenya dengan Neovim:
```bash
sudo nvim /var/lib/forgejo/custom/templates/explore/repo_list.tmpl

```
**Terapkan Perubahan (Wajib dijalankan setelah edit .tmpl apapun):**
Setelah selesai mengedit file, kembalikan *permission* folder ke user `forgejo` dan *restart* sistem agar layout baru dibaca:

```bash
sudo chown -R forgejo:forgejo /var/lib/forgejo/custom
```
```
sudo systemctl restart forgejo

```

### Verifikasi

Pastikan Forgejo berjalan normal tanpa *error*:

```bash
sudo systemctl status forgejo      # pastikan "active (running)"
```
```
sudo journalctl -u forgejo -n 50   # cek error kalau ada masalah

```

> Lalu lakukan hard refresh browser: **`Ctrl + Shift + R`**

---

## 5. Referensi Source Asli Forgejo

Untuk melihat isi file `.tmpl` original (sebelum di-custom), *source* resmi ada di:

```text
https://codeberg.org/forgejo/forgejo/src/branch/forgejo/templates

```
