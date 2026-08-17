# Client Portal (Client Self Service) — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Client Portal (Client Self Service/CSS) adalah kanal akses mandiri bagi client — padanan Employee Self Service tapi untuk entitas bisnis, bukan individu, sehingga kompleksitas akses, kontrak, dan kepatuhan datanya jauh lebih berlapis. Domain ini mencakup: struktur akses berbasis peran dan segregasi data multi-client, monitoring & approval data kehadiran secara real-time, kanal permintaan penggantian tenaga dan komplain, akses read-only ke dokumen kontrak/billing/laporan visit, kepatuhan data pribadi, notifikasi proaktif, pelaporan mandiri, audit trail & keamanan akses, onboarding akses client baru, desain inklusif untuk kesenjangan kanal komunikasi dan konektivitas daerah, tiering client & peran Account Manager, dukungan multi-bahasa, eskalasi ke jalur formal, verifikasi kewenangan PIC, kanal pengajuan kebutuhan tenaga baru, proses buy-out/direct-hire, penanganan penyalahgunaan akses, dan unified approval queue.

---

## 2. Prinsip Dasar: Tujuan, Struktur Akses & Segregasi Data

### 2.1 Tujuan & Filosofi

Portal ini mengurangi beban komunikasi manual (telepon/email/WhatsApp ke Account Manager) untuk hal-hal rutin, sekaligus meningkatkan transparansi ke client sebagai nilai jual kompetitif.

- **Pre-Cycle**: kondisi sebelum portal tersedia — komunikasi rutin client-ke-Account Manager sepenuhnya manual (telepon/email/WhatsApp), menimbulkan beban operasional berulang dan risiko informasi tidak konsisten antar-kanal.
- **Life Cycle**: client menggunakan portal sebagai kanal utama untuk kebutuhan rutin (lihat status, unduh dokumen, ajukan permintaan), Account Manager hanya dilibatkan untuk hal yang benar-benar memerlukan interaksi manusia.
- **Post-Cycle**: penurunan volume komunikasi manual dan tingkat pemakaian portal menjadi salah satu indikator adopsi client, diteruskan sebagai input skor kesehatan akun (bagian 10.3).

### 2.2 Struktur Akses & Role-Based Permission

Client dapat memiliki lebih dari satu user dengan level akses berbeda per peran: PIC operasional harian (akses detail transaksional) vs Manajemen/Direksi client (akses ringkasan saja); PIC site hanya melihat data site-nya, HR client dapat melihat data gabungan seluruh site di perusahaannya. Akses dipetakan langsung ke PKS yang relevan — client hanya dapat melihat data tenaga yang tercakup dalam PKS mereka, krusial untuk client dengan multi-PKS berlingkup berbeda (domain PKS, bagian Multi-PKS dalam Satu Client).

- **Pre-Cycle**: pemetaan role-ke-cakupan akses (site/gabungan site/PKS) ditetapkan saat onboarding akses client baru (bagian 12) atau saat terjadi perubahan struktur organisasi/PKS di sisi client.
- **Life Cycle**: setiap request data/aksi di portal disaring sesuai kombinasi role dan cakupan PKS/site user yang bersangkutan; perubahan role (mis. PIC site dipromosikan menjadi HR client dengan akses gabungan) memerlukan pembaruan mapping akses.
- **Post-Cycle**: perubahan struktur akses tercatat dalam audit trail (bagian 9) untuk keperluan penelusuran jika terjadi dispute mengenai siapa yang seharusnya dapat melihat data tertentu.

### 2.3 Data Segregation Multi-Client & Multi-Legal-Entity

**Business rule & validasi**: jaminan client A tidak dapat melihat data client B sama sekali — diterapkan sebagai *multi-tenancy logic* di level aplikasi meski platform bersifat *single-tenant* per instalasi; skenario satu grup usaha client dengan beberapa anak perusahaan berpekerjaan-sama masing-masing memiliki PKS terpisah tetap disegregasi per PKS/anak perusahaan, bukan otomatis digabung hanya karena berada dalam satu grup usaha yang sama.

- **Pre-Cycle**: konfigurasi mapping client/anak perusahaan/PKS/legal entity ditetapkan sejak provisioning akses awal (bagian 12), menjadi acuan baku bagi seluruh kontrol segregasi berikutnya.
- **Life Cycle**: enforcement segregasi berjalan pada setiap transaksi baca/tulis/ekspor data — query, API, maupun laporan — sesuai mapping yang berlaku saat itu.
- **Post-Cycle**: jika ditemukan indikasi kebocoran/segregasi gagal, ditangani sebagai insiden keamanan kritikal lewat jalur penanganan penyalahgunaan (bagian 16) dan tercatat di audit trail (bagian 9) sebagai bukti investigasi.

**Pertimbangan Non-Fungsional**: segregasi data multi-client adalah syarat keamanan mutlak (bukan sekadar filter tampilan) — kegagalan segregasi (kebocoran data satu client ke client lain) tergolong insiden keamanan kritikal, bukan bug fungsional biasa; kontrol wajib diterapkan konsisten di seluruh lapisan (query data, API, ekspor laporan), tidak cukup hanya di lapisan antarmuka.

---

## 3. Siklus Utama: Monitoring & Approval Data Kehadiran Tenaga Penempatan

### 3.1 Pre-Cycle

Data kehadiran (absensi harian, cuti, izin, sakit, lembur) sudah tercatat dari proses Time Management di lapangan, tersedia untuk ditampilkan real-time di portal.

### 3.2 Life Cycle

**Actor**: PIC client/Site Supervisor (endorser), HR internal (approver final), sistem (agregasi dashboard).

**Proses bisnis**:
1. Dashboard menampilkan kehadiran tenaga per site secara granular per kategori: absensi harian, cuti, izin, sakit (dengan lampiran surat keterangan dokter jika ada), lembur, dan koreksi absensi (pengajuan ulang jika ada kesalahan sistem/lupa check-in/out).
2. Status kepatuhan SLA response time replacement (domain Reliever & Buffer) ditampilkan bersamaan sebagai konteks.
3. Untuk kategori yang membutuhkan endorsement fungsional dari PIC client/Site Supervisor (mengikuti dualitas Garis Struktural vs Fungsional pada domain Garis Komando) — misal lembur yang perlu sepengetahuan client, atau koreksi absensi yang perlu konfirmasi PIC client sebagai saksi kehadiran aktual — portal menyediakan tombol approve/reject dengan catatan.
4. Endorsement PIC client diteruskan sebagai salah satu input ke approval final HR internal — bersifat **endorsement operasional**, bukan pengganti approval administratif HR sesuai kontrak kerja.

**Data/field**: kategori kehadiran, tanggal, site, status endorsement PIC client (jika berlaku), status approval final HR.

**Business rule & validasi**: field yang ditampilkan tunduk pada kebijakan klasifikasi field yang ditetapkan pada domain Employee Data Governance — portal tidak menampilkan data pribadi sensitif karyawan yang tidak relevan bagi client (lihat bagian 6).

**Business rule & validasi (Lembur)**: proses endorsement lembur pada portal beroperasi dalam koridor Peraturan Pemerintah Nomor 35 Tahun 2021 tentang Perjanjian Kerja Waktu Tertentu, Alih Daya, Waktu Kerja dan Waktu Istirahat, dan Pemutusan Hubungan Kerja, yang antara lain membatasi waktu kerja lembur paling banyak 4 (empat) jam dalam 1 (satu) hari dan 18 (delapan belas) jam dalam 1 (satu) minggu serta mensyaratkan persetujuan tertulis pekerja sebelum lembur dilaksanakan. Endorsement PIC client pada portal ini adalah konfirmasi operasional atas jam lembur yang benar-benar terjadi di lapangan (sebagai saksi kehadiran aktual), bukan pengganti syarat persetujuan tertulis pekerja maupun kepatuhan terhadap batas jam kerja lembur — kedua kewajiban tersebut tetap menjadi tanggung jawab perusahaan outsourcing sebagai pemberi kerja dan divalidasi terpisah oleh HR internal sebelum lembur dihitung dalam payroll.

**Approval Workflow**: endorsement PIC client adalah tahap tambahan yang berjalan sebelum approval final HR internal, mengikuti matrix Garis Komando kategori yang bersangkutan (mis. lembur mengikuti kategori administratif HR) — endorsement client yang ditolak tidak otomatis membatalkan pengajuan, melainkan dikembalikan ke pemohon untuk klarifikasi. Skenario dikelompokkan mengikuti pola baku Setuju/Tolak/Perlu Revisi/Eksepsi berikut, dengan catatan skenario Tolak memiliki dua varian tergantung titik penolakannya:
- **Skenario Setuju (Endorsement & Approval HR)** — PIC client mengendorse dan HR internal menyetujui; transaksi (lembur/koreksi absensi) final dan masuk perhitungan payroll.
- **Skenario Tolak** — pengajuan tidak disetujui pada salah satu dari dua titik berikut, keduanya bermuara pada pengembalian pengajuan untuk klarifikasi (bukan pembatalan permanen):
  - *Varian A — PIC Client Menolak Endorsement*: PIC client menyatakan tidak mengenali/tidak menyetujui klaim (mis. lembur yang diklaim tidak sesuai catatan client); pengajuan dikembalikan ke pemohon/site supervisor untuk klarifikasi dengan catatan penolakan — pemohon dapat mengajukan ulang dengan bukti tambahan.
  - *Varian B — HR Menolak Meski Diendorse Client*: endorsement client hanya bersifat operasional, bukan pengganti approval administratif; HR internal tetap dapat menolak pengajuan yang sudah diendorse client jika ditemukan ketidaksesuaian dari sisi administratif HR (mis. saldo cuti tidak mencukupi, dokumen pendukung sakit tidak lengkap, atau indikasi pelanggaran batas jam lembur sesuai PP 35/2021) — keputusan akhir tetap di HR internal.
- **Skenario Perlu Revisi (Kelengkapan Dokumen Pendukung)** — pengajuan pada dasarnya wajar namun dokumen pendukungnya belum memadai (mis. lampiran surat keterangan dokter untuk sakit tidak terbaca/kedaluwarsa formatnya, atau bukti pendukung koreksi absensi kurang lengkap); item dikembalikan ke pemohon untuk melengkapi dokumen tanpa berstatus ditolak penuh — begitu dokumen lengkap, pengajuan dapat langsung dilanjutkan ke endorsement/approval tanpa mengulang proses dari awal.
- **Skenario Eksepsi — Tidak Ada Respons Endorsement (Timeout)** — PIC client tidak merespons dalam SLA yang ditetapkan; pengajuan yang sifatnya tidak wajib menunggu endorsement (mis. koreksi absensi rutin tanpa dampak signifikan ke billing) dapat diteruskan langsung ke approval HR internal tanpa endorsement, dicatat sebagai "Endorsement Timeout — Diproses Tanpa Konfirmasi Client".

**Notifikasi/trigger**: notifikasi ke PIC client saat ada item baru menunggu endorsement; notifikasi ke HR internal saat endorsement client selesai.

**Dokumen/output**: dashboard kehadiran real-time, log endorsement per transaksi.

### 3.3 Post-Cycle

Data kehadiran yang sudah final menjadi sumber bagi Konsolidasi Payroll (domain Payroll) dan bagi Laporan Operasional Site/BAST sebagai dasar penagihan (domain PKS/Billing).

**Jalur kegagalan/ketidaksinkronan**: jika payroll run (domain Payroll) sudah berjalan sebelum seluruh endorsement/approval kehadiran periode berjalan selesai final, data yang dipakai payroll wajib mengikuti cut-off resmi yang sudah disepakati (domain Payroll, bagian Konsolidasi) — transaksi yang endorsement-nya baru selesai setelah cut-off diperlakukan sebagai penyesuaian pada periode berikutnya (*retroactive adjustment*), bukan memaksa payroll run tertunda menunggu seluruh endorsement rampung. Ketentuan yang sama berlaku untuk penyusunan BAST sebagai dasar penagihan — BAST yang sudah diterbitkan sebelum endorsement final selesai wajib direvisi lewat mekanisme Credit-Debit Note (bagian 5) jika ditemukan selisih material setelah endorsement rampung.

---

## 4. Sub-Siklus: Replacement Request & Komplain via Portal

**Pre-Cycle**: client mengidentifikasi kebutuhan penggantian tenaga (alasan operasional, bukan buy-out — lihat bagian 15 untuk perbedaannya) atau ketidakpuasan yang perlu diformalkan sebagai komplain.

**Life Cycle**:
- **Actor**: PIC client (pemohon), Reliever & Buffer/Account Manager (penindaklanjut).
- **Proses bisnis**: **Replacement Request** diajukan langsung dari portal, memicu otomatis proses assignment pada domain Reliever & Buffer; **Komplain formal** diajukan sebagai tiket terlacak, memicu proses pada domain Employee Discipline dan tercatat sebagai temuan setara temuan Visit Management.
- **Business rule**: kedua jenis pengajuan memiliki SLA respons yang dipantau dari portal — keterlambatan respons terhadap salah satu jenis pengajuan ini menjadi indikator kualitas layanan yang masuk skor kesehatan akun client (bagian 10.3).

**Approval Workflow**: replacement request tidak memerlukan approval tambahan di sisi client (langsung memicu proses internal); status penyelesaian ditampilkan kembali ke client sebagai bagian transparansi.

**Notifikasi/trigger**: notifikasi ke domain tujuan (Reliever & Buffer/Discipline) saat pengajuan masuk; notifikasi ke client saat status berubah.

**Dokumen/output**: tiket replacement request/komplain dengan riwayat status.

**Post-Cycle**: riwayat komplain & replacement request per client menjadi input skor kesehatan akun (bagian 10.3) dan bahan evaluasi renewal PKS (domain PKS).

---

## 5. Akses Read-Only: Dokumen Kontrak, Billing, dan Laporan Visit

**Pre-Cycle**: dokumen sumber (PKS, invoice, laporan visit) harus sudah final/diterbitkan resmi di domain pemiliknya masing-masing (PKS, Payroll & Billing, Visit Management) sebelum dapat ditampilkan ke portal — portal tidak menampilkan draft/dokumen yang belum final.

**Life Cycle**:

- **Akses Dokumen Kontrak (PKS)** — client dapat melihat status PKS aktif, masa berlaku, dan riwayat amandemen dalam mode baca-saja (tidak dapat mengubah), dengan notifikasi otomatis mendekati masa perpanjangan (domain PKS, bagian Monitoring Masa Berlaku).
- **Akses Billing & Invoice** — unduh invoice, status pembayaran, riwayat tagihan, rincian bukti pendukung (rekap kehadiran/timesheet per periode), dokumen pajak yang wajib disediakan ke client (faktur pajak elektronik/e-Faktur, bukti potong PPh 23 dari client — dua arah dokumen pajak perlu diakomodasi), termasuk riwayat revisi/Credit-Debit Note yang transparan (domain Payroll, bagian Revisi Data Konsolidasi) agar client tidak bingung saat ada perubahan angka.
- **Akses Laporan Visit** — client dapat melihat ringkasan hasil site visit (bukan detail internal sensitif seperti catatan indisipliner karyawan), sebagai bukti transparansi kontrol kualitas (domain Visit Management).

**Business rule bersama**: ketiga jenis akses ini bersifat baca-saja murni — tidak ada aksi tulis/ubah dari sisi client atas dokumen sumbernya, kecuali aksi approve/reject spesifik yang sudah diatur eksplisit di bagian lain (endorsement kehadiran bagian 3, Unified Approval Queue bagian 17).

**Post-Cycle**: setiap akses lihat/unduh dokumen tercatat dalam audit trail (bagian 9), menjadi bukti pendukung jika timbul dispute mengenai dokumen mana yang sudah/belum diakses client; frekuensi akses dokumen turut menjadi bagian data pemakaian portal yang dievaluasi pada skor kesehatan akun (bagian 10.3).

---

## 6. Kepatuhan Data Pribadi & Lokasi Penyimpanan Data

**Pre-Cycle**: klasifikasi field (mana yang boleh/tidak boleh ditampilkan ke client) sudah ditetapkan lebih dulu oleh domain Employee Data Governance sebelum portal menampilkan data apa pun ke client — portal tidak mendefinisikan klasifikasi ini secara mandiri.

**Life Cycle**: setiap kali data karyawan ditampilkan/diunduh lewat portal, pembatasan field sesuai klasifikasi ditegakkan secara konsisten di seluruh titik akses (dashboard, ekspor laporan, API).

**Post-Cycle**: kepatuhan klasifikasi field dan lokasi penyimpanan data ditinjau secara berkala sebagai bagian audit kepatuhan data pribadi; setiap perubahan kebijakan klasifikasi di domain Employee Data Governance wajib disinkronkan kembali ke aturan tampilan portal.

**Business rule & validasi**: field data karyawan yang boleh ditampilkan ke client (nama, posisi) vs yang wajib disembunyikan (gaji internal karyawan, riwayat kesehatan, data keluarga, NIK, NPWP, rekening bank) tunduk pada kebijakan klasifikasi field yang **ditetapkan dan dikonsumsi langsung dari** domain Employee Data Governance — portal tidak mendefinisikan ulang klasifikasi ini secara terpisah. Perusahaan outsourcing tetap berkedudukan sebagai **pengendali data (data controller)** atas seluruh data karyawan; client, sejauh mendapat akses baca terbatas via portal, berkedudukan sebagai pihak yang menerima data terbatas untuk kepentingan operasional yang sah, bukan sebagai pengendali data bersama. Ketentuan ini merujuk pada Undang-Undang Nomor 27 Tahun 2022 tentang Pelindungan Data Pribadi.

**Lokasi & Keamanan Penyimpanan Data (Data Residency)**: data yang diakses lewat portal (termasuk data pribadi karyawan yang ditampilkan terbatas) perlu dipertimbangkan lokasi penyimpanannya — disimpan di server yang berlokasi di Indonesia menjadi preferensi kepatuhan yang lebih aman, mengingat ketentuan transfer data pribadi ke luar negeri di bawah Undang-Undang Pelindungan Data Pribadi memerlukan syarat tambahan (tingkat pelindungan yang setara, atau persetujuan subjek data) jika platform di-*hosting* di luar Indonesia.

**Integrasi antar tahap**: bagian ini adalah sisi "akses baca client" dari kepatuhan data pribadi; sisi "consent karyawan sebagai subjek data" dikelola tersendiri pada domain akses mandiri karyawan (ESS) — keduanya saling melengkapi, bukan saling menggantikan (lihat linkage bidirectional pada domain Employee Data Governance, bagian Kontrol Akses Identitas di Client Portal).

---

## 7. Sub-Siklus: Notifikasi & Alert Proaktif ke Client

**Pre-Cycle**: ambang/kondisi trigger ditetapkan per jenis alert (mis. reminder PKS H-60/H-30/H-14, ambang ketidakhadiran tanpa reliever pengganti).

**Life Cycle**:
- **Proses bisnis**: sistem mengirim reminder PKS mendekati jatuh tempo, alert jika ada tenaga tidak hadir tanpa reliever pengganti (potensi SLA breach sebelum benar-benar terjadi), notifikasi invoice jatuh tempo, dan **ringkasan status Requisition Fulfillment** untuk PKS milik client tersebut (persentase headcount terisi vs komitmen, estimasi tanggal fulfillment penuh) sebagai visibilitas proaktif, bukan hanya alert reaktif setelah SLA breach benar-benar terjadi.
- **Business rule**: alert proaktif (sebelum breach) dan alert reaktif (setelah breach) dibedakan eksplisit dalam kategorisasi notifikasi, karena tujuan komunikasinya berbeda — proaktif untuk mitigasi, reaktif untuk transparansi insiden yang sudah terjadi.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Jenis Alert | Enum | Reminder PKS H-14, Alert SLA Risk, Invoice Jatuh Tempo, Ringkasan Requisition Fulfillment | Menentukan template pesan & tingkat kekritisan |
| Tanggal Trigger | Tanggal (YYYY-MM-DD) | 2026-08-17 | Tanggal ambang/kondisi terpenuhi |
| PKS/Client Terkait | String (kode PKS) | PKS-2024-00187 | Referensi ke domain PKS |
| Kanal Pengiriman | Enum (multi-select) | Email, WhatsApp Business API, Portal In-App | Satu alert dapat dikirim ke lebih dari satu kanal sekaligus |
| Status Kirim | Enum | Terkirim, Gagal, Tertunda, Dikirim Ulang | Dasar audit keterkiriman |
| Waktu Kirim | Datetime | 2026-08-17T09:00:00+07:00 | - |

**Dokumen/output**: log notifikasi terkirim per client, dapat diaudit untuk memastikan alert kritikal benar-benar terkirim tepat waktu.

**Post-Cycle**: efektivitas notifikasi proaktif (apakah client sempat merespons sebelum breach benar-benar terjadi) menjadi bagian evaluasi skor kesehatan akun (bagian 10.3).

**Jalur kegagalan/ketidaksinkronan**: jika pengiriman notifikasi gagal (mis. WhatsApp Business API gagal mengirim reminder PKS H-14 karena gangguan layanan pihak ketiga atau nomor PIC client tidak valid/sudah tidak aktif), sistem wajib mencatat status "Gagal" pada log notifikasi (bukan dianggap terkirim), melakukan percobaan pengiriman ulang otomatis dalam jendela waktu tertentu, dan mengeskalasi ke kanal alternatif (email atau notifikasi in-app portal) serta ke Account Manager jika alert tergolong kritikal (mis. reminder PKS mendekati jatuh tempo) dan percobaan ulang tetap gagal — memastikan kegagalan satu kanal tidak berarti client tidak pernah diberi tahu sama sekali.

---

## 8. Self-Service Reporting/Export

Client dapat men-generate laporan sendiri (kehadiran per periode, ringkasan billing) dalam format umum (PDF/Excel) tanpa perlu meminta ke Account Manager — mengurangi beban permintaan laporan manual yang berulang.

**Pre-Cycle**: data sumber yang akan digenerate (kehadiran, billing) harus sudah berstatus final/terverifikasi di domain asalnya (Time Management, Payroll & Billing) — laporan tidak digenerate dari data yang masih berjalan proses endorsement/approval agar tidak menyesatkan client.

**Life Cycle**: client memilih jenis laporan, periode, dan format output; sistem menyaring data sesuai cakupan akses (bagian 2.2) dan klasifikasi field yang berlaku (bagian 6) sebelum laporan digenerate.

**Post-Cycle**: setiap unduhan laporan tercatat dalam audit trail (bagian 9); volume dan frekuensi pemakaian fitur ini menjadi salah satu indikator tingkat adopsi portal yang masuk skor kesehatan akun (bagian 10.3).

---

## 9. Sub-Siklus: Audit Trail & Keamanan Akses

**Pre-Cycle**: kebijakan keamanan akses (kebijakan password, 2FA) ditetapkan sebagai baku sebelum akun PIC client aktif.

**Life Cycle**:
- **Actor**: sistem (pencatatan otomatis), IT/HR Ops (administrator keamanan).
- **Proses bisnis**: setiap aktivitas user client di portal dicatat (siapa akses apa, kapan); mekanisme *suspend* akses berjalan otomatis saat PKS berakhir/diputus (domain PKS, bagian Pemutusan PKS), memastikan tidak ada akses yang tertinggal aktif pasca-hubungan kontraktual berakhir.
- **Business rule**: suspend akses akibat pemutusan PKS berbeda dari suspend akibat penyalahgunaan (bagian 16) — keduanya memakai mekanisme teknis yang sama tapi trigger dan proses pemulihannya berbeda (PKS berakhir = permanen kecuali PKS baru diteken; penyalahgunaan = dapat dipulihkan setelah eskalasi selesai). **Jalur jika suspend otomatis gagal terpicu saat PKS berakhir** (mis. keterlambatan sinkronisasi status PKS ke modul akses portal): dianggap insiden keamanan yang wajib dideteksi lewat rekonsiliasi berkala antara status PKS aktif dan daftar akun portal aktif — bukan diasumsikan selalu berhasil karena "seharusnya otomatis".

**Pertimbangan Non-Fungsional**: log audit trail wajib bersifat *immutable* dan tersedia untuk investigasi kapan pun dibutuhkan (termasuk untuk PKS yang sudah lama berakhir), mengingat perannya sebagai bukti pendukung dispute hukum.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| User ID | String | PIC-CLT-00456 | Identitas user client yang melakukan aksi |
| Jenis Aksi | Enum/String | Login, Unduh Invoice, Approve Lembur, Ekspor Laporan | Kategori aktivitas yang dicatat |
| Timestamp | Datetime | 2026-08-17T14:32:10+07:00 | Waktu aksi terjadi |
| Alamat IP | String | 103.10.xx.xx | Untuk investigasi keamanan/lokasi akses |
| Objek yang Diakses | String | INV-2026-0817, ATT-2026-08170088 | Entitas/dokumen yang diakses atau diubah |
| Hasil Aksi | Enum | Berhasil, Gagal, Ditolak Sistem | Status akhir dari aksi yang dilakukan |

**Notifikasi/trigger**: notifikasi ke IT/HR Ops saat rekonsiliasi berkala menemukan anomali (akun portal masih aktif meski PKS terkait sudah berakhir); notifikasi ke Account Manager saat sistem mendeteksi pola akses mencurigakan yang menjadi pemicu awal penanganan penyalahgunaan (bagian 16).

**Dokumen/output**: log audit trail akses portal, dapat difilter per client/user/rentang waktu.

**Post-Cycle**: log ini menjadi bukti pendukung investigasi jika terjadi dugaan penyalahgunaan akses (bagian 16) atau dispute mengenai siapa yang melakukan suatu aksi di portal.

---

## 10. Desain Inklusif & Tata Kelola Relasi Client

### 10.1 Integrasi Mobile & Ketersediaan untuk PIC Lapangan

PIC client di site (bukan hanya level kantor pusat) memerlukan akses versi ringan/mobile untuk approval cepat (misal approve timesheet mingguan langsung dari ponsel) — desain ini melengkapi Unified Client Approval Queue (bagian 17) agar approval tidak tertunda hanya karena PIC lapangan tidak membuka portal versi desktop.

- **Pre-Cycle**: PIC lapangan didaftarkan dengan preferensi akses mobile saat onboarding akses client (bagian 12), termasuk pendaftaran nomor perangkat/kontak yang relevan untuk notifikasi mobile.
- **Life Cycle**: PIC lapangan menerima notifikasi item approval dan melakukan aksi approve/reject langsung dari versi mobile, disinkronkan real-time dengan Unified Client Approval Queue (bagian 17).
- **Post-Cycle**: aksi approval via mobile tercatat dengan cara yang sama seperti aksi via desktop pada audit trail (bagian 9) — tidak ada perbedaan bobot bukti antara kanal mobile dan desktop.

### 10.2 Kesenjangan Kanal Komunikasi & Konektivitas Daerah

**Business rule & validasi**: banyak PIC client (terutama site supervisor lapangan atau client skala UMKM) masih lebih terbiasa berkomunikasi via WhatsApp/telepon dibanding membuka portal web — strategi *hybrid channel* diterapkan: portal sebagai sumber data resmi/*single source of truth*, dengan notifikasi push ke WhatsApp Business API sebagai jembatan, bukan menggantikan portal sepenuhnya. Untuk site di luar kota besar/daerah dengan konektivitas terbatas: desain portal ringan (*low-bandwidth friendly*), opsi akses via SMS/USSD untuk notifikasi kritis, atau laporan yang tetap dapat diunduh dalam mode *offline-sync*.

- **Pre-Cycle**: asesmen kebiasaan komunikasi PIC dan kondisi konektivitas site dilakukan saat onboarding client/site (domain PKS, bagian Asesmen Site) untuk menentukan kombinasi kanal yang relevan bagi site tersebut.
- **Life Cycle**: kombinasi kanal (portal, WhatsApp, SMS/USSD, mode offline-sync) berjalan paralel sesuai hasil asesmen, dengan portal tetap menjadi rujukan status resmi meski notifikasi/interaksi awal terjadi lewat kanal lain.
- **Post-Cycle**: efektivitas kanal alternatif (tingkat respons, keterlambatan akibat konektivitas) dievaluasi berkala dan menjadi salah satu masukan penyesuaian *service level* per tier (bagian 10.3).

**Jalur kegagalan/ketidaksinkronan**: lihat jalur kegagalan pengiriman notifikasi WhatsApp yang dijabarkan pada bagian 7 — prinsip retry dan eskalasi kanal yang sama berlaku untuk seluruh notifikasi lintas kanal pada bagian ini.

### 10.3 Sub-Siklus: Tiering Client & Peran Account Manager

**Pre-Cycle**: kriteria tiering ditetapkan sebagai kebijakan baku — nilai kontrak/bill rate total, jumlah headcount, tingkat strategis (potensi ekspansi/referensi) — dikombinasikan jadi skor/kategori tier (Strategic/Enterprise/Standard/UMKM).

**Life Cycle**:
- **Actor**: Account Manager (pemilik relasi), Sales/BD (input awal saat akuisisi client).
- **Proses bisnis**: setiap client diklasifikasi ke satu tier; *service level* berbeda diterapkan per tier — kadens *engagement* rutin (Strategic: kunjungan bulanan terjadwal; Standard: reaktif sesuai kebutuhan — melengkapi jenis kunjungan pada domain Visit Management dengan dimensi kadens), prioritas SLA response (domain PKS, Kerangka SLA), dan level integrasi teknis (API *system-to-system* untuk client enterprise besar seperti perbankan/manufaktur multinasional, vs portal manual sederhana untuk client UMKM — kebutuhan requirement dipisah eksplisit berdasarkan tier).
- **Client Health Score**: agregasi dari frekuensi komplain (bagian 4), performa SLA (domain PKS), dan tingkat pemakaian portal (bagian 8) menjadi satu skor kesehatan akun per client, dipakai sebagai *early-warning* sebelum client berpotensi tidak memperpanjang kontrak (domain PKS, bagian Monitoring Masa Berlaku).

**Business rule & validasi**: perubahan tier client memicu penyesuaian *service level* secara eksplisit (bukan bertahan pada level lama) — client yang naik tier (ekspansi kontrak signifikan) berhak atas kadens engagement & prioritas SLA sesuai tier barunya.

**Approval Workflow**: penetapan/perubahan tier memerlukan konfirmasi Account Manager & Sales/BD; tidak memerlukan approval berjenjang tambahan kecuali berdampak pada alokasi sumber daya signifikan (misal penambahan tim dedicated untuk client Strategic baru).
- **Skenario Setuju** — Account Manager & Sales/BD sepakat atas tier yang diusulkan; service level baru berlaku efektif.
- **Skenario Tolak** — Account Manager & Sales/BD menilai kriteria tiering sama sekali belum mengarah ke tier yang diusulkan (mis. nilai kontrak jauh di bawah ambang tier yang diajukan tanpa indikasi potensi ekspansi jangka pendek); pengajuan perubahan tier ditolak, client tetap pada tier saat ini, dan pengajuan baru hanya dapat diajukan kembali pada siklus evaluasi berikutnya sesuai kadens tiering baku.
- **Skenario Perlu Revisi (Data Tambahan)** — kriteria tiering terpenuhi sebagian (mis. nilai kontrak sudah memenuhi ambang tetapi data jumlah headcount atau indikasi potensi ekspansi belum lengkap/belum terverifikasi); pengajuan tier ditahan sementara, Account Manager diminta melengkapi data pendukung tambahan — berbeda dari Skenario Tolak, pengajuan ini dapat langsung dievaluasi ulang begitu data tambahan lengkap, tanpa menunggu siklus evaluasi berikutnya.
- **Skenario Eskalasi Direksi (Perubahan Signifikan)** — kenaikan tier yang memerlukan alokasi sumber daya besar (tim dedicated, integrasi API khusus); memerlukan approval tambahan Direksi sebelum service level baru diterapkan, di luar konfirmasi Account Manager & Sales/BD saja.

**Notifikasi/trigger**: notifikasi ke Account Manager saat skor kesehatan akun client turun melewati ambang tertentu (*early-warning*); notifikasi ke Direksi saat pengajuan perubahan tier memerlukan eskalasi karena alokasi sumber daya signifikan.

**Kontinuitas Saat Account Manager Berganti**: berbeda arah dari pergantian PIC di sisi client (bagian 11) — saat terjadi mutasi internal Account Manager, dilakukan serah-terima riwayat relasi, kontak, dan catatan khusus client ke AM pengganti, supaya client tidak merasa memulai relasi dari nol setiap kali terjadi mutasi internal perusahaan outsourcing.

**Dokumen/output**: klasifikasi tier per client, skor kesehatan akun, catatan serah-terima relasi saat pergantian AM.

**Post-Cycle**: skor kesehatan akun dipantau tren-nya dari waktu ke waktu, menjadi bahan evaluasi proaktif sebelum masa renewal PKS tiba, bukan baru dievaluasi saat PKS sudah mendekati jatuh tempo.

### 10.4 Dukungan Multi-Bahasa untuk Client PMA

Sebagian client adalah Penanaman Modal Asing dengan PIC berbahasa Inggris — portal mendukung minimal Bahasa Indonesia dan Inggris, khususnya untuk dokumen legal/kontrak dan invoice.

- **Pre-Cycle**: preferensi bahasa PIC client dicatat saat onboarding akses client baru (bagian 12) atau dapat diubah sewaktu-waktu lewat pengaturan akun.
- **Life Cycle**: tampilan portal, notifikasi, dan dokumen yang dapat dialihbahasakan (invoice, ringkasan kontrak) disajikan sesuai preferensi bahasa yang tersimpan; dokumen hukum asli (PKS) tetap mengacu pada versi yang sah secara hukum meski disediakan terjemahan pendamping untuk kemudahan pembacaan.
- **Post-Cycle**: tidak ada dampak lanjutan khusus di luar siklus pemakaian rutin portal — preferensi bahasa tetap berlaku pada seluruh interaksi berikutnya sampai diubah oleh PIC client yang bersangkutan.

---

## 11. Sub-Siklus: Verifikasi Identitas & Kewenangan PIC Client

**Pre-Cycle**: calon PIC client didaftarkan sebagai user portal, baik saat onboarding client baru (bagian 12) maupun saat pergantian PIC di tengah masa kontrak.

**Life Cycle**:
- **Actor**: Account Manager (verifikator awal), Legal (validasi kewenangan untuk aksi sensitif).
- **Proses bisnis**:
  1. Validasi bahwa user yang didaftarkan sebagai PIC client benar-benar berwenang secara legal mewakili perusahaan client — dipecah menjadi tiga sub-pemeriksaan berjenjang:
     - **1a. Cek kelengkapan dokumen** — memastikan dokumen pendukung kewenangan (surat kuasa/surat penunjukan dari perusahaan client) tersedia lengkap sesuai format yang dipersyaratkan (tanda tangan, kop surat, cap perusahaan, masa berlaku).
     - **1b. Cek kesesuaian dokumen dengan jabatan yang diklaim** — memverifikasi bahwa jabatan yang tercantum dalam dokumen kewenangan sesuai dengan jabatan yang diklaim user saat pendaftaran, dan bahwa pihak yang menandatangani dokumen memang memiliki kewenangan sesuai struktur organisasi perusahaan client (mis. bukan ditandatangani oleh pihak yang levelnya tidak setara untuk memberi kuasa).
     - **1c. Cek indikasi pemalsuan** — memeriksa keaslian dokumen (kewajaran tanda tangan, kop surat, cap perusahaan) dan mencocokkannya dengan data perusahaan client yang sudah tercatat (mis. PKS, dokumen legalitas sebelumnya) untuk mendeteksi indikasi dokumen palsu/dipalsukan.
     Validasi ini krusial khususnya untuk aksi sensitif seperti approve invoice atau amandemen PKS.
  2. Saat terjadi pergantian PIC di sisi client: akses lama di-*offboarding* (dinonaktifkan), akses baru di-*onboarding* mengikuti proses verifikasi yang sama (1a-1c) seperti PIC baru.

**Data/field**: identitas PIC, dokumen pendukung kewenangan (surat kuasa/penunjukan dari perusahaan client), riwayat pergantian PIC per client.

**Business rule & validasi**: aksi sensitif (approve invoice, amandemen PKS) hanya dapat dilakukan oleh PIC yang sudah lolos verifikasi kewenangan tingkat tinggi — PIC dengan akses operasional harian biasa (misal endorsement kehadiran) tidak otomatis memiliki kewenangan untuk aksi sensitif ini kecuali didaftarkan secara terpisah dengan verifikasi tambahan.

**Approval Workflow**: pendaftaran PIC baru dengan kewenangan aksi sensitif memerlukan approval Account Manager & Legal sebelum akun diaktifkan penuh.
- **Skenario Setuju** — dokumen kewenangan (surat kuasa/penunjukan) valid dan sesuai; akun diaktifkan penuh dengan kewenangan aksi sensitif.
- **Skenario Tolak** — dokumen kewenangan tidak valid/tidak sesuai jabatan yang diklaim; pendaftaran ditolak, akun tetap berstatus akses operasional harian biasa (tanpa kewenangan aksi sensitif) sampai dokumen yang sah diserahkan.
- **Skenario Perlu Dokumen Tambahan (Revisi)** — dokumen pada dasarnya sah namun ada kekurangan minor (mis. surat kuasa belum ditandatangani pihak berwenang, atau sudah mendekati kedaluwarsa); akun diaktifkan sementara dengan akses operasional harian saja sambil menunggu dokumen final untuk kewenangan aksi sensitif.

**Notifikasi/trigger**: notifikasi ke Account Manager saat ada permintaan pendaftaran/pergantian PIC.

**Dokumen/output**: dokumen verifikasi kewenangan (tersimpan bersama Document Vault PKS terkait), log riwayat pergantian PIC.

**Post-Cycle**: akses PIC lama yang sudah di-*offboarding* tetap tercatat dalam audit trail (bagian 9) untuk kebutuhan penelusuran historis meski akunnya sudah tidak aktif.

---

## 12. Sub-Siklus: Skema Onboarding Akses Client Baru

**Pre-Cycle**: PKS baru sudah ditandatangani (domain PKS, bagian Drafting & Approval).

**Life Cycle**:
- **Actor**: Account Manager, IT (provisioning akun), PIC client baru.
- **Proses bisnis**: akses portal diberikan begitu PKS aktif, PIC client baru melalui proses Verifikasi Identitas & Kewenangan (bagian 11) sebelum akun diaktifkan, dilanjutkan training singkat penggunaan portal.
- **Business rule**: pemberian akses portal berjalan paralel dengan proses Client Operational Onboarding lain (asesmen site, SOP capture — domain PKS) sebagai salah satu dari tiga proses paralel yang harus selesai sebelum status "client aktif beroperasi penuh" tercapai.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nama PIC Client | String | Budi Santoso | - |
| Email Kerja | String (format email) | budi.santoso@clientabc.co.id | Dipakai sebagai username login |
| Nomor Telepon | String | +6281234567890 | Untuk notifikasi WhatsApp Business API |
| PKS Terkait | String (kode PKS) | PKS-2026-00042 | Menentukan lingkup akses (bagian 2.2) |
| Role/Peran | Enum | PIC Operasional, Manajemen/Direksi, HR Client | Menentukan level akses sesuai bagian 2.2 |
| Status Training | Enum | Belum Selesai, Selesai | Prasyarat aktivasi akun penuh |
| Tanggal Aktivasi | Tanggal (YYYY-MM-DD) | 2026-08-20 | - |

**Notifikasi/trigger**: notifikasi ke Account Manager & IT saat PKS baru berstatus aktif dan siap untuk provisioning akun; notifikasi ke PIC client baru saat kredensial akses portal diterbitkan dan saat jadwal training ditetapkan.

**Dokumen/output**: kredensial akses portal PIC client baru, catatan penyelesaian training.

**Post-Cycle**: akses yang sudah aktif masuk ke siklus pemakaian rutin (bagian 3-10) dan siklus audit trail (bagian 9).

---

## 13. Sub-Siklus: Eskalasi dari Portal ke Jalur Formal

**Pre-Cycle**: komplain/dispute yang diajukan lewat portal (bagian 4) tidak terselesaikan sesuai SLA yang berlaku.

**Life Cycle**:
- **Actor**: Account Manager, Legal (untuk eskalasi ke jalur hukum).
- **Proses bisnis**: portal mencatat status eskalasi ke jalur lebih formal — dapat berupa negosiasi ulang PKS (domain PKS, bagian Amandemen) atau, jika menyangkut isu ketenagakerjaan yang tidak terselesaikan secara internal, jalur mediasi/Perselisihan Hubungan Industrial (domain Employee Discipline).
- **Business rule**: status eskalasi tetap terlihat oleh client di portal sebagai bagian transparansi proses, meski detail substansi negosiasi/hukum berjalan di luar sistem portal.

**Notifikasi/trigger**: notifikasi ke Legal saat komplain resmi dieskalasi ke jalur formal.

**Dokumen/output**: log status eskalasi per komplain, rujukan ke dokumen formal yang dihasilkan (amandemen PKS atau dokumen proses PHI).

**Post-Cycle**: riwayat eskalasi menjadi bagian evaluasi skor kesehatan akun client (bagian 10.3) — frekuensi eskalasi tinggi mengindikasikan hubungan client yang perlu perhatian manajemen senior.

---

## 14. Sub-Siklus: Kanal Pengajuan Kebutuhan Tenaga Baru (Client-Initiated Request)

Berbeda dari Replacement Request (bagian 4, mengganti tenaga existing), ini adalah kanal client mengajukan **kebutuhan tenaga baru/tambahan** langsung dari portal — baik untuk ekspansi dalam PKS existing maupun kebutuhan mendadak yang levelnya Sales/Service Order.

**Pre-Cycle**: client mengidentifikasi kebutuhan tambahan headcount.

**Life Cycle**:
- **Actor**: PIC client (pemohon), Talent Acquisition (pemroses requisition).
- **Proses bisnis**: pengajuan menjadi salah satu kategori dalam Taksonomi Sumber Kebutuhan (kepemilikan domain Talent Acquisition, melengkapi kategori Replacement, Ekspansi PKS existing, Ramp-up terencana, dan SO/transaksional) — pengajuan client tetap wajib melalui Approval MPR di sisi internal sebelum menjadi requisition aktif; portal hanya berfungsi sebagai kanal *intake* awal, **bukan bypass** approval internal.
- **Business rule & validasi**: tidak ada jalur pintas dari pengajuan client langsung menjadi requisition aktif tanpa approval MPR — prinsip ini berlaku konsisten dengan seluruh kategori sumber kebutuhan lain, memastikan kontrol budget/sourcing tetap terjaga meski permintaan datang dari client.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nomor Pengajuan | String | REQ-CLT-2026-0031 | ID unik pengajuan dari portal |
| Jenis Kebutuhan | Enum | Ekspansi PKS Existing, Kebutuhan Mendadak (SO) | Menentukan kategori pada Taksonomi Sumber Kebutuhan (Talent Acquisition) |
| Jumlah Headcount | Numerik | 5 | - |
| Posisi/Jabatan | String | Security Officer | - |
| Site Penempatan | String | Site Jakarta - Gedung A | - |
| Target Tanggal Mulai | Tanggal (YYYY-MM-DD) | 2026-09-15 | - |
| Status Pengajuan | Enum | Diterima, Dalam Proses MPR, Disetujui, Ditolak | Wajib sinkron dengan status riil di Talent Acquisition |

**Approval Workflow**: mengikuti Approval MPR (kepemilikan proses domain Talent Acquisition) di sisi internal.

**Notifikasi/trigger**: notifikasi ke Talent Acquisition saat pengajuan baru masuk dari portal.

**Dokumen/output**: status pengajuan (diterima/dalam proses/ditolak dengan alasan) terlihat oleh client di portal — transparansi yang sama seperti Requisition Fulfillment (bagian 7).

**Jalur kegagalan/ketidaksinkronan**: jika status pengajuan yang ditampilkan di portal belum mencerminkan status riil requisition di sistem Talent Acquisition (mis. requisition sudah disetujui/ditolak lewat Approval MPR internal tetapi belum ter-sinkron ke tampilan portal karena keterlambatan integrasi), portal wajib menampilkan indikator "status terakhir diperbarui pada [timestamp]" agar client tidak salah asumsi, dan rekonsiliasi berkala dijalankan untuk mendeteksi selisih status antar kedua sistem — status final yang mengikat selalu status di sistem Talent Acquisition, bukan tampilan portal yang berpotensi tertunda.

**Post-Cycle**: pengajuan yang disetujui masuk ke siklus Requisition Fulfillment standar; status fulfillment-nya kembali ditampilkan ke client lewat notifikasi proaktif (bagian 7).

---

## 15. Sub-Siklus: Proses Buy-Out/Direct-Hire oleh Client

Skenario client ingin merekrut langsung karyawan yang sedang ditempatkan — mengakhiri hubungan penempatan outsourcing, karyawan berpindah menjadi karyawan tetap client. Berbeda dari Replacement Request (bagian 4, karyawan diganti karena alasan operasional) dan berbeda dari Sub-rogasi/Pengalihan Tenaga Kerja (domain PKS, levelnya pindah antar-vendor, bukan ke client langsung).

**Pre-Cycle**: client mengajukan minat buy-out atas karyawan tertentu, baik via portal maupun langsung ke Account Manager.

**Life Cycle**:
- **Actor**: PIC client (pengaju), Account Manager & Legal (evaluasi & approval internal), karyawan yang bersangkutan (pihak yang menyetujui perpindahan).
- **Proses bisnis**: pengajuan buy-out tunduk pada klausul buy-out di PKS jika ada (biaya kompensasi/penalti ke perusahaan outsourcing atas kehilangan tenaga terlatih — domain PKS, bagian Struktur & Klausul); Account Manager & Legal melakukan review dan approval internal sebelum disetujui; jika disetujui, hubungan kerja dengan perusahaan outsourcing diakhiri lewat proses Offboarding dengan kategori tersendiri "Buy-Out/Direct-Hire" (berbeda dari kategori Resign/PHK/Habis Kontrak).

**Data/field**: karyawan yang diajukan buy-out, klausul buy-out PKS terkait (jika ada), nominal kompensasi/penalti (jika berlaku), persetujuan karyawan.

**Business rule & validasi**: karyawan yang menjalani buy-out **eligible rehire secara default** (bukan berstatus blacklist), karena perpindahan ini bukan pelanggaran melainkan kesepakatan bisnis yang sah; biaya kompensasi buy-out (jika ada) dibebankan ke client sebagai bagian Konsolidasi Billing (kepemilikan proses domain Payroll & Billing).

**Business rule & validasi (Rujukan Regulasi Pengakhiran Hubungan Kerja)**: pengakhiran hubungan kerja antara karyawan dengan perusahaan outsourcing dalam skema buy-out/direct-hire tunduk pada Undang-Undang Nomor 13 Tahun 2003 tentang Ketenagakerjaan sebagaimana diubah dengan Undang-Undang Cipta Kerja (Undang-Undang Nomor 6 Tahun 2023), khususnya ketentuan mengenai pengakhiran hubungan kerja dan hak-hak karyawan pasca-pengakhiran. Karena inisiatif perpindahan datang dari kombinasi minat client dan kesediaan karyawan (bukan pemutusan hubungan kerja sepihak oleh perusahaan outsourcing), pengakhiran ini diklasifikasikan setara pengunduran diri atas dasar kesepakatan — sehingga **persetujuan tertulis karyawan yang bersangkutan menjadi syarat sah mutlak**, sejalan dengan prinsip bahwa hubungan kerja tidak dapat diakhiri secara sepihak tanpa dasar yang diatur undang-undang. Karyawan tetap berhak atas uang penggantian hak sesuai masa kerja dan ketentuan yang berlaku, meskipun pengakhiran ini bukan kategori pemutusan hubungan kerja (PHK) atas inisiatif pemberi kerja.

**Approval Workflow**: mengikuti matrix Garis Komando — Account Manager & Legal sebagai approver internal minimal, dengan eskalasi ke Finance jika nominal kompensasi signifikan.
- **Skenario Setuju** — Account Manager & Legal (dan Finance jika nominal signifikan) menyetujui, dan karyawan yang bersangkutan menyatakan persetujuan; buy-out dieksekusi lewat Offboarding kategori khusus.
- **Skenario Tolak (Internal)** — Legal/Account Manager menilai buy-out tidak layak secara kontraktual (mis. klausul buy-out PKS mensyaratkan kompensasi yang belum disepakati client) atau berisiko tinggi (karyawan kunci tanpa pengganti yang siap); pengajuan ditolak dan karyawan tetap berstatus penempatan aktif.
- **Skenario Karyawan Menolak** — proses internal (Account Manager, Legal) sudah menyetujui prinsip buy-out, namun karyawan yang bersangkutan tidak bersedia berpindah menjadi karyawan tetap client; buy-out tidak dapat dipaksakan (memerlukan persetujuan individu karyawan), kasus ditutup tanpa perubahan status penempatan.
- **Skenario Perlu Revisi (Nominal Kompensasi)** — prinsip buy-out disetujui namun nominal kompensasi/penalti yang diajukan client belum sesuai klausul PKS atau belum disepakati Finance; pengajuan ditahan sampai nominal disepakati ulang antara client dan Finance sebelum difinalisasi.

**Notifikasi/trigger**: notifikasi ke Offboarding untuk memproses kategori khusus ini; notifikasi ke Payroll untuk billing kompensasi (jika berlaku).

**Dokumen/output**: dokumen persetujuan buy-out (tiga pihak: perusahaan outsourcing, client, karyawan), catatan kategori offboarding "Buy-Out/Direct-Hire".

**Jalur kegagalan/ketidaksinkronan**: jika proses Offboarding gagal memproses kategori khusus "Buy-Out/Direct-Hire" secara benar (mis. akibat kesalahan input, tercatat sebagai kategori Resign biasa), status eligible-rehire karyawan berisiko tidak tercatat sesuai ketentuan (lihat business rule di atas — eligible rehire secara default). Kondisi ini wajib terdeteksi lewat rekonsiliasi berkala antara dokumen persetujuan buy-out (tiga pihak) dengan kategori offboarding yang tercatat di domain Employee Data Governance, dan dikoreksi manual oleh HR/Legal begitu ditemukan — status eligible-rehire yang keliru berdampak signifikan jika karyawan tersebut melamar kembali di masa depan.

**Post-Cycle**: status eligible-rehire karyawan tersimpan sebagai bagian riwayat identitasnya (domain Employee Data Governance) — relevan jika suatu saat karyawan tersebut melamar kembali.

---

## 16. Sub-Siklus: Penanganan Penyalahgunaan Akses oleh Client

Berbeda arah dari eskalasi komplain (bagian 13) — ini menangani skenario client/PIC yang menyalahgunakan akses portalnya sendiri: berbagi kredensial ke pihak tidak berwenang, mencoba mengakses data di luar cakupan PKS-nya, atau mengunduh & membocorkan data sensitif.

**Pre-Cycle**: sistem/audit trail (bagian 9) mendeteksi pola akses yang mencurigakan, atau ada laporan langsung dugaan penyalahgunaan.

**Life Cycle** — direspons berjenjang:
1. **Peringatan** — untuk pelanggaran ringan pertama kali.
2. **Suspend akses sementara** — memakai mekanisme teknis yang sama seperti suspend saat PKS berakhir (bagian 9), namun dipicu pelanggaran, bukan akhir kontrak.
3. **Eskalasi ke Account Manager & manajemen client** — komunikasi formal mengenai temuan pelanggaran.
4. **Linkage ke klausul pelanggaran di PKS** — jika berulang/serius, menjadi dasar kontraktual tindakan lebih lanjut (domain PKS).

**Actor**: IT/HR Ops (deteksi & eksekusi suspend), Account Manager (komunikasi ke client), Legal (jika sampai tahap linkage klausul PKS).

**Business rule & validasi**: eskalasi berjenjang ini bersifat progresif — tidak boleh langsung lompat ke linkage klausul PKS tanpa melalui peringatan dan suspend terlebih dahulu, kecuali pelanggaran levelnya sudah kategori berat (misal kebocoran data sensitif skala besar) yang memerlukan eskalasi segera.

**Approval Workflow**: suspend akses dapat dieksekusi langsung oleh IT/HR Ops untuk kasus jelas; eskalasi ke linkage klausul PKS memerlukan approval Legal & Account Manager.
- **Skenario Setuju Linkage Klausul** — Legal & Account Manager mengonfirmasi pelanggaran cukup serius/berulang untuk dikaitkan ke klausul PKS; tindakan kontraktual lebih lanjut (mis. peringatan formal ke client, atau dasar pertimbangan non-renewal) berjalan mengikuti domain PKS.
- **Skenario Tolak Linkage (Cukup Peringatan/Suspend)** — Legal/Account Manager menilai pelanggaran belum cukup serius untuk dikaitkan ke klausul kontraktual; kasus ditutup pada tahap peringatan/suspend saja tanpa eskalasi lebih lanjut, namun tetap tercatat dalam riwayat pelanggaran untuk akumulasi skor kesehatan akun.
- **Skenario Pemulihan Akses Pasca-Suspend** — client yang di-suspend mengajukan pemulihan setelah menunjukkan tindakan korektif (mis. mengganti PIC yang bermasalah, memperbaiki praktik keamanan kredensial); IT/HR Ops memulihkan akses setelah konfirmasi Account Manager bahwa risiko sudah dimitigasi — bukan otomatis pulih hanya karena waktu suspend berlalu.
- **Skenario Eksepsi (Kebocoran Data Skala Besar)** — pelanggaran kategori berat terdeteksi; proses melompat langsung ke eskalasi manajemen & linkage klausul PKS tanpa melalui tahap peringatan bertahap, sejalan dengan business rule di atas, dengan notifikasi darurat ke Legal & Direksi.

**Notifikasi/trigger**: notifikasi ke client (level manajemen, bukan hanya PIC yang bersangkutan) saat eskalasi mencapai tahap 3.

**Dokumen/output**: log pelanggaran & tindakan yang diambil per tahap eskalasi.

**Post-Cycle**: riwayat pelanggaran akses menjadi bagian pertimbangan skor kesehatan akun (bagian 10.3) dan bahan evaluasi renewal PKS jika berulang.

---

## 17. Sub-Siklus: Unified Client Approval Queue

Melengkapi berbagai titik endorsement/approval client yang tersebar (endorsement absensi/lembur bagian 3, approval Sales/Service Order bernilai tinggi domain Payroll & Billing, *acknowledgment* BAST domain Payroll & Billing, approval timesheet mingguan bagian 10.1) dengan satu tampilan konsolidasi.

**Pre-Cycle**: prasyarat mutlak bagi sub-siklus ini adalah seluruh titik approval individual yang hendak dikonsolidasi (endorsement absensi/lembur bagian 3, approval SO domain Payroll & Billing, *acknowledgment* BAST domain Payroll & Billing, approval timesheet mobile bagian 10.1) sudah terdefinisi dan berjalan sebagai proses tersendiri di domain masing-masing — Unified Client Approval Queue tidak dapat dibangun lebih dulu dari proses-proses individual tersebut, karena sub-siklus ini murni lapisan agregasi, bukan pemilik proses approval baru.

**Life Cycle**:
- **Proses bisnis**: PIC client melihat seluruh item yang butuh tindakan approve/reject lintas kategori dalam satu antrian — setara Unified Dashboard/Inbox yang didefinisikan untuk karyawan pada domain ESS, tapi dari sisi client. Setiap item di antrian menampilkan konteks ringkas (jenis approval, nominal/dampak jika relevan, SLA tersisa) sebelum PIC client membuka detail lengkap.
- **Business rule**: PIC client dengan multi-peran (mis. mengelola lebih dari satu site) melihat antrian gabungan dengan filter per site/PKS sesuai Struktur Akses & Role-Based Permission (bagian 2.2).

**Business rule & validasi**: tujuan utama konsolidasi ini adalah mengurangi risiko SLA breach akibat item approval "tersembunyi" di menu yang jarang dibuka — desain antrian tunggal ini bukan sekadar kenyamanan visual, melainkan mitigasi risiko operasional.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Item Approval | String | APR-Q-2026-08170012 | ID unik di lapisan antrian, bukan pengganti ID transaksi di domain asal |
| Kategori | Enum | Endorsement Absensi, Approval SO, BAST Acknowledgment, Approval Timesheet | Menentukan domain asal & tampilan detail |
| Domain & ID Transaksi Asal | String | Time Management / ATT-2026-08170088 | Dipakai untuk menyegarkan status sebelum aksi dieksekusi |
| Nominal/Dampak | Numerik | Rp 4.500.000 | Ditampilkan jika kategori terkait nilai finansial (mis. approval SO) |
| SLA Tersisa | Durasi | 1 hari 4 jam | Dihitung mengikuti SLA domain asal |
| Status | Enum | Menunggu, Disetujui, Ditolak, Kedaluwarsa/Usang | "Kedaluwarsa/Usang" dipakai saat status domain asal berubah sebelum PIC client bertindak |

**Dokumen/output**: tampilan antrian approval konsolidasi per PIC client.

**Integrasi antar tahap**: bagian ini adalah lapisan presentasi yang mengagregasi transaksi dari domain lain (Time Management, Payroll & Billing) — tidak mendefinisikan proses approval baru, hanya mengonsolidasikan titik akses ke proses yang sudah ada. **Jalur jika status transaksi di domain asal berubah setelah tampil di antrian** (mis. transaksi sudah diproses lewat kanal lain sebelum PIC client sempat membuka antrian): antrian wajib menyegarkan status sebelum aksi approve/reject dieksekusi, bukan memproses berdasarkan status yang sudah usang saat item pertama kali dimuat — mencegah PIC client tidak sengaja menyetujui/menolak transaksi yang sudah tidak relevan.

**Post-Cycle**: begitu suatu item disetujui/ditolak lewat antrian konsolidasi, hasilnya mengalir balik sebagai status final ke domain asal masing-masing (Time Management, Payroll & Billing) — antrian tidak menyimpan keputusan akhir secara terpisah dari domain asal. Riwayat aksi lewat antrian tercatat dalam audit trail (bagian 9), dan kecepatan respons PIC client terhadap item di antrian menjadi salah satu masukan skor kesehatan akun (bagian 10.3).

---

## 18. Ringkasan Ketentuan Kunci

- Segregasi data multi-client bersifat mutlak — client A tidak pernah dapat melihat data client B, diterapkan konsisten di seluruh lapisan (query, API, ekspor), bukan hanya filter tampilan.
- Endorsement PIC client bersifat operasional, bukan pengganti approval administratif HR — HR internal tetap dapat menolak transaksi yang sudah diendorse client, dan keputusan akhir selalu di HR internal.
- Perusahaan outsourcing tetap berkedudukan sebagai pengendali data (data controller); client hanya penerima data terbatas untuk kepentingan operasional yang sah, tunduk pada klasifikasi field yang ditetapkan (bukan didefinisikan ulang) oleh domain Employee Data Governance.
- Suspend akses portal berjalan otomatis saat PKS berakhir/diputus atau saat terdeteksi penyalahgunaan — kedua trigger memakai mekanisme teknis sama tapi proses pemulihannya berbeda; eskalasi penyalahgunaan bersifat progresif (peringatan → suspend → eskalasi manajemen → linkage klausul PKS) kecuali kategori pelanggaran berat.
- Pendaftaran PIC dengan kewenangan aksi sensitif (approve invoice, amandemen PKS) memerlukan verifikasi kewenangan legal tingkat tinggi terpisah dari akses operasional harian biasa.
- Pengajuan kebutuhan tenaga baru dari client tetap wajib melalui Approval MPR internal — portal hanya kanal intake, bukan jalur pintas yang melewati kontrol budget/sourcing.
- Buy-out/direct-hire memerlukan persetujuan individual karyawan yang bersangkutan — tidak dapat dipaksakan meski internal (Account Manager & Legal) sudah menyetujui prinsipnya; karyawan yang menjalani buy-out eligible rehire secara default.

---

## 19. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Monitoring Kehadiran | Endorsement & approval | Review dashboard, endorsement PIC client, approval final HR | PIC client, HR internal | Endorsement client → approval HR sesuai matrix | Klausul endorsement operasional | TBD |
| Replacement & Komplain | Pengajuan via portal | Replacement request, komplain formal sebagai tiket | PIC client, Reliever & Buffer/Discipline | Tidak ada approval tambahan sisi client | Klausul SLA respons komplain | TBD |
| Akses Read-Only Dokumen | Akses baca kontrak/billing/laporan visit | Lihat status PKS, unduh invoice/bukti pajak, lihat ringkasan visit | PIC client | Tidak ada approval (akses baca-saja) | Klausul akses dokumen client | TBD |
| Kepatuhan Data Pribadi | Penerapan klasifikasi field & lokasi data | Pembatasan tampilan field sensitif, penetapan data residency | Employee Data Governance, IT | Tidak ada approval (mengikuti kebijakan baku) | Klausul kerahasiaan data pribadi | TBD |
| Notifikasi Proaktif | Pengiriman alert & reminder | Reminder PKS H-60/H-30/H-14, alert SLA, ringkasan Requisition Fulfillment | Sistem, Account Manager | Tidak ada approval (notifikasi otomatis) | Klausul reminder PKS | TBD |
| Self-Service Reporting | Generate laporan mandiri | Export laporan kehadiran/billing format PDF/Excel | PIC client | Tidak ada approval (self-service) | Tidak berlaku langsung | TBD |
| Audit Trail & Keamanan Akses | Pencatatan aktivitas & suspend otomatis | Log aktivitas user, suspend akses saat PKS berakhir/diputus | Sistem, IT/HR Ops | Tidak ada approval untuk pencatatan; approval Legal & Account Manager untuk investigasi lanjutan | Klausul kerahasiaan & keamanan akses | TBD |
| Onboarding Akses Client | Provisioning & verifikasi | Verifikasi kewenangan, aktivasi akun, training | Account Manager, IT, PIC client baru | Approval Account Manager & Legal untuk kewenangan sensitif | Klausul onboarding operasional | TBD |
| Tiering & Account Management | Klasifikasi & service level | Penetapan tier, kadens engagement, skor kesehatan akun | Account Manager, Sales/BD | Konfirmasi Account Manager | Klausul tingkat layanan (implisit) | TBD |
| Eskalasi Formal | Eskalasi komplain tidak terselesaikan | Pencatatan status, rujuk ke amandemen PKS/PHI | Account Manager, Legal | Approval Legal untuk jalur hukum | Klausul dispute resolution | TBD |
| Client-Initiated Request | Pengajuan kebutuhan tenaga baru | Intake via portal, Approval MPR internal | PIC client, Talent Acquisition | Approval MPR (internal, bukan bypass) | Klausul ekspansi/SO | TBD |
| Buy-Out/Direct-Hire | Pengajuan & penyelesaian | Pengajuan, review klausul buy-out, approval, offboarding kategori khusus | PIC client, Account Manager, Legal, karyawan | Approval Account Manager & Legal, eskalasi Finance jika nominal signifikan | Klausul buy-out PKS | TBD |
| Penyalahgunaan Akses | Eskalasi berjenjang | Peringatan, suspend, eskalasi manajemen, linkage klausul PKS | IT/HR Ops, Account Manager, Legal | Approval Legal & Account Manager untuk linkage klausul | Klausul pelanggaran akses | TBD |
| Unified Approval Queue | Konsolidasi approval | Tampilan antrian lintas kategori | PIC client | Mengikuti approval masing-masing kategori asal | Tidak berlaku langsung | TBD |
