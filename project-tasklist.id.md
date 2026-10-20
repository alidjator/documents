# Tasklist Proyek ONESIS ERP / ONESIS Career

Diperbarui: 20 Oktober 2026.

**Tentang tanggal:** tanggal pada task yang sudah selesai adalah tanggal sebenarnya. Tanggal lainnya adalah **usulan target** yang perlu dikonfirmasi. Tanggal yang bergantung pada pihak luar (jawaban legal, penyedia SMTP, penyedia telepon) akan bergeser kalau pihak luar itu terlambat.

**Status:** Selesai · Sedang dikerjakan · Menunggu pengecekan · Siap dikerjakan · Terhambat · Belum dimulai

## 1. Backend

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 1.1 | Fondasi backend: identitas, pengaturan, notifikasi, penyimpanan file, audit log, master data | Selesai | 14 Okt 2026 | Fase 0 |
| 1.2 | Pindah database ke PostgreSQL 16 | Selesai | 10 Okt 2026 | |
| 1.3 | Skrip import data karir.pkp dan rehearsal pertama | Selesai | 13 Okt 2026 | 964 avatar dan 3 CV tidak ada di salinan file |
| 1.4 | Perubahan import untuk aktivasi akun (#34), salinan isi lowongan (#35), riwayat import (#36) | Selesai | 20 Okt 2026 | Di branch `phase-8-activation`, belum masuk `master` |
| 1.5 | Pengecekan lengkap (`composer check`) untuk #34–#38 | Siap dikerjakan | 22 Okt 2026 | Dijalankan user |
| 1.6 | Gabungkan ke `master`, buat tag rilis, push ke GitHub | Siap dikerjakan | 23 Okt 2026 | Push dilakukan user |
| 1.7 | Jalankan migrasi baru di database dev dan test | Siap dikerjakan | 23 Okt 2026 | Dijalankan user |
| 1.8 | Lengkapi file avatar dan CV lama yang hilang | Siap dikerjakan | 23 Okt 2026 | |
| 1.9 | Rehearsal kedua import (mencakup #34, #35, #36) | Terhambat | 28 Okt 2026 | Menunggu 1.7 dan salinan baru data karir.pkp |
| 1.10 | Bandingkan hasil rehearsal kedua dengan yang pertama | Terhambat | 29 Okt 2026 | |
| 1.11 | Perbaiki tes yang kadang gagal (`TestAuthoringTest`) | Siap dikerjakan | 30 Okt 2026 | Lolos kalau diulang |
| 1.12 | Parser webhook bounce dan komplain dari penyedia SMTP | Terhambat | 6 Nov 2026 | Menunggu pilihan penyedia SMTP |
| 1.13 | Siapkan server produksi dan PHP | Belum dimulai | 27 Nov 2026 | `deployment-checklist.md` bagian 1 |
| 1.14 | Ganti nama infrastruktur ke `onesis`, pisahkan situs live dari folder kerja | Belum dimulai | 4 Des 2026 | |
| 1.15 | Isi `.env` produksi | Terhambat | 11 Des 2026 | Menunggu SMTP dan domain produksi |
| 1.16 | Instal Janus dan coturn (video interview) | Belum dimulai | 11 Des 2026 | |
| 1.17 | Instal Asterisk (interview telepon) | Terhambat | 18 Des 2026 | Menunggu SIP trunk |
| 1.18 | Siapkan database produksi: backup, migrasi, seed, hak akses, admin pertama | Terhambat | 18 Des 2026 | Menunggu rilis API go-live (3.12) |
| 1.19 | Jalankan scheduler, queue worker, cache; pasang monitor uptime | Terhambat | 21 Des 2026 | |
| 1.20 | Atur nginx produksi | Belum dimulai | 21 Des 2026 | |
| 1.21 | Import final dari salinan data pada tanggal freeze | Terhambat | 6 Jan 2027 | Tanggal freeze belum ditetapkan |
| 1.22 | Jalankan perintah setelah import (rollup analitik, facet pencarian pelamar) | Terhambat | 6 Jan 2027 | |
| 1.23 | Smoke test produksi | Terhambat | 6 Jan 2027 | Menunggu 1.13–1.20 dan deploy front end |
| 1.24 | Kirim email aktivasi: staf, pemilik perusahaan, pilot 200 pencari kerja, lalu 500 per hari | Terhambat | Mulai 8 Jan 2027 | Selesainya tergantung jumlah akun pencari kerja |
| 1.25 | Audit performa: pengukuran dengan data nyata | Terhambat | 22 Jan 2027 | Setelah import final |
| 1.26 | Audit performa: tes jumlah query | Terhambat | Belum dijadwalkan | Setelah 1.25 |
| 1.27 | Temuan review arsitektur 7, 8, 20 | Siap dikerjakan | Belum dijadwalkan | |
| 1.28 | Temuan review arsitektur 9, 21 (refactor) | Terhambat | Belum dijadwalkan | Setelah import final |
| 1.29 | Hapus catatan talent pool langsung, bukan menunggu proses malam | Belum dimulai | Belum dijadwalkan | Perlu desain kecil |

## 2. Frontend

Koding front end dimulai setelah rehearsal kedua. Build di server dijalankan oleh user.

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 2.1 | Desain front end: tiga aplikasi, peta halaman, kontrak link | Selesai | 19 Okt 2026 | |
| 2.2 | Putuskan poin yang masih terbuka: IP klien di balik proxy Nuxt, layar kompleks, teks | Siap dikerjakan | 30 Okt 2026 | Keputusan user |
| 2.3 | Siapkan workspace (tiga aplikasi dan paket bersama) | Terhambat | 4 Nov 2026 | Menunggu rehearsal kedua (1.9) |
| 2.4 | Paket bersama: klien API, tema UI dan komponen, teks `id`/`en` | Terhambat | 18 Nov 2026 | |
| 2.5 | Aplikasi publik: halaman publik, akun, area pencari kerja | Terhambat | 9 Des 2026 | |
| 2.6 | Back office perusahaan: akun, rekrutmen, halaman ERP | Terhambat | 16 Des 2026 | Menunggu endpoint 3.7 |
| 2.7 | Aplikasi admin dan area psikolog (termasuk laporan aktivasi) | Terhambat | 16 Des 2026 | |
| 2.8 | Siapkan perintah build dan rilis | Terhambat | 17 Des 2026 | Memori server terbatas, jadi build satu aplikasi per kali |
| 2.9 | Build dan deploy ketiga aplikasi di domain dev, atur service dan nginx | Terhambat | 21 Des 2026 | Dilakukan user |
| 2.10 | Arahkan link di email dan notifikasi ke halaman front end | Terhambat | 22 Des 2026 | |
| 2.11 | Deploy front end ke produksi | Terhambat | 6 Jan 2027 | Menunggu domain produksi |

## 3. API

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 3.1 | Fase 1–7: alur rekrutmen inti, kolaborasi, pencarian dan matching, konten, asesmen, tele-interview | Selesai | 14 Okt 2026 | |
| 3.2 | Fase 8 sampai #29: panel admin, hak akses, ERP (grup, SSO, approval, requisition), interview, penawaran, hiring, talent pool, pencarian pelamar, lokasi | Selesai | 19 Okt 2026 | Rilis terakhir v0.9.1 |
| 3.3 | Aktivasi akun hasil import (#34) | Selesai | 20 Okt 2026 | Belum masuk `master` |
| 3.4 | Salinan isi lowongan di lamaran (#35) dan riwayat import dikeluarkan dari laporan (#36) | Selesai | 20 Okt 2026 | Belum masuk `master` |
| 3.5 | Perbaikan tim perusahaan (#37) | Menunggu pengecekan | 21 Okt 2026 | Kode sudah ditulis |
| 3.6 | Kartu antrean admin (#38) | Siap dikerjakan | 21 Okt 2026 | |
| 3.7 | Endpoint yang diminta front end: link `.ics` tim dan daftar `my_scoring` | Siap dikerjakan | 27 Okt 2026 | |
| 3.8 | Pemantauan antrean / queue watch (#30) | Siap dikerjakan | 30 Okt 2026 | Wajib sebelum go-live |
| 3.9 | Posting lowongan, termasuk lowongan dengan beberapa lokasi (#20) | Siap dikerjakan | 13 Nov 2026 | Besar, beberapa sesi |
| 3.10 | Penolakan pelamar (#14) | Terhambat | 4 Des 2026 | Menunggu jawaban legal pertanyaan 13 |
| 3.11 | Pengalaman kandidat dan agenda (#15) | Terhambat | 11 Des 2026 | Menunggu 3.10. Perlu keputusan: tetap masuk go-live atau tidak |
| 3.12 | Rilis API untuk go-live | Terhambat | 11 Des 2026 | Menunggu 3.5–3.9 |
| 3.13 | Terjemahan pesan error API ke bahasa Indonesia | Terhambat | 15 Jan 2027 | Setelah import final |
| 3.14 | Item akun dan halaman publik (#39) | Siap dikerjakan | Belum dijadwalkan | Setelah go-live, kalau front end memintanya |
| 3.15 | Penutupan rekrutmen untuk akun terhapus atau perusahaan yang ditangguhkan (#40) | Siap dikerjakan | Belum dijadwalkan | Sama seperti 3.14 |
| 3.16 | Eskalasi approval (#41) | Siap dikerjakan | Belum dijadwalkan | Sama seperti 3.14 |
| 3.17 | Pencegahan edit bersamaan (#42) | Siap dikerjakan | Belum dijadwalkan | Front end harus siap lebih dulu |
| 3.18 | Penggabungan data referensi (#43) | Siap dikerjakan | Belum dijadwalkan | |
| 3.19 | Akses support ke data perusahaan (#44) | Siap dikerjakan | Belum dijadwalkan | |
| 3.20 | Bukti penerimaan penawaran dan approval penawaran tenant internal (#12) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 12 |
| 3.21 | Serah terima ke modul Employee dan dokumen pra-onboarding (#13) | Belum dimulai | Belum dijadwalkan | Perlu sesi desain |
| 3.22 | Otomasi asesmen (#22) | Siap dikerjakan | Belum dijadwalkan | |
| 3.23 | Tools dan institusi mitra psikolog (#23, #24) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 11, 17, 19 |
| 3.24 | Privasi pencari kerja bagian 3 dan 4 (#25) | Siap dikerjakan | Belum dijadwalkan | |
| 3.25 | Retensi data admin perusahaan (#26) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 15 |
| 3.26 | Kandidat manual (#31) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 20 |
| 3.27 | Pemeriksaan pra-kerja (#32) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 21 dan #15 |
| 3.28 | Notifikasi WhatsApp (#33) | Terhambat | Belum dijadwalkan | Menunggu verifikasi Meta, jawaban legal pertanyaan 22, dan #32 |
| 3.29 | Manajemen user | Terhambat | Belum dijadwalkan | Setelah import final |
| 3.30 | Fitur marketplace yang dibekukan (direktori perusahaan, registrasi perusahaan, detail kursus, discovery lowongan bagian 6–7) | Terhambat | Belum dijadwalkan | Setelah inti ERP TA |
