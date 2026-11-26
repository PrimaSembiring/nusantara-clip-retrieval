# Log keputusan

Setiap keputusan dicatat bersama alasannya. Entri baru ditambahkan per milestone. Angka hasil tidak ditulis di sini; angka ada di `mx_*/results/` dan README milestone.

---

## Repo (dibuat 26 Nov 2026)

| Keputusan | Alasan |
|---|---|
| Repo **private** sampai penilaian mata kuliah selesai | Kode tidak boleh terlihat kelompok lain selama perkuliahan berjalan (integritas akademik). Isinya juga masih perlu dipoles sebelum publik. |
| **"Kode sama"**: dari kode asli, yang boleh berubah hanya path, perangkat, folder keluaran, komentar/penjelasan, dan pemecahan sel. Logika, model, seed, dan hiperparameter tetap | Repo ini untuk belajar dan portofolio, bukan eksperimen baru. Setiap notebook diakhiri sel yang membandingkan hasilnya dengan angka resmi yang dicatat di sel itu. Bila berbeda, perbedaannya dicatat, tidak diakali. |
| Path **relatif terhadap repo** (`data/`, `cache/`). Dataset dihubungkan lewat **symlink** `data` yang diabaikan git | Kode asli memakai path absolut laptop pribadi. Path relatif membuat notebook bisa jalan di komputer lain tanpa mengedit kode, dan path pribadi tidak masuk repo. Dipilih daripada berkas konfigurasi terpisah agar tetap sederhana. |
| Perangkat dipilih otomatis (`cuda` bila ada, selain itu `cpu`); di laptop sendiri dijalankan dengan GPU | Supaya belajar dan pelatihan lebih cepat. Hasil resmi dihitung di CPU, jadi angka GPU bisa berbeda di digit akhir. Sel verifikasi melaporkan selisihnya, bukan menghentikan notebook. Angka di README tetap angka resmi (CPU). |
| Tidak di-commit: dataset (gambar/CSV), cache `.npy`, bobot `.pt`, data/prediksi test, dokumen dosen dan laporan kelompok, kredensial | Dataset berlisensi CC BY-SA 4.0 dan tidak perlu didistribusikan ulang. Cache dan bobot bisa dibuat ulang. Test set privat tidak boleh tersebar. Dokumen kelompok memuat identitas anggota. |
| Hasil yang memuat caption/gambar dataset (per query, contoh query, gambar contoh) **default tidak di-commit** | Secara lisensi boleh asal diberi atribusi, tetapi keputusan ditunda sampai tahap publik. Tabel agregat (metrik, gap, biaya) tetap di-commit. |
| README utama berbahasa Inggris + ringkasan Indonesia; README milestone dan catatan belajar berbahasa Indonesia | Bahasa Inggris untuk pembaca portofolio; catatan belajar dalam bahasa yang paling mudah dipahami pemiliknya. |
| README menyebut asal proyek (tugas kelompok mata kuliah), peran resmi, dan bantuan asisten AI | Jujur tentang kontribusi. |

---

## M1: Baseline

| Keputusan | Alasan |
|---|---|
| Encoder pra-latih **dibekukan** dan hanya dipakai untuk inferensi (`encode`). M1 tidak melatih apa pun | Aturan tugas. Hasilnya bisa dihitung di CPU dalam hitungan menit dan menjadi pembanding yang adil untuk adapter di M2. |
| Gambar dan teks EN: `sentence-transformers/clip-ViT-B-32`. Teks ID: `sentence-transformers/clip-ViT-B-32-multilingual-v1` | Pasangan model baseline yang ditetapkan tugas. Encoder multibahasa adalah hasil distilasi ke ruang embedding CLIP (512 dimensi), sehingga bisa langsung dibandingkan dengan embedding gambar. |
| CPU, seed 0, batch gambar 32, batch teks 64 | Hasil resmi harus reproduksibel di CPU. Seed dikunci supaya pemilihan contoh acak bisa diulang persis. |
| Embedding dinormalisasi (norma 1); skor = perkalian titik | Dengan norma 1, kosinus sama dengan perkalian titik, sehingga semua skor cukup dihitung dengan satu perkalian matriks. |
| Cache embedding `.npy` dengan nama berisi md5 urutan `image_id` | Encoder cukup dijalankan sekali. Kalau isi atau urutan CSV berubah, nama berkas ikut berubah, sehingga cache lama tidak pernah terpakai untuk data versi lain. |
| Mode **ketat** (hanya pasangan asli yang benar) = angka utama. Mode **longgar** (caption identik juga benar) = pelengkap | Mode ketat adalah definisi standar. Mode longgar menunjukkan pengaruh caption kembar di val. |
| Skor seri diputus **pesimis** (kandidat salah ditaruh di atas kandidat benar) | Angka menjadi batas bawah yang jujur, tidak terbantu oleh kebetulan urutan baris. |
| mAP@10 dilaporkan. Dengan satu jawaban benar per query, nilainya sama dengan MRR@10 | Metrik yang diminta tugas; kesetaraannya dicatat supaya tidak disalahartikan. |
| Language gap EN−ID dilaporkan dengan **CI95 bootstrap berpasangan** (2000×, seed 0, atas query) | Untuk mengetahui apakah selisihnya konsisten atau hanya kebetulan sampel. |
| 10 contoh query diambil **acak dari train** (seed 0, galeri = semua gambar train) | Contoh tidak boleh dipilih dari val (aturan kebocoran). Karena galerinya berbeda (2072 vs 275), peringkat contoh tidak sebanding dengan tabel val. |
| Contoh gagal dipilih dengan aturan tertulis: 2 kegagalan terdekat ke peringkat 1 ditambah 1 kegagalan terparah | Supaya contoh tidak dipilih-pilih secara manual. |
| Diagnostik: caption EN juga di-encode dengan encoder multibahasa | Memisahkan efek **encoder** dari efek **bahasa** dalam language gap. Ini menjadi dasar memilih pendekatan M2. |
