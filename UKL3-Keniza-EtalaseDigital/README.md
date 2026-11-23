# Etalase Digital Stella (gabungan)

Gabungan dua proyek:

| Bagian | Sumber |
|---|---|
| Tampilan (Bootstrap 5, navbar, kartu galeri, form, halaman kelola siswa) | `etalase_stella` |
| Backend (PDO + prepared statement, middleware role, tabel `users`/`projects`, login username) | `etalase_stella_lkpd` |
| Popup (detail project & form tambah admin) | `etalase_stella_lkpd` |
| **Baru:** halaman *Kelola User* khusus Super Admin untuk menambah admin | – |

## Instalasi (XAMPP)
1. Salin folder `etalase_stella` ke `htdocs/`.
2. Buka phpMyAdmin → **Import** → pilih `database.sql`.
3. Cek `config/kuis_db.php` (host/user/password MySQL).
4. Buka `http://localhost/etalase_stella/auth/login.php`.

## Akun default (password: `password123`)
| Username | Role |
|---|---|
| superadmin | super_admin |
| admin | admin |
| siswa | siswa |

Segera ganti password akun default setelah instalasi.

## Hak akses
| Fitur | Siswa | Admin | Super Admin |
|---|:-:|:-:|:-:|
| Lihat galeri, tambah project | ✔ | ✔ | ✔ |
| Edit/hapus project | milik sendiri | semua | semua |
| Kelola Siswa (`admin/siswa.php`, tabel `students`) | – | ✔ | ✔ |
| Kelola User + tambah admin (`admin/users.php`) | – | – | ✔ |

## Struktur akun: `users` (staff) dan `students` (siswa)
| Tabel | Isi |
|---|---|
| `users` | hanya **admin** dan **super admin** |
| `students` | semua **siswa**: nama, **NIS** (unik, 4-20 digit angka), **jurusan** (RPL / TKJ / TJAT / Animasi), username, password |
| `projects` | dimiliki satu akun: `student_id` (project siswa) **atau** `user_id` (project admin) |

- Login memakai username dan mencari di `users` lalu `students`; karena itu username harus unik di **kedua** tabel (dicek otomatis oleh `username_taken()`). Format username: 3-30 karakter, huruf/angka/titik/underscore/strip.
- Project yang diunggah siswa masuk ke akun siswa itu (`projects.student_id`) dan hanya bisa diedit/dihapus olehnya (atau admin).
- Kelola Siswa memakai tabel `students`; Kelola User menampilkan seluruh akun (staff + siswa, lengkap dengan NIS & jurusan).
- Daftar jurusan diatur di satu tempat: `JURUSAN_LIST` di `includes/helpers.php` (jika ditambah, ubah juga ENUM `students.jurusan`).
- **Database lama maupun instalasi baru:** cukup import `database.sql` (otomatis memindahkan siswa ke `students` dan mengganti kolom `email` menjadi `username`, tanpa kehilangan data).

## Catatan perubahan
- Login memakai **username** (sebelumnya email).
- Project memakai satu kolom `link_project` (skema lkpd); kolom `link_pendukung`/`link_demo` dari versi lama tidak dipakai.
- `reset.php` dihapus (bisa dipakai siapa saja tanpa login untuk mereset password).
- Hapus/reset sekarang lewat POST + token CSRF; upload gambar divalidasi (JPG/PNG/WEBP, maks. 2MB) baik saat tambah maupun edit.
- Reset password siswa oleh admin menjadi `654321` (ubah di `DEFAULT_RESET_PASSWORD`, `middleware/auth_check.php`).
