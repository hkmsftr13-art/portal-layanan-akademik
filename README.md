# Portal Layanan Akademik – Universitas Pattimura (Prototipe)

Prototipe tugas kuliah: web client responsif dan aksesibel, HTML + CSS saja (tanpa JavaScript).
Bukan situs resmi Universitas Pattimura. Form belum terhubung ke server.

Dibuat oleh: Hikmah S Ramadhani (202451019)

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
├── img/            logo.svg (informatif), ilustrasi.svg (dekoratif)
├── evidence/       screenshot hasil pengujian
└── README.md
```

Tiap menu di navigasi membuka halaman sendiri. Menu halaman yang sedang dibuka ditandai `aria-current="page"`. Menu "Kontak" menuju footer yang ada di semua halaman.

Cara menjalankan: buka `index.html` di browser, atau `python3 -m http.server 8000`.

## Keputusan desain

| Aspek | Keputusan | Alasan |
|---|---|---|
| Semantik | `header`, `nav`, `main`, `section`, `article` (kartu), `footer`, `aside` (banner) | Landmark jelas untuk screen reader |
| Heading | Satu `h1` per halaman (judul halaman), lalu `h2` dan `h3` tanpa lompat level | Hierarki mengikuti isi |
| Layout | Mobile-first, Grid 1 → 2 (≥40em) → 4 kolom (≥64em) | Breakpoint saat konten mulai lebar |
| Form | `label for`/`id`, `fieldset`+`legend` untuk radio, `required`, `type=email`, `pattern`, `minlength`, `aria-describedby` | Validasi native, ARIA seperlunya |
| Tabel | `caption`, `th scope`, dibungkus wadah scroll yang bisa difokus | Tabel lebar tidak merusak layout mobile |
| FAQ | `details`/`summary` | Buka-tutup native, bisa dipakai keyboard |
| Fokus | Skip link, outline 3px, target sentuh ≥ 44px | Navigasi keyboard dan layar sentuh |
| Gambar | Logo beralt bermakna, ilustrasi `alt=""` | Dekoratif tidak dibacakan |

## Hasil pengujian

Diuji dengan headless Chromium (Playwright) dan axe-core 4.10.2, bukan DevTools manual.

| Uji | Hasil |
|---|---|
| Link dan resource internal, semua halaman | Tidak ada yang rusak |
| Klik menu navigasi | Layanan, Alur Permohonan, Form Permohonan, Syarat Dokumen, FAQ, Beranda masing-masing membuka halamannya |
| Overflow horizontal (360 / 768 / 1280 px), 6 halaman | Tidak ada |
| axe-core (WCAG 2.0/2.1 A dan AA + best-practice), 6 halaman | 0 pelanggaran |
| Satu `h1` per halaman, urutan heading | Sesuai |
| Form kosong | Ditolak |
| NIM `123`, email `abc`, keperluan pendek | Ditolak |
| Semua kolom valid | Diterima |

Temuan yang diperbaiki saat pengujian:

1. Banner prototipe berada di luar landmark (axe: `region`). Dibungkus `<aside aria-label>`.
2. Judul kartu di halaman Layanan melompat dari `h1` ke `h3` (axe: `heading-order`). Diganti `h2`.
3. Kolom Estimasi pada tabel syarat terpotong di layar 360 px. Ditambah petunjuk geser dan wadah scroll yang bisa difokus.

Screenshot di folder `evidence/` (format `<halaman>-<lebar>px.png`), ditambah `fokus-keyboard-1280px.png` dan `fokus-form-360px.png` untuk indikator fokus.

## Yang perlu kamu lengkapi sendiri (jika tugas mewajibkan DevTools asli)

Ambil screenshot manual dari browser (F12): tab Network (semua status 200), Elements/Computed (`.card-grid` berubah kolom), Accessibility tree, dan skor Lighthouse. Lalu isi:

- Temuan debugging pribadi: _(isi)_
- URL aplikasi: _(isi link GitHub Pages / Netlify)_
- Repositori GitHub: _(isi link repo)_

## Catatan

Email, alamat sekretariat, jam layanan, estimasi proses, dan syarat dokumen adalah data contoh.
