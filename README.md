# instabox-keepalive

Autowake cron untuk VPS InstaBox `hermesjabrix`.

## Kenapa repo ini ada
VPS free-tier InstaBox punya dua jenis mati:

| Jenis | Pemicu | Bisa dilawan dari dalam VPS? |
|---|---|---|
| idle-suspend | 30 menit tanpa aktivitas | ya (CPU burst) |
| maxRunMinutes | 8 jam jalan terus | **tidak** — mesin dimatikan paksa |

Cron di dalam VPS ikut mati bareng mesinnya. Jadi pemicu wake harus dari luar.
GitHub Actions menjalankan `POST /v1/machines/{id}/wake` tiap 20 menit.

## Setup (sekali saja)
Settings → Secrets and variables → Actions → New repository secret:

- `INSTABOX_API_KEY` — API key InstaBox
- `INSTABOX_MACHINE_ID` — id mesin

Wajib pakai **Secrets**, bukan Variables — API key tidak boleh terbaca publik.

## Verifikasi
Tab **Actions** → `instabox-keepalive` → **Run workflow** (manual) → log harus
menampilkan `wake http_code=200`.

## Batasan yang jujur
- GitHub schedule bisa telat 5–15 menit saat server mereka sibuk.
- Kalau `maxRunMinutes` habis, mesin mati sampai tick berikutnya nyalain lagi —
  downtime beberapa menit, bukan nol.
- 8 jam batas keras tetap ada. Untuk uptime 24/7 murni: upgrade plan InstaBox.
