# Tasklist Proyek ONESIS ERP / ONESIS Career

Diperbarui: 20 Oktober 2026.

**Tentang tanggal:** tanggal pada task yang sudah selesai adalah tanggal sebenarnya. Tanggal lainnya adalah **usulan target** yang perlu dikonfirmasi. Tanggal yang bergantung pada pihak luar (jawaban legal, penyedia SMTP, Meta) adalah perkiraan dan akan bergeser kalau pihak luar itu terlambat.

**Status:** Selesai · Sedang dikerjakan · Menunggu pengecekan · Siap dikerjakan · Terhambat · Belum dimulai

## 1. Sudah selesai

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 1.1 | Fase 0 sampai 7 API (fondasi, alur rekrutmen inti, kolaborasi, pencarian dan matching, konten, asesmen, tele-interview) | Selesai | 14 Okt 2026 | Sudah di-commit dan dites |
| 1.2 | Pindah database ke PostgreSQL 16 | Selesai | 10 Okt 2026 | |
| 1.3 | Rehearsal pertama import data karir.pkp | Selesai | 13 Okt 2026 | Semua data masuk. 964 avatar dan 3 CV tidak ada di salinan file |
| 1.4 | Fase 8: panel admin, hak akses perusahaan, privasi pencari kerja, admin perusahaan, platform admin | Selesai | 15 Okt 2026 | |
| 1.5 | Fase 8: struktur grup ERP, SSO, approval, rencana tenaga kerja, requisition | Selesai | 16 Okt 2026 | |
| 1.6 | Fase 8: kalender interview, hari kerja rekruter, hiring manager, pewawancara, hiring, pengunduran diri lamaran | Selesai | 17 Okt 2026 | |
| 1.7 | Fase 8: surat penawaran, platform admin lanjutan, talent pool | Selesai | 18 Okt 2026 | |
| 1.8 | Fase 8: pencarian pelamar, hasil interview, lokasi perusahaan, penjadwalan ulang interview (v0.9.1) | Selesai | 19 Okt 2026 | Rilis terakhir: v0.9.1 |
| 1.9 | Desain front end (tiga aplikasi, peta halaman, kontrak link) | Selesai | 19 Okt 2026 | Belum dikoding |
| 1.10 | Aktivasi akun hasil import (#34), salinan isi lowongan di lamaran (#35), riwayat import dikeluarkan dari laporan (#36) | Selesai | 20 Okt 2026 | Di-commit di branch `phase-8-activation`, belum masuk `master` |

## 2. Menyelesaikan batch API saat ini

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 2.1 | Cek dan commit perbaikan tim perusahaan (#37) | Menunggu pengecekan | 21 Okt 2026 | Kode sudah ditulis, menunggu tes dari user |
| 2.2 | Koding kartu antrean admin (#38) | Siap dikerjakan | 21 Okt 2026 | Kecil, satu sesi |
| 2.3 | Pengecekan lengkap (`composer check`) untuk #34–#38 | Siap dikerjakan | 22 Okt 2026 | Dijalankan user |
| 2.4 | Gabungkan ke `master`, buat tag rilis, push ke GitHub | Siap dikerjakan | 23 Okt 2026 | Push dilakukan user |
| 2.5 | Jalankan migrasi baru di database dev dan test | Siap dikerjakan | 23 Okt 2026 | Dijalankan user, setelah 2.4 |

## 3. Rehearsal kedua dan tanggal freeze import

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 3.1 | Review daftar master data yang belum cocok dan setujui pemetaan grup | Siap dikerjakan | 23 Okt 2026 | Keputusan user. File di `/home/jobportal/import/` |
| 3.2 | Lengkapi file avatar dan CV lama yang hilang | Siap dikerjakan | 23 Okt 2026 | Atau terima bahwa file itu memang hilang |
| 3.3 | Ambil salinan baru database dan folder file karir.pkp | Siap dikerjakan | 26 Okt 2026 | Dilakukan user |
| 3.4 | Jalankan rehearsal kedua (mencakup #34, #35, #36) | Terhambat | 28 Okt 2026 | Menunggu 2.5 dan 3.3 |
| 3.5 | Bandingkan hasilnya dengan rehearsal pertama | Terhambat | 29 Okt 2026 | Menunggu 3.4 |
| 3.6 | Tetapkan tanggal freeze import final | Terhambat | 30 Okt 2026 | Keputusan user, setelah 3.5 |

## 4. Sisa API untuk go-live

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 4.1 | Endpoint yang diminta front end: link `.ics` tim dan daftar `my_scoring` | Siap dikerjakan | 27 Okt 2026 | Kecil |
| 4.2 | Pemantauan antrean / queue watch (#30) | Siap dikerjakan | 30 Okt 2026 | Wajib sebelum go-live |
| 4.3 | Posting lowongan, termasuk lowongan dengan beberapa lokasi (#20) | Siap dikerjakan | 13 Nov 2026 | Besar, beberapa sesi |
| 4.4 | Penolakan pelamar (#14) | Terhambat | 4 Des 2026 | Menunggu jawaban legal pertanyaan 13 |
| 4.5 | Pengalaman kandidat dan agenda (#15) | Terhambat | 11 Des 2026 | Menunggu 4.4. Perlu keputusan: tetap masuk go-live atau tidak kalau jawaban legal terlambat |
| 4.6 | Rilis API untuk go-live | Terhambat | 11 Des 2026 | Menunggu 4.1–4.3 (dan 4.5 kalau tetap masuk go-live) |
| 4.7 | Terjemahan pesan error API ke bahasa Indonesia | Terhambat | 15 Jan 2027 | Dikerjakan setelah import final |

## 5. Front end

Koding front end dimulai setelah rehearsal kedua. Build di server dijalankan oleh user.

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 5.1 | Putuskan poin front end yang masih terbuka: IP klien di balik proxy Nuxt, layar kompleks, teks | Siap dikerjakan | 30 Okt 2026 | Keputusan user |
| 5.2 | Siapkan workspace (tiga aplikasi dan paket bersama) | Terhambat | 4 Nov 2026 | Menunggu 3.4 |
| 5.3 | Paket bersama: klien API, tema UI dan komponen, teks `id`/`en` | Terhambat | 18 Nov 2026 | |
| 5.4 | Aplikasi publik: halaman publik, akun, area pencari kerja | Terhambat | 9 Des 2026 | |
| 5.5 | Back office perusahaan: akun, rekrutmen, halaman ERP | Terhambat | 16 Des 2026 | Menunggu 4.1 |
| 5.6 | Aplikasi admin dan area psikolog (termasuk laporan aktivasi) | Terhambat | 16 Des 2026 | |
| 5.7 | Siapkan perintah build dan rilis | Terhambat | 17 Des 2026 | Memori server terbatas, jadi build satu aplikasi per kali |
| 5.8 | Build dan deploy ketiga aplikasi di domain dev, atur service dan nginx | Terhambat | 21 Des 2026 | Dilakukan user |
| 5.9 | Arahkan link di email dan notifikasi ke halaman front end | Terhambat | 22 Des 2026 | Pengaturan oleh user |
| 5.10 | Cabut pembatasan koding API | Terhambat | 22 Des 2026 | Keputusan user setelah halaman go-live jadi |

## 6. Keputusan dan input dari pihak luar

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 6.1 | Kirim review legal (pertanyaan 1–22) ke konsultan hukum | Siap dikerjakan | 23 Okt 2026 | Belum dikirim |
| 6.2 | Terima jawaban legal untuk go-live (pertanyaan 1–7, 10, 13 dan pertanyaan j aktivasi) | Terhambat | 20 Nov 2026 | Perkiraan, tergantung konsultan |
| 6.3 | Kirim laporan keamanan sistem lama (S1–S3, kritis) ke pengelola karir.pkp | Siap dikerjakan | 23 Okt 2026 | Prioritas tinggi |
| 6.4 | Pilih penyedia SMTP | Siap dikerjakan | 30 Okt 2026 | Keputusan user |
| 6.5 | Koding parser webhook bounce dan komplain dari penyedia SMTP | Terhambat | 6 Nov 2026 | Menunggu 6.4 |
| 6.6 | Tentukan kontak support dan kontak situs publik | Siap dikerjakan | 30 Okt 2026 | Keputusan user |
| 6.7 | Tentukan domain produksi dan origin front end | Siap dikerjakan | 13 Nov 2026 | Keputusan user |
| 6.8 | Siapkan SIP trunk dengan satu nomor DID untuk interview telepon | Siap dikerjakan | 27 Nov 2026 | Dengan penyedia telekomunikasi |
| 6.9 | Verifikasi Meta Business dan persetujuan template WhatsApp | Sedang dikerjakan | 31 Jan 2027 | Untuk fitur setelah go-live |

## 7. Server produksi dan go-live

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 7.1 | Siapkan server dan PHP | Belum dimulai | 27 Nov 2026 | Lihat `deployment-checklist.md` bagian 1 |
| 7.2 | Ganti nama infrastruktur ke `onesis`, pisahkan situs live dari folder kerja | Belum dimulai | 4 Des 2026 | |
| 7.3 | Isi `.env` produksi | Terhambat | 11 Des 2026 | Menunggu 6.4 dan 6.7 |
| 7.4 | Instal Janus dan coturn (video interview) | Belum dimulai | 11 Des 2026 | |
| 7.5 | Instal Asterisk (interview telepon) | Terhambat | 18 Des 2026 | Menunggu 6.8 |
| 7.6 | Siapkan database produksi: backup, migrasi, seed, hak akses | Terhambat | 18 Des 2026 | Menunggu 4.6 |
| 7.7 | Buat admin pertama dan minimal dua super admin | Terhambat | 18 Des 2026 | |
| 7.8 | Buat jenis dokumen verifikasi wajib dan cek pengaturan moderasi lowongan | Terhambat | 18 Des 2026 | |
| 7.9 | Jalankan scheduler, queue worker, cache; pasang monitor uptime | Terhambat | 21 Des 2026 | |
| 7.10 | Atur nginx produksi | Belum dimulai | 21 Des 2026 | |
| 7.11 | Smoke test setelah switch | Terhambat | 6 Jan 2027 | Menunggu 5.8 dan 7.1–7.10 |
| 7.12 | Rapat go/no-go dan go-live | Terhambat | 11 Jan 2027 | Usulan tanggal go-live. Butuh jawaban legal (6.2) |

## 8. Import final dan aktivasi akun

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 8.1 | Ambil salinan database dan file karir.pkp pada tanggal freeze | Terhambat | 4 Jan 2027 | Menunggu 3.6 dan 7.6 |
| 8.2 | Dry run import final, bandingkan dengan rehearsal | Terhambat | 5 Jan 2027 | |
| 8.3 | Jalankan import final | Terhambat | 6 Jan 2027 | Dijalankan user |
| 8.4 | Jalankan perintah setelah import (rollup analitik, facet pencarian pelamar) | Terhambat | 6 Jan 2027 | |
| 8.5 | Isi pengaturan dan link aktivasi | Terhambat | 6 Jan 2027 | Menunggu 5.8 dan 6.6 |
| 8.6 | Tes email aktivasi ke staf | Terhambat | 8 Jan 2027 | Menunggu 6.5 |
| 8.7 | Kirim email aktivasi ke pemilik perusahaan | Terhambat | 12 Jan 2027 | Setelah go-live |
| 8.8 | Kirim pilot 200 email ke pencari kerja | Terhambat | 14 Jan 2027 | Bounce harus di bawah 2% dan komplain di bawah 0,1% |
| 8.9 | Kirim email aktivasi ke semua pencari kerja (500 per hari), lalu pengingat setelah 7 hari | Terhambat | Belum bisa ditentukan | Lamanya tergantung jumlah akun pencari kerja |
| 8.10 | Pantau laporan aktivasi dan tangani kasus support | Terhambat | Rutin mingguan | |

## 9. Setelah go-live (backlog)

Urutan dan tanggalnya ditetapkan setelah go-live.

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 9.1 | Item akun dan halaman publik (#39) | Siap dikerjakan | Belum dijadwalkan | Dikerjakan kalau front end memintanya |
| 9.2 | Penutupan rekrutmen untuk akun terhapus atau perusahaan yang ditangguhkan (#40) | Siap dikerjakan | Belum dijadwalkan | Sama seperti 9.1 |
| 9.3 | Eskalasi approval (#41) | Siap dikerjakan | Belum dijadwalkan | Sama seperti 9.1 |
| 9.4 | Pencegahan edit bersamaan (#42) | Siap dikerjakan | Belum dijadwalkan | Front end harus siap lebih dulu |
| 9.5 | Penggabungan data referensi (#43) | Siap dikerjakan | Belum dijadwalkan | |
| 9.6 | Akses support ke data perusahaan (#44) | Siap dikerjakan | Belum dijadwalkan | |
| 9.7 | Bukti penerimaan penawaran dan approval penawaran tenant internal (#12) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 12 |
| 9.8 | Desain serah terima ke modul Employee dan dokumen pra-onboarding (#13) | Belum dimulai | Belum dijadwalkan | Perlu sesi desain |
| 9.9 | Otomasi asesmen (#22) | Siap dikerjakan | Belum dijadwalkan | |
| 9.10 | Tools dan institusi mitra psikolog (#23, #24) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 11, 17, 19 |
| 9.11 | Privasi pencari kerja bagian 3 dan 4 (#25) | Siap dikerjakan | Belum dijadwalkan | |
| 9.12 | Retensi data admin perusahaan (#26) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 15 |
| 9.13 | Kandidat manual (#31) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 20 |
| 9.14 | Pemeriksaan pra-kerja (#32) | Terhambat | Belum dijadwalkan | Menunggu jawaban legal pertanyaan 21 dan #15 |
| 9.15 | Notifikasi WhatsApp (#33) | Terhambat | Belum dijadwalkan | Menunggu 6.9, jawaban legal pertanyaan 22, dan #32 |
| 9.16 | Manajemen user | Terhambat | Belum dijadwalkan | Setelah import final |
| 9.17 | Fitur marketplace yang dibekukan (direktori perusahaan, registrasi perusahaan, detail kursus, discovery lowongan bagian 6–7) | Terhambat | Belum dijadwalkan | Setelah inti ERP TA |
| 9.18 | Tentukan cakupan gap benchmark G1–G21 | Siap dikerjakan | Belum dijadwalkan | Keputusan user. G20 sebaiknya sebelum go-live |

## 10. Utang teknis dan audit

| No | Task | Status | Waktu selesai | Keterangan |
|---|---|---|---|---|
| 10.1 | Perbaiki tes yang kadang gagal (`TestAuthoringTest`) | Siap dikerjakan | 30 Okt 2026 | Lolos kalau diulang |
| 10.2 | Audit performa: pengukuran dengan data nyata | Terhambat | 22 Jan 2027 | Setelah import final |
| 10.3 | Audit performa: tes jumlah query | Terhambat | Belum dijadwalkan | Setelah 10.2 |
| 10.4 | Temuan review arsitektur 7, 8, 20 | Siap dikerjakan | Belum dijadwalkan | |
| 10.5 | Temuan review arsitektur 9, 21 (refactor) | Terhambat | Belum dijadwalkan | Setelah import final |
| 10.6 | Hapus catatan talent pool langsung, bukan menunggu proses malam | Belum dimulai | Belum dijadwalkan | Perlu desain kecil |
