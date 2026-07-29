

# forgezo-project

# Dokumentasi Custom Theme & Layout Forgejo — Yuros Guideline

Forgejo mendukung 2 jenis *override* tanpa perlu *compile* ulang aplikasi:

| Jenis Override | Lokasi | Fungsi |
| --- | --- | --- |
| **CSS** | `custom/public/assets/css/custom.css` | Ubah warna, *spacing*, radius, *font*, *styling* elemen |
| **Template (`.tmpl`)** | `custom/templates/<path/sama/seperti/source>` | Ubah struktur HTML/layout halaman |

Semua file di dalam folder `custom/` **menimpa** file bawaan Forgejo dengan *path* yang sama persis. Forgejo akan otomatis pakai file di `custom/` kalau ada, tanpa perlu *restart-compile* — cukup `systemctl restart forgejo`.

### 1. Lokasi Folder Custom di Server (Arch Linux)

Berdasarkan struktur repository, berikut adalah letak folder di server:

```text
/var/lib/forgejo/custom/
├── public/assets/css/custom.css       ← semua CSS
└── templates/                         ← semua override HTML/layout
    ├── explore/
    │   └── repo_list.tmpl
    ├── repo/
    │   ├── issue/
    │   ├── create.tmpl
    │   ├── create_advanced.tmpl
    │   ├── create_basic.tmpl
    │   ├── create_from_template.tmpl
    │   ├── create_init.tmpl
    │   └── headers.tmpl
    ├── home.tmpl
    └── home_forgejo.tmpl

```

Cek lokasi pastinya di server dengan:

```bash
sudo grep -i custom_path /etc/forgejo/app.ini

```

---

## 2. Design Tokens (Yuros Guideline)

Semua warna & nilai visual didefinisikan sebagai *CSS variable* di paling atas `custom.css`, supaya konsisten dan gampang diubah dari 1 tempat:

```css
:root {
  --custom-bg-base:      #0C1016;  /* background utama halaman */
  --custom-bg-elevated:  #12161D;  /* background card/box */
  --custom-text-primary: #F8F8FF;  /* teks utama */
  --custom-text-muted:   #98A0AD;  /* teks sekunder/label */
  --custom-border:       #2D3444;  /* garis/border */
  --custom-accent:       #FF4500;  /* warna aksen (tombol, link, hover) */
  --custom-danger:       #DA3633;  /* warna error/overdue */

  --custom-radius-sm:    4px;
  --custom-radius-md:    8px;
  --custom-radius-lg:    10px;
  --custom-radius-pill:  999px;

  --custom-spring:       cubic-bezier(0.32, 0.72, 0, 1); /* easing animasi */
}

```

**Aturan:** Kalau mau ubah 1 warna di seluruh *instance* (misal ganti warna aksen), cukup ubah nilai di `:root` ini — **jangan** *hardcode* *hex value* langsung di *section* manapun di bawahnya.

---

## 3. Struktur `custom.css`

File dibagi per-*section* dengan *comment header*, urut dari global ke spesifik per halaman:

1. **Design Tokens** (`:root`) — semua variable warna & radius
2. **Typography** — font family global (Inter/Manrope + JetBrains Mono untuk code)
3. **Global Layout** — styling dasar card/segment/table yang dipakai di banyak halaman
4. **Navbar/Header** — styling header atas (termasuk versi transparan)
5. **Homepage** — hero section + bento grid 4 kartu fitur
6. **Explore — Repo List** — card repo di halaman `/explore/repos`
7. **Issue List** — card issue/PR di `shared/issuelist.tmpl`
8. **Project — New/Edit** — layout 2 kolom form + preview
9. **Milestone List** — card milestone dengan progress bar custom
10. **New Repository** — form pembuatan repo baru

*Section* baru selalu ditambahkan di **paling bawah** dengan format *comment* yang sama:

```css
/* ==========================================================================
   NAMA SECTION (path/file/asal.tmpl)
   ========================================================================== */

```

---

## 4. Cara Menerapkan Perubahan (Deployment)

Masuk sebagai *root* terlebih dahulu:

```bash
sudo su

```

Buat kerangka folder awal (hanya dilakukan sekali jika belum ada):

```bash
mkdir -p /var/lib/forgejo/custom/public/assets/css
mkdir -p /var/lib/forgejo/custom/templates

```

### A. Update CSS

Buka file CSS:

```bash
sudo nvim /var/lib/forgejo/custom/public/assets/css/custom.css

```

> Lalu isi dengan file `custom.css` yang ada di GitHub.

*Paste*/*edit* isinya, *save*, lalu jalankan:

```bash
sudo chown -R forgejo:forgejo /var/lib/forgejo/custom
sudo systemctl restart forgejo

```

### B. Update/Tambah Template Layout

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


> Paste isi file dari GitHub, lalu save.



**Contoh 2: Template berada LANGSUNG di luar (Contoh: `home.tmpl`)**
Karena file ini tidak masuk ke dalam sub-folder apapun, kamu bisa langsung membuatnya di dalam folder `templates`:

1. Buat file templatenya dengan Neovim:
```bash
sudo nvim /var/lib/forgejo/custom/templates/home.tmpl

```


> Paste isi file dari GitHub, lalu save.



**Terapkan Perubahan (Wajib dijalankan setelah edit .tmpl apapun):**
Setelah selesai mengedit file, kembalikan *permission* folder ke user `forgejo` dan *restart* sistem agar layout baru dibaca:

```bash
sudo chown -R forgejo:forgejo /var/lib/forgejo/custom
sudo systemctl restart forgejo

```

### C. Verifikasi

Pastikan Forgejo berjalan normal tanpa *error*:

```bash
sudo systemctl status forgejo      # pastikan "active (running)"
sudo journalctl -u forgejo -n 50   # cek error kalau ada masalah

```

> Lalu lakukan hard refresh browser: **`Ctrl + Shift + R`**

---

## 5. Referensi Source Asli Forgejo

Untuk melihat isi file `.tmpl` original (sebelum di-custom), *source* resmi ada di:

```text
https://codeberg.org/forgejo/forgejo/src/branch/forgejo/templates

```
