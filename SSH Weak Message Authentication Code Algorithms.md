# Mengatasi SSH Weak Message Authentication Code Algorithms di Ubuntu

Tutorial ini menjelaskan langkah-langkah untuk mengatasi masalah "SSH Weak Message Authentication Code Algorithms" pada Ubuntu.

## Langkah-langkah

### 1. Buka Terminal
Buka terminal di Ubuntu Anda.

### 2. Edit File Konfigurasi SSH
Gunakan editor teks untuk membuka file konfigurasi SSH. Anda bisa menggunakan `nano` atau editor lain. Jalankan perintah berikut:

    sudo nano /etc/ssh/sshd_config

### 3. Tambahkan atau Edit Baris MACs
Cari baris yang berhubungan dengan `MACs`. Jika tidak ada, tambahkan baris berikut untuk menonaktifkan algoritma yang lemah:

    MACs hmac-sha2-256,hmac-sha2-512

### 4. Simpan dan Keluar
Jika menggunakan `nano`, tekan `CTRL + O` untuk menyimpan, lalu `CTRL + X` untuk keluar dari editor.

### 5. Restart Layanan SSH
Setelah melakukan perubahan, restart layanan SSH agar perubahan diterapkan:

    sudo systemctl restart ssh

### 6. Verifikasi Konfigurasi
Untuk memverifikasi bahwa konfigurasi telah diterapkan dengan benar, gunakan perintah berikut:

    ssh -Q mac

Perintah ini akan menampilkan daftar algoritma MAC yang didukung oleh klien SSH Anda.

## Kesimpulan
Dengan mengikuti langkah-langkah di atas, Anda dapat mengatasi masalah SSH Weak Message Authentication Code Algorithms pada Ubuntu. Pastikan untuk selalu memeriksa dan memperbarui konfigurasi keamanan sesuai dengan praktik terbaik.
