---
site: suarawalet.id
deploy: { host: unknown, cf_account: null, branch: main, deploy_hook: unknown }
url_patterns: { artikel: "/berita/{slug}.html (flat .html, no trailing slash; sebagian artikel nyangkut di root /{slug}.html)", kota: "/kota/{slug} CAMPUR: {slug}.html DAN {slug}/index.html", page: "/{page}/ folder (/beranda, /tentang, /contact)", desain: "n/a" }
chrome: { header_lines: "119-298", footer_lines: "1026-1054", byte_identical: true }
analytics: { gtm: "GTM-K78H78X", ga4_property_id: "", tiktok: "", fb_verify: "aglluzokjnkhpdjh9khurpabghin47", adsense: "" }
brand: { primary: "#f8c300", font: "Jost", logo: "/wp-content/uploads/2023/04/cropped-LOGO-SUARA-WALET-e1680581192158.png", favicon: "/wp-content/uploads/2023/04/cropped-LOGO-SUARA-WALET-FAVICON-e1680581140187-32x32.png" }
ecosystem_menu: absent
contact: { wa: "6287725260196", email: "markaswaletcom@gmail.com" }
media: { local_mb: 260, hotlink: "flaticon:328 (icons only, non-content)" }
content: { articles: 166, kota_unique: 489, kota_admin_level: kabupaten }
cleanup:
  - "kota: 970 entri total, 481 duplikat bersuffix -2/-3/-4 (thin/cruft) -> 301-consolidate ke base (kota unik nyata ~489)"
  - "url kota campur: 489 {slug}.html + 481 {slug}/index.html -> standarkan satu pola"
  - "artikel nyangkut di root /{slug}.html (mis. suara-undang-burung-walet.html) -> normalisasi ke /berita/ + 301"
  - "demo cruft Astra -> hapus/noindex: hello-world.html, elementor-2041.html, folder course/ courses/ produk/ services/ blog/ blog-2/ contact-3/ home/"
  - "flaticon icon hotlink (328 ref) -> self-host saat build"
blockers:
  - "deploy target tidak diketahui dari repo (tidak ada CNAME / .github workflow / netlify/vercel/wrangler config) -> pemilik konfirmasi host + branch produksi + deploy hook"
  - "akses tulis CMS ke repo -> rekomendasi GitHub App dengan contents:write pada org Website-Markas-Walet (scoped, reusable lintas situs)"
---

# Catatan kesiapan integrasi CMS — suarawalet.id

## Identitas teknis
Situs **statis hasil export WordPress 2023** (WordPress 6.2, tema **Astra**, page builder **Elementor 3.11.2**, SEO **Rank Math**, form **Fluent Forms + WPForms Lite**, export via **Simply Static**). **Tidak ada PHP / database / REST API hidup** — `wp-json/` di repo hanya JSON beku. Kontrol satu-satunya = **git** (CMS generate/patch HTML lalu commit). Total ±2.664 file HTML, ±3.700 file, media 260MB.

## Blocker (butuh pemilik) — detail
- **Deploy:** temuan negatif pasti — tidak ada `CNAME`, `.github/workflows/`, `.nojekyll`, `netlify.toml`, `vercel.json`, `wrangler.toml`, `_redirects`. Deploy dikendalikan di luar repo. Pemilik perlu jawab: host mana (Cloudflare Pages / Netlify / cPanel-git-pull), akun CF (#1/#2) bila relevan, branch produksi (dugaan `main`), dan ada/tidaknya Deploy Hook.
- **Akses tulis:** rekomendasi GitHub App `contents:write` pada org `Website-Markas-Walet` (lebih baik dari deploy key/PAT: scoped + auditable + reusable untuk 12 situs).

## Chrome
Astra, **byte-identik di seluruh halaman** (bersih, kandidat baik untuk template). Batas di `index.html`:
- Header: baris **119–298** (`<header id="masthead">` … `</header>`)
- Slot konten mulai `id="content"` / `<main id="main">` (baris ~299–306)
- Footer: baris **1026–1054** (`<footer id="colophon">` … `</footer>`)

Menu navigasi nyata: **Beranda · Tentang Kami · Berita** (3 item). **Belum ada menu ekosistem/jaringan** → kandidat bagus untuk ditambahkan dari kontrol pusat.

## Konten
- **Artikel:** ~166 berita (`/berita/{slug}.html`, flat) + beberapa di root. Isi Elementor "kotor" → butuh parser (bukan regex) untuk memisahkan body dari markup tema.
- **Kota:** ±489 kabupaten unik, pola `suarawalet-{kabupaten}-{provinsi}`, **admin_level = kabupaten** (beda granularitas dari sarangwalet yang per-kota → beri flag saat digabung ke dataset master). Ini situs kota **terbesar** di jaringan. 481 sisanya = duplikat suffix.

## Media
Hampir seluruhnya **lokal** (260MB, gambar jpg/png di `wp-content/uploads/{2020,2022,2023}`). Hotlink eksternal **ringan**: hanya ikon `cdn-icons-png.flaticon.com` (328 ref, dekoratif). **Rehome ke R2 minimal.** Audio (produk "suara walet") **tidak ada di repo** — diurus di luar.

## Scope aman
CMS bersifat **aditif**: generate/edit konten (artikel baru, edit page, dataset kota) lalu commit. **Tidak** mengubah navbar/menu/footer/URL existing. Perubahan slug apa pun harus disertai peta redirect 301 (lihat `cleanup`).
