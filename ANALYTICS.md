# Instagram Branch Scraper & Analytics Guide

Dokumentasi ini dirancang khusus sebagai panduan arsitektur data dan referensi teknis bagi **Pengembang (Manusia)** dan **AI Agent**.

---

## 📁 Arsitektur Direktori & File

```text
scrape-ig/
├── config.json               # Konfigurasi daftar 28 cabang Instagram (Nama & URL Profile/Reels)
├── login.mjs                 # Script autentikasi Playwright Instagram (menghasilkan storageState.json)
├── scrape.mjs                # Engine scraper engagement (Views, Likes, Comments, & Thumbnail images)
├── followers.mjs             # Engine scraper statistik profil master & riwayat jumlah follower
├── serve.mjs                 # Web server API & Dashboard lokal (Port 3000)
├── analytics/
│   └── index.html            # UI Web Dashboard Analytics (HTML5 + Vanilla CSS/JS)
└── data/
    ├── followers.json        # Master database profil & riwayat pertumbuhan follower seluruh cabang
    └── YYYY-MM/              # Direktori data bulanan (contoh: 2026-08)
        ├── summary.csv       # Ringkasan agregasi statistik performa per cabang (CSV)
        ├── branch-*.json     # File data detail postingan per cabang (JSON)
        └── thumbs/           # Direktori simpan gambar thumbnail per cabang
            └── {branch-slug}/
                └── {shortcode}.jpg
```

---

## 📊 Skema & Struktur Data

### 1. Data Detail Cabang (`data/YYYY-MM/branch-{slug}.json`)
Setiap cabang memiliki 1 file JSON independen yang memuat metrik agregat dan array seluruh postingan yang berhasil diserap.

```json
{
  "branch": "MKS Cendrawasih",
  "url": "https://www.instagram.com/englishacademy.mkscendrawasih/reels/",
  "scraped_at": "2026-08-31T07:31:00.000Z",
  "month": "2026-08",
  "from_date": null,
  "summary": {
    "total": 51,
    "total_reels": 48,
    "total_posts": 3,
    "total_views": 45120,
    "total_likes": 508920,
    "total_comments": 5890,
    "avg_views_per_reel": 940,
    "followers": 3943,
    "followers_checked_at": "2026-08-31T07:41:34.380Z",
    "following": 182,
    "total_ig_posts": 319,
    "is_business": true,
    "category": "Education",
    "city_name": "Makassar"
  },
  "posts": [
    {
      "branch": "MKS Cendrawasih",
      "shortcode": "DcnjEbNzSW8",
      "pk": "3974299431906125244",
      "type": "reel/video",
      "views": 1357,
      "likes": 3,
      "comments": 0,
      "taken_at": 1787993670,
      "thumb_url": "https://..."
    }
  ]
}
```

### 2. Database Profil & Follower (`data/followers.json`)
Menyimpan informasi metadata profil dan histori perkembangan jumlah follower per cabang dari waktu ke waktu.

```json
{
  "updated_at": "2026-08-31T07:41:34.437Z",
  "branches": {
    "MKS Cendrawasih": {
      "history": [
        { "date": "2026-08-31", "followers": 3943 }
      ],
      "username": "englishacademy.mkscendrawasih",
      "full_name": "English Academy Center Makassar - Cendrawasih",
      "followers": 3943,
      "following": 182,
      "total_posts": 319,
      "is_private": false,
      "is_verified": false,
      "biography": "Kursus bahasa Inggris interaktif...",
      "last_checked": "2026-08-31T07:41:34.380Z"
    }
  }
}
```

### 3. AgregasiCSV (`data/YYYY-MM/summary.csv`)
File kompilasi seluruh cabang dalam 1 periode bulan untuk keperluan ekspor data spreadsheet / BI tools:
- `branch`: Nama cabang
- `total_posts`, `total_reels`, `total_items`: Jumlah konten per tipe
- `total_views`, `total_likes`, `total_comments`: Total performa interaksi
- `avg_views_per_reel`, `avg_likes_per_item`: Rata-rata per konten

---

## 🤖 Panduan Pemrosesan Data untuk AI Agent & Pengembang

Saat AI Agent atau sistem otomatis membaca dan memproses repository ini:

1. **Pemetaan File Cabang:** Data per cabang berada di `data/YYYY-MM/branch-{slug}.json`. Nama file mengikuti format *slugified* dari nama cabang di `config.json`.
2. **Kalkulasi Metrik Engagement:**
   - $\text{Total Engagement} = \text{Total Likes} + \text{Total Comments}$
   - $\text{Rata-rata Views per Reel} = \frac{\text{Total Views}}{\text{Total Reels}}$
3. **Format Waktu (`taken_at`):** Field `taken_at` menyimpan **Unix Timestamp (detik)**. Wajib dikonversi ke waktu lokal (`Asia/Makassar` / WITA atau WIB) untuk analisis distribusi jam/hari posting.
4. **Relasi Thumbnail Image:** Setiap item di array `posts` memeta file gambar thumbnail lokal pada path:
   `data/YYYY-MM/thumbs/{branch-slug}/{shortcode}.jpg`
5. **Pertumbuhan Follower:** Untuk analisis perbandingan dan pertumbuhan follower, bandingkan entry terkini di `followers.json` dengan nilai `history` sebelumnya.
