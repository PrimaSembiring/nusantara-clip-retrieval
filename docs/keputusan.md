# Log keputusan

Catatan keputusan penting beserta alasannya.

## Repo

| Keputusan | Alasan |
|---|---|
| Kode bersama dipisah ke `pipeline/`; notebook hanya memanggil dan menampilkan | Satu sumber kode untuk semua tahap, jadi perbaikan cukup dilakukan di satu tempat |
| Path relatif terhadap repo (`data/`, `cache/`); dataset dihubungkan dengan symlink `data` | Bisa dijalankan di komputer lain tanpa mengedit kode, dan tidak ada path pribadi di repo |
| Perangkat dipilih otomatis (GPU bila ada, selain itu CPU) | Lebih cepat di laptop sendiri, tetap bisa jalan tanpa GPU |
| Dataset, cache `.npy`, bobot model, dan kredensial tidak di-commit | Dataset bukan milik saya; cache dan bobot bisa dibuat ulang |
| Hasil yang memuat caption atau gambar dataset tidak di-commit; hanya tabel ringkas | Menjaga repo tetap ringan dan tidak menyebarkan ulang isi dataset |

## M1: Baseline

| Keputusan | Alasan |
|---|---|
| Encoder dibekukan; M1 tidak melatih apa pun | M1 mengukur kemampuan model apa adanya sebagai titik awal pembanding |
| Gambar dan teks EN: CLIP ViT-B/32. Teks ID: clip-ViT-B-32-multilingual-v1 | Encoder multibahasa sudah disejajarkan ke ruang CLIP (512 dimensi), jadi teks ID bisa langsung dibandingkan dengan gambar |
| Embedding dinormalisasi; skor = perkalian titik | Dengan norma 1, kosinus sama dengan perkalian titik, sehingga semua skor cukup dihitung dengan satu perkalian matriks |
| Cache embedding `.npy`, nama berkas memuat md5 urutan `image_id` | Encoder cukup dijalankan sekali; cache otomatis tidak terpakai bila data berubah |
| Mode ketat (hanya pasangan asli yang benar) sebagai angka utama; mode longgar (caption identik juga benar) sebagai pelengkap | Mode ketat adalah definisi standar; mode longgar menunjukkan pengaruh caption kembar |
| Skor seri diputus pesimis (kandidat salah ditaruh di atas yang benar) | Angka tidak terbantu oleh kebetulan urutan baris |
| mAP@10; dengan satu jawaban benar per query nilainya sama dengan MRR@10 | Dicatat supaya tidak disalahartikan |
| Language gap EN−ID dengan CI95 bootstrap (2000×, seed 0) | Untuk tahu apakah selisihnya konsisten atau hanya kebetulan sampel |
| 10 contoh query diambil acak dari train (seed 0) | Validation tidak dipakai memilih apa pun; contoh tidak dipilih-pilih |
| Contoh gagal dipilih dengan aturan tetap: 2 kegagalan terdekat ke peringkat 1, ditambah 1 terparah | Supaya contoh tidak dipilih secara manual |
| Diagnostik: caption EN juga di-encode dengan encoder multibahasa | Memisahkan efek encoder dari efek bahasa dalam language gap |
