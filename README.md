# Kalkulator Faraidh

Sebuah aplikasi web sumber terbuka (open-source) untuk menghitung pembagian harta waris sesuai dengan hukum Islam (Ilmu Faraidh) secara akurat dan transparan.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

![Contoh Tampilan Aplikasi](https://i.ibb.co/L6V2Mh0/image-435e09.png)
*(Gambar di atas adalah contoh inspirasi desain)*

## Daftar Isi

- [Kalkulator Faraidh](#kalkulator-faraidh)
  - [Daftar Isi](#daftar-isi)
  - [Tentang Proyek](#tentang-proyek)
  - [Landasan Syariah](#landasan-syariah)
  - [Fitur Utama](#fitur-utama)
  - [Tumpukan Teknologi](#tumpukan-teknologi)
  - [Instalasi \& Menjalankan Proyek](#instalasi--menjalankan-proyek)
  - [Rencana Pengembangan](#rencana-pengembangan)
  - [Berkontribusi](#berkontribusi)
  - [Lisensi](#lisensi)

## Tentang Proyek

**Kalkulator Faraidh** dibuat untuk memudahkan umat Islam dalam menjalankan salah satu kewajiban penting, yaitu membagi harta warisan sesuai dengan ketentuan syariat. Proses perhitungan waris (mawaris) seringkali rumit dan memerlukan ketelitian tinggi. Aplikasi ini bertujuan untuk menjadi alat bantu yang:

-   **Akurat:** Logika perhitungan didasarkan pada sumber-sumber hukum Islam yang otentik.
-   **Mudah Digunakan:** Antarmuka yang bersih dan intuitif, bahkan bagi pengguna yang awam dengan Ilmu Faraidh.
-   **Transparan:** Hasil perhitungan disajikan secara rinci, lengkap dengan dasar pembagiannya, untuk tujuan edukasi.

## Landasan Syariah

Akurasi adalah fondasi dari aplikasi ini. Semua formula dan logika perhitungan mengacu pada sumber-sumber primer dan kitab-kitab fikih yang diakui:

-   **Al-Qur'an:** Terutama QS. An-Nisa ayat 7, 11, 12, dan 176.
-   **As-Sunnah:** Hadits-hadits Nabi Muhammad ﷺ yang relevan mengenai pembagian waris.
-   **Kitab Fikih:** Mengambil rujukan dari kitab-kitab ulama yang secara spesifik membahas Ilmu Mawaris, seperti *Zahratul Faraidh*, *Al-Mawaris fil Syari'ah al-Islamiyyah*, dan kitab-kitab lainnya.

## Fitur Utama

-   ✅ **Input Tirkah:** Memasukkan total harta peninggalan yang siap dibagikan.
-   ✅ **Pemilihan Ahli Waris:** Antarmuka visual untuk memilih ahli waris yang ada.
-   ✅ **Kalkulasi Otomatis:** Menghitung bagian setiap ahli waris (`Ashabul Furudh`) dan penerima sisa (`Ashabah`).
-   ✅ **Hasil Rinci:** Menampilkan bagian dalam bentuk pecahan dan nominal Rupiah (atau mata uang lain).
-   ✅ **Penanganan Kasus Khusus:** Logika aplikasi dirancang untuk menangani kondisi:
    -   `Hijab` (Ahli waris yang terhalang)
    -   `'Aul` (Total bagian melebihi harta)
    -   `Radd` (Sisa harta dikembalikan ke ahli waris)

## Tumpukan Teknologi

Proyek ini dibangun menggunakan teknologi web modern yang cepat dan efisien.

-   **Frontend:** [**React.js**](https://reactjs.org/) (dengan [**Vite**](https://vitejs.dev/) atau [**Next.js**](https://nextjs.org/))
-   **Styling:** [**Tailwind CSS**](https://tailwindcss.com/)
-   **State Management:** (Opsional) Zustand atau Redux Toolkit
-   **Deployment:** [**Vercel**](https://vercel.com/) / [**Netlify**](https://netlify.com/)

## Instalasi & Menjalankan Proyek

Ikuti langkah-langkah berikut untuk menjalankan proyek ini di lingkungan lokal Anda.

**Prasyarat:**
-   Node.js (v18 atau lebih baru)
-   npm / yarn / pnpm

**Langkah Instalasi:**

1.  **Clone repositori ini:**
    ```bash
    git clone [https://github.com/](https://github.com/)[USERNAME_ANDA]/kalkulator-faraidh.git
    ```

2.  **Masuk ke direktori proyek:**
    ```bash
    cd kalkulator-faraidh
    ```

3.  **Install semua dependensi:**
    ```bash
    npm install
    # atau
    yarn install
    # atau
    pnpm install
    ```

4.  **Jalankan server pengembangan:**
    ```bash
    npm run dev
    # atau
    yarn dev
    # atau
    pnpm dev
    ```

5.  Buka browser Anda dan kunjungi `http://localhost:5173` (atau port lain yang muncul di terminal).

## Rencana Pengembangan

Proyek ini akan terus dikembangkan untuk menambah fitur yang lebih bermanfaat.

-   [ ] **Fase 1 (MVP):** Fitur utama kalkulasi dan penanganan kasus khusus.
-   [ ] **Fase 2:**
    -   Fitur input utang & wasiat.
    -   Opsi cetak/unduh hasil perhitungan (PDF).
    -   Halaman edukasi dan glosarium istilah Faraidh.
-   [ ] **Fase 3:**
    -   Dukungan multi-bahasa dan multi-mata uang.
    -   (Opsional) Pilihan perhitungan berdasarkan mazhab fikih yang berbeda.

## Berkontribusi

Kontribusi dari komunitas sangat kami harapkan! Jika Anda ingin membantu mengembangkan proyek ini, silakan:

1.  **Fork** repositori ini.
2.  Buat **Branch** baru untuk fitur Anda (`git checkout -b fitur/NamaFitur`).
3.  **Commit** perubahan yang Anda buat (`git commit -m 'Menambahkan Fitur X'`).
4.  **Push** ke branch tersebut (`git push origin fitur/NamaFitur`).
5.  Buka sebuah **Pull Request**.

## Lisensi

Proyek ini dilisensikan di bawah **MIT License**. Lihat file `LICENSE` untuk detail lebih lanjut.