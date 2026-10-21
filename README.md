# Portal Layanan Akademik – Universitas Pattimura (Prototipe)

Tugas Pertemuan 5: Responsive & Accessible Web Client. HTML + CSS saja (tanpa JavaScript).
Bukan situs resmi Universitas Pattimura. Form belum terhubung ke server.

Dibuat oleh: Hikmah S Ramadhani (202451019)

## Tautan

- Aplikasi: https://hkmsftr13-art.github.io/portal-layanan-akademik/
- Repositori: https://github.com/hkmsftr13-art/portal-layanan-akademik

## Struktur

```
portal-unpatti/
├── index.html      Beranda: pengantar + jalan pintas ke halaman lain
├── layanan.html    8 kartu layanan
├── alur.html       Alur permohonan (4 langkah)
├── form.html       Form permohonan
├── syarat.html     Tabel syarat dokumen (contoh)
├── faq.html        Pertanyaan umum
├── css/styles.css  CSS mobile-first, dipakai semua halaman
├── img/            logounpatti.png (informatif), ilustrasi.svg (dekoratif)
├── evidence/       screenshot hasil pengujian
└── README.md
```

- Tiap menu membuka halaman sendiri; halaman aktif ditandai `aria-current="page"`.
- Menu "Kontak" menuju footer yang ada di semua halaman.
- Menjalankan: buka `index.html` di browser, atau `python3 -m http.server 8000`.

## Keputusan desain

| Aspek | Keputusan | Alasan |
|---|---|---|
| Semantik | `header`, `nav`, `main`, `section`, `article` (kartu), `footer`, `aside` (banner) | Landmark jelas untuk screen reader |
| Heading | Satu `h1` per halaman, lalu `h2`/`h3` tanpa lompat level | Hierarki mengikuti isi |
| Layout | Mobile-first, Grid 1 → 2 (≥40em) → 4 kolom (≥64em) | Breakpoint saat konten mulai lebar |
| Form | `label for`/`id`, `fieldset`+`legend` untuk radio, `required`, `type=email`, `pattern`, `minlength`, `aria-describedby` | Validasi native, ARIA seperlunya |
| Tabel | `caption`, `th scope`, wadah scroll yang bisa difokus | Tabel lebar tidak merusak layout mobile |
| FAQ | `details`/`summary` | Buka-tutup native, bisa lewat keyboard |
| Fokus | Skip link, outline 3px, target sentuh ≥ 44px | Navigasi keyboard dan layar sentuh |
| Gambar | Logo beralt bermakna; ilustrasi `alt=""` | Ilustrasi hanya hiasan, tidak perlu dibacakan |

## Hasil pengujian

### 1. Pengujian otomatis (Playwright + axe-core 4.10.2)

| Uji | Hasil |
|---|---|
| Link dan resource internal, 6 halaman | Tidak ada yang rusak |
| Klik menu navigasi | Setiap menu membuka halamannya |
| Overflow horizontal (360 / 768 / 1280 px), 6 halaman | Tidak ada |
| axe-core (WCAG 2.0/2.1 A dan AA + best-practice), 6 halaman | 0 pelanggaran |
| Satu `h1` per halaman, urutan heading | Sesuai |
| Form kosong | Ditolak |
| NIM `123`, email `abc`, keperluan pendek | Ditolak |
| Semua kolom valid | Diterima |

### 2. Pemeriksaan manual dengan Browser DevTools (Edge)

**Elements / Computed – `.card-grid` di `layanan.html`**

| Viewport | `grid-template-columns` | Lebar konten | Hasil |
|---|---|---|---|
| 360 px | `328px` | 328 px | 1 kolom |
| 768 px | `360px 360px` | 736 px | 2 kolom |
| 1280 px | `268px 268px 268px 268px` | 1120 px | 4 kolom |

- Grid berubah sesuai breakpoint dan konten tidak melebihi lebar viewport.
- Bukti: `element-360px.png`, `element-768px.png`, `element-1280px.png`.

**Responsive Mode**

- Enam halaman diuji di 360, 768, dan 1280 px: tidak ada horizontal scroll, teks tetap terbaca.
- Di 360 px menu navigasi membungkus ke dua baris, bukan terpotong.
- Bukti: `<halaman>-<lebar>px.png` (beranda, layanan, alur, form, syarat, faq).

**Network – reload `index.html` (Disable cache aktif)**

| Resource | Status | Tipe | Ukuran | Waktu |
|---|---|---|---|---|
| index.html | 200 | document | 3,7 kB | 17 ms |
| styles.css | 200 | stylesheet | 8,3 kB | 117 ms |
| logounpatti.png | 200 | png | 560 kB | 126 ms |
| ilustrasi.svg | 200 | svg+xml | 984 B | 107 ms |
| logounpatti.png (permintaan kedua) | 200 | png | 560 kB | 119 ms |

- Total 5 request, semua 200, tidak ada resource gagal.
- DOMContentLoaded 77 ms, Load 201 ms, selesai 329 ms.
- Total transfer 1,1 MB, hampir seluruhnya dari `logounpatti.png`.
- Bukti: `network.png`.

**Keyboard dan fokus**

- Tab pertama menuju skip link; Enter memindahkan fokus ke `#konten-utama` (terlihat di URL).
- Tab berikutnya berpindah ke menu "Layanan" dengan outline oranye 3px yang jelas.
- Di form, tiap kontrol (input, select, radio, textarea, checkbox, tombol) bisa dicapai dengan Tab dan fokusnya terlihat.
- Bukti: `keyboard.png`, `fokus-keyboard-1280px.png`.

**Validasi form (versi online)**

- Mengirim form dengan NIM kosong ditolak browser: kolom NIM diberi border merah dan muncul pesan bawaan "Please fill out this field."
- Bukti: `form validasi kesalahan.png`.

### Temuan dan perbaikan

1. Banner prototipe berada di luar landmark (axe: `region`). Dibungkus `<aside aria-label>`.
2. Judul kartu di halaman Layanan melompat dari `h1` ke `h3` (axe: `heading-order`). Diganti `h2`.
3. Kolom Estimasi pada tabel syarat terpotong di 360 px. Ditambah petunjuk geser dan wadah scroll yang bisa difokus.
4. Network menunjukkan `logounpatti.png` berukuran 560 kB, jauh lebih besar dari semua resource lain.
5. Tag favicon memakai atribut `src`, seharusnya `href` (`<link rel="icon" src=...>`). Belum diperbaiki.

### Rencana perbaikan lanjutan

- Kompres `logounpatti.png` (atau buat versi 96×96 px / WebP) agar total transfer turun dari 1,1 MB.
- Ganti `src` menjadi `href` pada `<link rel="icon">` di semua halaman.

## Catatan

Email, alamat sekretariat, jam layanan, estimasi proses, dan syarat dokumen adalah data contoh.
