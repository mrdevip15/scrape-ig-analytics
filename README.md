# English Academy Instagram Analytics - GitHub Pages Repo

Repository ini berisi web dashboard analytics statis yang siap dideploy ke **GitHub Pages**.

## 🚀 Cara Deploy ke GitHub Pages

1. Inisialisasi repo git baru di folder ini (atau upload seluruh isi folder ini ke repository GitHub baru Anda):
   ```bash
   git init
   git add .
   git commit -m "Initial commit for GitHub Pages Analytics"
   git branch -M main
   git remote add origin https://github.com/USERNAME/NAMA-REPO.git
   git push -u origin main
   ```

2. Aktifkan GitHub Pages:
   - Buka **Settings** -> **Pages** di repository GitHub Anda.
   - Pada **Source**, pilih **Deploy from a branch**.
   - Pilih branch **main** dan folder **/(root)**.
   - Klik **Save**.

3. Website Analytics Anda akan langsung aktif secara publik! 🎉

## 📁 Struktur Berkas

- `index.html`: Halaman Dashboard Analytics Utama
- `top-performers.html`: Halaman Leaderboard Konten Performa Tertinggi
- `data/2026-08/`: Dataset statis 28 cabang, `dashboard.json`, `summary.csv`, dan thumbnail
- `ANALYTICS.md`: Dokumentasi analitik & arsitektur data
- `.nojekyll`: Memastikan file/folder bertanda khusus dibaca oleh GitHub Pages
