# Laporan Praktikum Effect Custom Hook dan Router

Nama: Fransisca Noviana Prina Ramiro Soares
NPM: 255314083
Kelas: PBW CP
Versi Node dan npm: Node.js v24.21.0, npm 11.19.0
Versi React dan React Router: isi dari hasil `npm.cmd list react react-router`

## Tujuan
Membangun aplikasi React SPA berisi beranda, katalog, detail produk, favorit, dan halaman tentang. Berpindah halaman tanpa memuat ulang dokumen, dengan data favorit yang dipakai bersama dan tetap tersimpan setelah browser dimuat ulang.

## Pemetaan state
| Data | Pemilik | Keterangan |
|---|---|---|
| Daftar produk | Array PRODUCTS di App.jsx | Data tetap, tiap produk punya kategori |
| ID favorit | State di FavoritesProvider | Dipakai katalog, detail, favorit, dan badge navigasi |
| Penyimpanan favorit | localStorage, key `praktikum-favorites-v1` | Salinan agar data bertahan setelah reload |
| Kata pencarian dan kategori | URL (query `q` dan `category`) | Dibaca dengan useSearchParams |
| Jumlah favorit | Turunan dari `ids.length` | Dihitung saat render, tanpa state atau Effect tambahan |

## Penjelasan implementasi
1. **usePageTitle**: Effect mengubah `document.title`. Dependency `[title]` membuat Effect berjalan lagi hanya saat judul berubah. Cleanup mengembalikan judul sebelumnya saat halaman dilepas atau judul berganti.
2. **usePersistentFavorites**: `useState(readIds)` memakai lazy initializer sehingga localStorage hanya dibaca sekali saat state dibuat, dan data rusak dipulihkan menjadi array kosong. Effect dengan dependency `[ids]` menulis ke localStorage setiap `ids` berubah.
3. **useFavorites dan state bersama**: setiap pemanggilan `useState` membuat state baru milik komponen pemanggilnya. Jika Catalog dan Favorites memanggil `usePersistentFavorites()` sendiri-sendiri, keduanya punya `ids` terpisah, dan menulis ke key localStorage yang sama tidak membuat komponen lain dirender ulang di tab yang sama. Karena itu hanya `FavoritesProvider` yang memanggil `usePersistentFavorites()`, dan semua komponen membaca satu nilai Context yang sama lewat `useFavorites()`. Provider dipasang di main.jsx di atas App sehingga state tidak hilang saat pindah route.
4. **Route dinamis dan pencarian**: route `/products/:id` dibaca dengan `useParams` (hasilnya string, maka dibandingkan dengan `String(item.id)`). Pencarian dan kategori dibaca dari URL dengan `useSearchParams` dan diubah dengan `setParams`, sehingga tombol Back dan Forward memulihkan pencarian.

## Eksperimen

- StrictMode: tambahkan sementara `console.log` pada setup dan cleanup `usePageTitle`, lalu catat urutan log saat pindah halaman.
- Dependency `[ids]` menjadi `[]`: tambah favorit lalu reload, catat hasilnya, lalu kembalikan ke `[ids]`.

## Hasil pengujian
Lihat `Lembar_Pengujian.md` yang sudah diisi, beserta bukti screenshot.

## Tugas mandiri
1. **Filter kategori**: setiap produk punya `category` (Buku atau Aksesori). Parameter `category` dibaca dari URL dan digabung dengan `q`. Fungsi `updateParam` menyalin parameter yang ada sebelum mengubah satu parameter, sehingga `q` dan `category` saling dipertahankan.
2. **Hapus semua favorit**: fungsi `clear` di `usePersistentFavorites` memanggil `setIds([])`. Effect `[ids]` otomatis menulis `[]` ke localStorage, sehingga badge, kartu, dan penyimpanan konsisten setelah reload. Tombolnya dinonaktifkan saat daftar kosong.
3. **Halaman Tentang**: route `/about`, link navigasi, dan judul tab dari `usePageTitle("Tentang Aplikasi")`.
4. **Penjelasan hook dan provider**: lihat poin 3 pada bagian penjelasan implementasi.

## Masalah dan perbaikan
`npm.ps1` diblokir PowerShell, diatasi dengan `npm.cmd`; `npm run dev` gagal karena terminal berada di folder luar, diatasi dengan `cd praktikum-spa`.


