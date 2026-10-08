# Dokumen Teknis Modul 2 — HTML Semantik, Tailwind CSS, dan Aksesibilitas

Nama/NIM : Wisnu Bimantoro - 105224045
Repositori : https://github.com/wsnubmntr/105224045_Praktikum_Pemweb_Modul2

---

## 1. Struktur Semantik

### 1.1 Kerangka landmark dan hierarki judul halaman utama

Halaman utama (`app/page.tsx`) disusun dengan elemen semantik HTML sehingga setiap bagian memiliki peran *landmark* yang jelas. Atribut `lang="id"` dan metadata (`title`, `description`) diatur pada `app/layout.tsx`.

| Elemen HTML | Peran landmark ARIA | Isi pada halaman |
|---|---|---|
| `<a href="#konten">` (sr-only) | — (tautan lewati) | "Lewati ke konten utama", tampil hanya saat menerima fokus papan ketik |
| `<header>` | `banner` | Kepala halaman |
| `<nav aria-label="Navigasi utama">` | `navigation` | Logo NamaProduk, tautan Fitur dan Kontak |
| `<main id="konten">` | `main` | Seluruh konten utama (satu per halaman) |
| `<section aria-labelledby="judul-utama">` | `region` | Kalimat nilai utama dan penjelasan singkat |
| `<section id="fitur" aria-labelledby="judul-fitur">` | `region` | Kartu Fitur Utama |
| `<section aria-labelledby="judul-cara">` | `region` | Cara Kerja |
| `<aside aria-label="Informasi tambahan">` | `complementary` | Informasi tambahan mengenai produk |
| `<section id="kontak" aria-labelledby="judul-kontak">` | `region` | Formulir Hubungi Kami |
| `<footer>` | `contentinfo` | Informasi kaki halaman |

Hierarki judul berjalan runtut tanpa melompat tingkat:

```
h1  Kalimat nilai utama produk
├── h2  Fitur Utama
│   ├── h3  Fitur pertama
│   ├── h3  Fitur kedua
│   └── h3  Fitur ketiga
├── h2  Cara Kerja
└── h2  Hubungi Kami
```

Alasan pemilihan elemen:

- `<header>` dan `<footer>` hanya menjadi `banner` dan `contentinfo` bila tidak berada di dalam `<article>`, `<aside>`, `<main>`, `<nav>`, atau `<section>`. Keduanya diletakkan langsung di level atas halaman sehingga syarat ini terpenuhi.
- `<section>` baru menjadi *landmark* `region` bila memiliki nama yang dapat diakses, karena itu setiap `<section>` diberi `aria-labelledby` yang merujuk ke `id` judulnya.
- Halaman hanya memiliki satu `<main>` dan satu `<h1>`.
- Setiap kartu fitur memakai `<article>` di dalam `<li>`, sehingga daftar fitur dikenali sebagai daftar oleh teknologi bantu.

### 1.2 Tangkapan layar pohon aksesibilitas pada DevTools

> **[Lampirkan tangkapan layar]**: DevTools → Elements → tab Accessibility → aktifkan *Enable full-page accessibility tree*. Pastikan *landmark* banner, navigation, main, region, complementary, dan contentinfo terlihat.
>
> `![Pohon aksesibilitas](images/pohon-aksesibilitas.png)`

**Checkpoint 1:** halaman memiliki satu `<h1>`, judul `<h2>` untuk setiap section, satu `<main>`, dan *landmark* lengkap pada pohon aksesibilitas.

---

## 2. Tata Letak Responsif

### 2.1 Tangkapan layar pada lebar 360 px, 768 px, dan 1280 px

Pengujian memakai Device Toolbar Chrome DevTools (mode *Responsive*, tinggi 600 px) pada `http://localhost:3000`.

**Lebar 360 px** (ponsel): navigasi bertumpuk, kartu fitur 1 kolom.

![alt text](<WhatsApp Image 2026-10-08 at 23.21.56-1.jpeg>)

**Lebar 768 px** (tablet): navigasi mendatar, kartu fitur 2 kolom.

![alt text](<WhatsApp Image 2026-10-08 at 23.23.07-1.jpeg>)

**Lebar 1280 px** (desktop): kartu fitur 3 kolom, bagian *Cara Kerja* dan *aside* berdampingan.

![alt text](<WhatsApp Image 2026-10-08 at 23.23.35-1.jpeg>)

Pada ketiga lebar tersebut tidak terdapat gulir horizontal, teks tetap terbaca, dan tautan tetap mudah disentuh.

### 2.2 Kelas Flexbox, Grid, dan breakpoint yang digunakan beserta alasannya

Pendekatan yang dipakai adalah *mobile-first*: kelas tanpa awalan berlaku untuk semua ukuran layar, sedangkan kelas berawalan `sm:` (≥ 640 px) dan `lg:` (≥ 1024 px) hanya menyesuaikan tampilan untuk layar yang lebih lebar.

| Bagian | Kelas utama | Perilaku | Alasan |
|---|---|---|---|
| Kontainer navigasi | `mx-auto flex max-w-6xl flex-col gap-3 p-4 sm:flex-row sm:items-center sm:justify-between` | Bertumpuk di ponsel; mendatar dengan logo di kiri dan menu di kanan mulai 640 px | Navigasi hanya memerlukan satu dimensi (baris atau kolom), sehingga **Flexbox** paling tepat. `justify-between` memisahkan logo dan menu; `items-center` meratakan keduanya secara vertikal. |
| Daftar menu | `flex flex-col gap-2 sm:flex-row sm:gap-6` | Menu vertikal di ponsel, horizontal mulai 640 px | Jarak antarmenu dikontrol dengan `gap`, bukan margin manual. |
| Kartu fitur | `mt-6 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3` | 1 kolom (< 640 px), 2 kolom (≥ 640 px), 3 kolom (≥ 1024 px) | Kumpulan kartu tersusun dalam baris dan kolom sekaligus, sehingga **Grid** paling tepat. Jumlah kolom bertambah sesuai ruang yang tersedia. |
| Isi kartu | `h-full rounded-lg border p-6` | Semua kartu pada satu baris sama tinggi | `h-full` pada `<article>` membuat kartu mengisi tinggi sel grid. |
| Konten dan aside | `grid gap-8 lg:grid-cols-[2fr_1fr]` | Bertumpuk di bawah 1024 px; berdampingan dengan kolom konten dua kali lebih lebar dari aside mulai 1024 px | Nilai sembarang (*arbitrary value*) `[2fr_1fr]` memberi proporsi kolom yang tidak tersedia pada kelas bawaan. |
| Pembatas lebar | `mx-auto max-w-6xl p-4` | Konten berhenti melebar di 72 rem dan berada di tengah | Mencegah baris teks terlalu panjang pada layar besar. |
| Formulir | `mt-4 grid max-w-xl gap-4` | Kolom isian tersusun vertikal dengan lebar maksimum 36 rem | Pada dasarnya satu kolom sehingga aman di ponsel tanpa breakpoint tambahan. |

Hasil pengamatan terhadap tiga lebar uji:

| Lebar | Navigasi | Kartu fitur | Konten dan aside |
|---|---|---|---|
| 360 px | Bertumpuk (`flex-col`) | 1 kolom | Bertumpuk |
| 768 px | Mendatar (`sm:flex-row`) | 2 kolom (`sm:grid-cols-2`) | Bertumpuk (belum mencapai `lg`) |
| 1280 px | Mendatar | 3 kolom (`lg:grid-cols-3`) | Berdampingan (`lg:grid-cols-[2fr_1fr]`) |

**Checkpoint 2 dan 3:** navigasi mendatar dengan logo di kiri dan menu di kanan, kartu fitur 3 kolom dan konten berdampingan dengan aside pada desktop, serta tanpa gulir horizontal di 360 px, 768 px, dan 1280 px.

---

## 3. Audit Aksesibilitas

Audit dijalankan dengan panel Lighthouse pada Chrome DevTools: mode *Navigation*, perangkat *Mobile*, kategori *Accessibility*.

> Catatan: Lighthouse menampilkan peringatan *"There may be stored data affecting loading performance in this location: IndexedDB"*. Peringatan ini muncul karena audit tidak dijalankan di jendela Incognito. Menurut modul, audit sebaiknya diulang di Incognito/InPrivate agar ekstensi dan data tersimpan tidak memengaruhi skor.

### 3.1 Tabel skor Lighthouse sebelum dan sesudah perbaikan

| Halaman | Sebelum perbaikan | Sesudah perbaikan | Target |
|---|---|---|---|
| Halaman latihan (`/latihan-audit`) | [isi skor awal] | **100** | ≥ 90 |
| Halaman utama (`/`) | [isi skor awal, bila ada] | **96** | ≥ 85 |

Kedua halaman memenuhi target.

**Halaman latihan setelah perbaikan: skor 100**

![Lighthouse halaman latihan-audit, skor 100](images/lighthouse-latihan-audit-100.jpeg)

**Halaman utama: skor 96**

![Lighthouse halaman utama, skor 96](images/lighthouse-beranda-96.jpeg)

### 3.2 Daftar audit yang gagal, penyebab, dan perbaikannya

**a. Halaman latihan (`/latihan-audit`)**

| Audit yang gagal | Penyebab pada kode | Perbaikan |
|---|---|---|
| *Image elements do not have [alt] attributes* | `<img src="/next.svg" />` tidak memiliki atribut `alt` | Menambahkan `alt` yang menjelaskan isi gambar, atau `alt=""` bila gambar hanya dekoratif |
| *Background and foreground colors do not have a sufficient contrast ratio* | Teks `text-gray-300` di atas latar putih, rasio kontras jauh di bawah 4,5:1 | Mengganti dengan warna lebih gelap, yaitu `text-gray-700` |
| *Form elements do not have associated labels* | `<input type="search">` tidak memiliki label | Menambahkan `<label htmlFor="cari">Cari alat</label>` yang terlihat dan `id="cari"` pada input |
| *Buttons do not have an accessible name* | Tombol hanya berisi ikon SVG tanpa teks | Menambahkan `aria-label="Cari"` pada tombol dan `aria-hidden="true"` pada ikon SVG |

Perbaikan di luar audit otomatis (hanya terlihat lewat pemeriksaan manual pohon aksesibilitas):

| Masalah | Penyebab | Perbaikan |
|---|---|---|
| Judul halaman bukan *heading* | Judul ditulis dengan `<div className="text-2xl font-bold">` | Diganti dengan `<h1>` agar dikenali sebagai judul oleh pembaca layar |
| Placeholder tidak cukup sebagai label | Placeholder hilang saat mengetik dan sering berkontras rendah | Tetap memakai `<label>` yang terlihat |

Setelah seluruh perbaikan diterapkan, skor halaman latihan menjadi **100**.

**b. Halaman utama (`/`)**

Skor awal 96 dengan satu audit yang masih gagal:

| Audit yang gagal | Penyebab | Perbaikan |
|---|---|---|
| *Background and foreground colors do not have a sufficient contrast ratio* | Berdasarkan tangkapan layar tampilan, teks berwarna abu-abu (`text-gray-700` dan `text-gray-600`) berada di atas latar gelap bawaan tema gelap `create-next-app`, sehingga kontrasnya rendah. Judul "Informasi Tambahan" pada `<aside>` (latar `bg-gray-100`) juga tampak mewarisi warna teks terang dari tema gelap sehingga hampir tidak terbaca. | Menentukan pasangan warna teks dan latar secara eksplisit: pada latar gelap pakai teks terang (misalnya `text-gray-300`), pada `<aside>` berlatar terang pakai `text-gray-900`. Alternatifnya, menyeragamkan tema (semua terang atau semua gelap) lewat variabel di `app/globals.css`. Rasio kontras minimal 4,5:1 (teks besar 3:1). |

> **[Isi/ubah sesuai kondisi akhir]** Skor 96 sudah melewati target ≥ 85. Apabila perbaikan kontras di atas sudah diterapkan dan audit diulang, perbarui skor akhir dan tangkapan layar pada tabel 3.1.

### 3.3 Hasil pemeriksaan manual dengan papan ketik (urutan fokus dan garis fokus)

Lighthouse sendiri menandai 10 butir "*Additional items to manually check*" (misalnya *Interactive controls are keyboard focusable*, *The page has a logical tab order*, *Visual order on the page follows DOM order*, *User focus is not accidentally trapped in a region*) karena tidak dapat diperiksa secara otomatis. Pemeriksaan manual dilakukan dengan tombol Tab, Shift + Tab, Spasi, dan Enter.

> **[Verifikasi dan isi hasil sebenarnya]** Tabel di bawah adalah daftar yang diharapkan dari kode pada modul. Ubah kolom "Hasil" sesuai yang kamu amati langsung di peramban.

| Urutan Tab | Elemen yang menerima fokus | Garis fokus terlihat | Hasil |
|---|---|---|---|
| 1 | Tautan "Lewati ke konten utama" (muncul saat fokus) | Ya | [ ] Sesuai |
| 2 | Logo "NamaProduk" | Ya | [ ] Sesuai |
| 3 | Tautan "Fitur" | Ya | [ ] Sesuai |
| 4 | Tautan "Kontak" | Ya | [ ] Sesuai |
| 5 | Kolom "Nama lengkap" | Ya (`focus-visible:outline-2`, biru) | [ ] Sesuai |
| 6 | Kolom "Surel" | Ya | [ ] Sesuai |
| 7 | Radio "Peran" (Pengguna/Mitra; panah untuk berpindah antarpilihan) | Ya | [ ] Sesuai |
| 8 | Kolom "Pesan" | Ya | [ ] Sesuai |
| 9 | Tombol "Kirim" | Ya | [ ] Sesuai |

Hal yang diperiksa:

- Urutan fokus mengikuti urutan visual dari atas ke bawah dan tidak melompat.
- Garis fokus (`focus-visible:outline-2 outline-offset-2 outline-blue-700`) selalu terlihat pada setiap kolom isian dan tombol.
- Tidak ada fokus yang terjebak pada suatu area.
- Tautan "Lewati ke konten utama" memindahkan fokus ke `<main id="konten">`.
- Formulir dapat diisi dan dikirim hanya dengan papan ketik (Tab, Spasi untuk radio, Enter untuk mengirim).
- Label terhubung dengan kolom (`htmlFor` dan `id`), sehingga mengeklik label memindahkan fokus ke kolom; petunjuk surel dibacakan melalui `aria-describedby`; pilihan radio dikelompokkan oleh `<fieldset>` dan `<legend>`.

**Checkpoint 4 dan 5:** setiap kolom memiliki label terhubung, radio dikelompokkan dengan legend, formulir dapat dioperasikan dengan papan ketik, skor halaman latihan 100 (≥ 90), dan skor halaman utama 96 (≥ 85). Folder `app/latihan-audit` dihapus setelah hasil audit dicatat.

---

## 4. Kendala dan Penyelesaian

> **[Sesuaikan dengan kendala yang benar-benar kamu alami]** Berikut contoh yang sesuai dengan hasil pengerjaan.

| Kendala | Penyebab | Penyelesaian |
|---|---|---|
| Navigasi di layar 360 px tampak terpotong pada bagian atas tangkapan layar | Navigasi bertumpuk (`flex-col`) sehingga lebih tinggi, dan tangkapan layar diambil setelah halaman tergulir | Mengambil tangkapan layar dari posisi paling atas halaman, atau memakai *Capture screenshot* pada Device Toolbar |
| Kartu fitur terlalu sempit di ponsel pada kondisi awal | `grid-cols-3` tanpa awalan breakpoint | Mengganti dengan `grid-cols-1 sm:grid-cols-2 lg:grid-cols-3` |
| Kontras teks rendah pada halaman utama (audit kontras gagal, skor 96) | Warna abu-abu pada latar gelap bawaan template dan warna teks `aside` yang tidak eksplisit | Menetapkan pasangan warna teks dan latar secara eksplisit serta memverifikasi rasio ≥ 4,5:1 |
| Peringatan Lighthouse tentang IndexedDB | Audit dijalankan di jendela biasa dan data tersimpan memengaruhi hasil | Mengulang audit di jendela Incognito/InPrivate |

---

## 5. Catatan Pemanfaatan AI

- **Alat:** Claude (Anthropic).
- **Perintah utama:** meminta penjelasan konsep dan referensi sebagai bahan belajar, misalnya perbedaan Flexbox dan Grid, pendekatan *mobile-first* pada Tailwind CSS, peran *landmark* ARIA, serta cara membaca hasil audit Lighthouse (khususnya audit kontras warna) dan menyusun kerangka laporan.
- **Bagian yang digunakan:** AI dipakai hanya sebagai sarana belajar dan referensi untuk memahami konsep, dan sebagai bantuan merapikan susunan laporan. Pembuatan kode halaman (`app/page.tsx`, `app/layout.tsx`, formulir), pengujian di tiga ukuran layar, audit Lighthouse, dan pemeriksaan papan ketik dikerjakan sendiri.
- **Cara memverifikasi:**
  1. Mencocokkan penjelasan AI dengan sumber pada daftar pustaka modul (MDN Web Docs, dokumentasi Tailwind CSS, WCAG 2.2, dan dokumentasi Lighthouse).
  2. Mencoba langsung setiap kelas Tailwind pada kode dan memeriksa hasilnya di Device Toolbar pada lebar 360 px, 768 px, dan 1280 px.
  3. Mencocokkan skor (100 dan 96) serta audit yang gagal dengan tangkapan layar Lighthouse yang saya jalankan sendiri.
  4. Memeriksa *landmark* dan hierarki judul langsung pada pohon aksesibilitas DevTools.
  5. Memastikan saya dapat menjelaskan setiap kelas dan elemen yang digunakan pada halaman (sesuai Ketentuan Umum modul).
