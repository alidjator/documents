# Reliever & Buffer Management — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Reliever & Buffer Management mengelola tenaga cadangan yang menjaga kelangsungan operasional lapangan tanpa mengorbankan pemenuhan SLA yang tercantum di PKS. Domain ini mencakup: perbedaan konseptual reliever (reaktif) vs buffer (proaktif/standby), perencanaan kebutuhan buffer, status kepegawaian khusus, pool management, siklus assignment reliever (termasuk durasi/multi-assignment dan eskalasi saat pool kosong), payroll lintas-site, monitoring biaya & utilisasi, transisi reliever ke status tetap, serta siklus terpisah untuk perubahan headcount terencana (ramp-up/ramp-down) yang berbeda karakter dari penggantian ad-hoc.

---

## 2. Konsep & Perbedaan Reliever vs Buffer

- **Reliever** — tenaga cadangan **reaktif**, dikerahkan untuk menutup ketidakhadiran mendadak (sakit, cuti darurat, hasil proses Discipline yang memerlukan penggantian sementara).
- **Buffer** — tenaga cadangan **proaktif/standby**, disiapkan di muka untuk memenuhi klausul SLA response time yang dinegosiasikan di PKS, tanpa menunggu kejadian ketidakhadiran aktual terjadi lebih dulu.

Perbedaan ini menentukan basis perhitungan kebutuhan (bagian 3) dan pemicu assignment (bagian 6) — reliever dipicu kejadian, buffer dipicu kebutuhan kesiapsiagaan yang sudah direncanakan.

---

## 3. Sub-Siklus: Perencanaan Kebutuhan Buffer

**Pre-Cycle**: data historis tersedia sebagai basis kalkulasi — tingkat *absenteeism* historis per site, tingkat turnover, kompleksitas operasional site (jumlah shift, risk level).

**Life Cycle**:
- **Actor**: Manpower Planning (pemilik proses perencanaan), Account Manager (validasi terhadap klausul SLA PKS).
- **Proses bisnis**: rasio buffer terhadap headcount aktif dihitung per client/site berdasarkan data historis di atas, dikonfirmasi terhadap SLA response time yang dijanjikan di PKS (kepemilikan proses domain PKS, bagian Kerangka SLA) — site dengan SLA lebih ketat memerlukan rasio buffer lebih tinggi.
- **Data/field**: rasio buffer per site, absenteeism rate historis, turnover rate historis, klausul SLA response time PKS terkait.
- **Business rule & validasi**: rasio buffer yang diusulkan tidak boleh lebih rendah dari ambang minimal yang tersirat oleh klausul SLA PKS — jika perhitungan historis menghasilkan rasio di bawah ambang tersebut, perencanaan wajib menyesuaikan ke atas meski secara statistik historis terlihat cukup.
- **Approval Workflow**: rencana rasio buffer per site/client memerlukan konfirmasi Account Manager (validasi kesesuaian SLA) sebelum ditetapkan sebagai baseline operasional.
  - **Skenario Setuju** — Account Manager mengonfirmasi rasio yang diusulkan sudah memadai terhadap klausul SLA PKS; baseline berlaku efektif.
  - **Skenario Tolak** — Account Manager menilai rasio yang diusulkan jauh di bawah kebutuhan riil klausul SLA tanpa dasar perhitungan yang memadai (mis. data historis yang dipakai tidak representatif/terlalu lama); pengajuan ditolak dan Manpower Planning wajib menyusun ulang perhitungan dari data yang lebih relevan sebelum diajukan kembali.
  - **Skenario Perlu Revisi** — perhitungan pada dasarnya valid tapi belum memperhitungkan perubahan spesifik yang diketahui (mis. SLA terbaru hasil amandemen PKS belum tercermin); Manpower Planning cukup menyesuaikan rasio berdasarkan perubahan tersebut tanpa mengulang seluruh perhitungan historis dari awal.

**Notifikasi/trigger**: notifikasi ke HR Ops/Site Manager saat rasio buffer baru ditetapkan untuk suatu site.

**Dokumen/output**: baseline rasio buffer per site/client, menjadi rujukan bagi Pool Management (bagian 5) dalam menentukan ukuran pool yang perlu disiapkan.

**Post-Cycle**: baseline direview berkala (lihat Reporting & Feedback Loop, bagian 8.2) berdasarkan data utilisasi aktual, bukan ditetapkan sekali dan dibiarkan statis.

---

## 4. Status Kepegawaian Reliever/Buffer

Reliever/buffer memakai status kontrak khusus (PKWT penempatan fleksibel atau harian lepas), berbeda dari status kontrak karyawan penempatan reguler. Status ini berimplikasi pada:
- **Masa kerja**: dihitung terpisah dari masa kerja karyawan reguler, mengikuti pola kontrak yang berlaku untuk kategori ini.
- **BPJS**: kepesertaan tetap wajib didaftarkan mengikuti ketentuan yang berlaku untuk kategori kontrak ini (kepemilikan proses domain Payroll untuk mekanisme pendaftaran).
- **Hak-hak lain**: mengikuti ketentuan spesifik kategori PKWT fleksibel/harian lepas sesuai peraturan ketenagakerjaan yang berlaku (Undang-Undang Ketenagakerjaan berikut perubahannya, dan Peraturan Pemerintah Nomor 35 Tahun 2021 untuk kategori dan syarat PKWT) — berbeda dari hak PKWT/PKWTT penempatan reguler yang dibahas pada domain Kontrak Kerja.

**Pre-Cycle**: kategori status kontrak khusus ini ditetapkan sejak kandidat direkrut ke Pool Management (bagian 5) — kandidat diinformasikan sejak awal implikasinya terhadap perhitungan masa kerja dan mekanisme pendaftaran BPJS, agar tidak menimbulkan sengketa persepsi hak di kemudian hari.

**Life Cycle**:
- **Actor**: HR Ops (administrasi status kontrak), Payroll (pendaftaran/pembaruan BPJS).
- **Proses bisnis**: selama status reliever/buffer aktif, masa kerja terhitung berdasarkan akumulasi periode kontrak yang berlaku untuk kategori ini (bukan otomatis berkelanjutan seperti karyawan penempatan reguler), sementara kepesertaan BPJS didaftarkan/diperbarui mengikuti setiap perubahan status kontrak atau perpindahan wilayah upah minimum akibat multi-assignment (bagian 6.3).
- **Business rule & validasi**: frekuensi perpindahan site/wilayah pada reliever multi-assignment berisiko menimbulkan gap administrasi kepesertaan BPJS jika pembaruan tidak dilakukan tepat waktu setiap kali terjadi perpindahan — proses administrasi wajib memastikan kontinuitas kepesertaan tanpa terputus meski basis penempatan berpindah-pindah.

**Post-Cycle**: akumulasi masa kerja dan riwayat kepesertaan BPJS selama berstatus reliever/buffer menjadi data yang direkonsiliasi saat Transisi ke Status Tetap (bagian 9) — menjadi dasar penentuan apakah masa kerja dihitung berkelanjutan atau dihitung ulang dari tanggal konversi.

**Integrasi antar tahap**: klasifikasi status ini menjadi rujukan bagi perhitungan Payroll Reliever/Buffer (bagian 7) dan menjadi syarat kelayakan saat Transisi ke Status Tetap (bagian 9). **Jalur kegagalan**: jika perubahan klasifikasi status reliever/buffer belum tersinkron di sistem pada saat Payroll (bagian 7) menjalankan payroll run atau saat proses Transisi ke Status Tetap (bagian 9) sudah berjalan (mis. status administratif sudah berubah namun basis kalkulasi payroll masih merujuk status lama), risiko yang timbul adalah kesalahan basis perhitungan gaji/BPJS pada periode transisi — proses pemroses di kedua domain wajib memvalidasi status terkini sebelum kalkulasi, bukan mengandalkan data status yang berpotensi usang.

---

## 5. Sub-Siklus: Pool Management

**Pre-Cycle**: kandidat/karyawan yang bersedia menjadi reliever/buffer sudah terekrut dan lolos syarat dasar (kepemilikan proses rekrutmen).

**Life Cycle**:
- **Actor**: HR Ops regional (pengelola pool), sistem (pencatatan status ketersediaan real-time).
- **Proses bisnis**: setiap reliever/buffer dicatat dalam database pool per wilayah/region, dilengkapi *skill matrix* (kualifikasi, sertifikasi, pengalaman per jenis site/pekerjaan) dan status ketersediaan real-time (tersedia/sedang assigned/tidak tersedia).
- **Data/field**: ID reliever, wilayah/region, skill matrix, status ketersediaan, riwayat assignment (rujukan ke bagian 6.2).
- **Business rule & validasi**: status ketersediaan wajib ter-update real-time begitu terjadi assignment (bagian 6) atau berakhirnya assignment — pool yang datanya stale (menampilkan tersedia padahal sedang assigned) berisiko menyebabkan double-booking.

**Approval Workflow**: tidak memerlukan approval untuk pencatatan rutin; penghapusan reliever dari pool (karena resign/tidak aktif berkepanjangan) mengikuti proses offboarding standar.

**Notifikasi/trigger**: notifikasi ke HR Ops regional saat ukuran pool suatu wilayah turun di bawah baseline yang ditetapkan perencanaan (bagian 3).

**Dokumen/output**: database pool ter-update, skill matrix per reliever.

**Pertimbangan Non-Fungsional**: status ketersediaan pool wajib ter-update secara real-time (bukan batch berkala) mengingat assignment reliever bersifat mendesak — keterlambatan sinkronisasi status berisiko langsung menyebabkan double-booking pada saat kejadian darurat, saat toleransi keterlambatan sistem paling rendah.

**Post-Cycle**: data pool historis (tingkat perputaran, rata-rata masa aktif reliever dalam pool) menjadi input evaluasi perencanaan kebutuhan buffer periode berikutnya (bagian 8.2).

---

## 6. Siklus Utama: Trigger & Proses Assignment Reliever

### 6.1 Pre-Cycle

Kejadian pemicu teridentifikasi: ketidakhadiran mendadak (sakit, cuti darurat), atau hasil proses Employee Discipline yang memerlukan penggantian sementara di suatu site.

### 6.2 Life Cycle

**Actor**: Site Supervisor/Site Manager (pemohon), HR Ops regional (matching), sistem (pencatatan SLA).

**Proses bisnis**:
1. Site Supervisor melaporkan kebutuhan penggantian mendadak.
2. Sistem/HR Ops regional mencari kandidat dari pool (bagian 5), melalui sub-langkah:
   - **2a. Pencocokan skill matrix** — sistem menyaring kandidat pool yang memenuhi kualifikasi/sertifikasi yang disyaratkan site tujuan.
   - **2b. Penyaringan status ketersediaan** — dari hasil 2a, hanya kandidat berstatus "tersedia" real-time yang dipertimbangkan.
   - **2c. Prioritisasi geografis** — dari kandidat yang lolos 2a-2b, dipilih yang secara geografis terdekat untuk meminimalkan waktu tempuh; jika tidak ada kandidat yang lolos seluruh sub-langkah ini, proses bercabang ke Eskalasi Pool Kosong (bagian 6.4).
3. Assignment diajukan untuk approval sesuai Garis Komando:
   - **Skenario Setuju (Rutin)** — untuk assignment dalam ambang biaya normal, Site Manager menyetujui; reliever langsung diberangkatkan ke site tujuan.
   - **Skenario Perlu Revisi** — data pendukung pengajuan belum lengkap (mis. justifikasi durasi ketidakhadiran yang digantikan belum dicantumkan) meski kandidat reliever dan kecocokan skill sudah tepat; Site Manager meminta kelengkapan data ke HR Ops regional tanpa mengulang pencarian kandidat dari pool — assignment lanjut diproses begitu data dilengkapi, berbeda dari Skenario Tolak yang mengharuskan pencarian kandidat alternatif.
   - **Skenario Perlu Eskalasi Finansial** — assignment melibatkan biaya tambahan signifikan (mis. tunjangan transport lintas-region) atau lintas-region; diajukan ke matrix approval finansial sebelum diberangkatkan, dengan SLA approval dipercepat mengingat sifat mendesak kebutuhan.
   - **Skenario Tolak** — Site Manager/approver finansial menilai assignment yang diajukan tidak sesuai (mis. skill tidak benar-benar cocok meski lolos filter otomatis, atau biaya tidak dapat dijustifikasi); permintaan dikembalikan ke HR Ops regional untuk mencari kandidat alternatif dari pool, bukan otomatis bercabang ke eskalasi pool kosong selama kandidat lain masih tersedia.
4. Reliever yang di-assign melapor ke site tujuan sesuai SLA response time yang berlaku (mengikuti Kerangka SLA pada domain PKS — SLA Response Time Replacement).

**Data/field**: ID kejadian pemicu, site asal kebutuhan, reliever yang di-assign, waktu trigger, waktu reliever tiba di site, SLA target vs aktual.

**Business rule & validasi**: waktu tempuh sejak trigger sampai reliever tiba di site diukur terhadap SLA Response Time Replacement yang berlaku untuk PKS/site tersebut — pelanggaran SLA pada titik ini menjadi input langsung ke mekanisme penalti pada domain PKS.

**Approval Workflow (Kewenangan Override Darurat)**: mengikuti matrix Garis Komando; assignment darurat dapat memakai kewenangan override Site Manager untuk kasus mendesak, dengan validasi/dokumentasi retroaktif dalam batas waktu tertentu setelah assignment berjalan.
- **Skenario Override Disetujui Retroaktif** — dokumentasi yang diajukan setelah assignment berjalan sesuai dengan kondisi darurat yang diklaim; approval finansial mengonfirmasi retroaktif tanpa mengubah status assignment yang sudah berjalan.
- **Skenario Perlu Klarifikasi/Revisi Retroaktif** — dokumentasi yang diajukan belum cukup lengkap untuk dinilai (mis. bukti pendukung kondisi darurat belum disertakan), bukan berarti override otomatis dianggap tidak sah; Site Manager diberi batas waktu tambahan untuk melengkapi dokumentasi sebelum keputusan retroaktif final (Disetujui/Ditolak) ditetapkan, tanpa mengubah status assignment yang sudah berjalan selama masa klarifikasi ini.
- **Skenario Override Ditolak Retroaktif** — dokumentasi/justifikasi darurat yang diajukan setelah fakta dinilai tidak memadai (mis. kondisi sebenarnya tidak cukup mendesak untuk melewati approval normal); assignment yang sudah berjalan tetap dilanjutkan (tidak ditarik kembali demi kontinuitas operasional site), namun dicatat sebagai pelanggaran prosedur dan menjadi bahan evaluasi kewenangan override Site Manager tersebut ke depannya.

**Notifikasi/trigger**: notifikasi ke reliever terpilih, ke Site Supervisor tujuan, dan ke PIC client (jika PKS mensyaratkan notifikasi client atas penggantian tenaga).

**Dokumen/output**: catatan assignment (reliever, site, durasi, SLA aktual).

**Integrasi antar tahap**: keterlambatan penerbitan dokumen atau perlengkapan terkait assignment (bagian 6.3 di bawah) dapat memperlambat capaian SLA meski reliever secara fisik sudah siap. **Jalur jika reliever yang sudah di-assign ternyata tidak dapat berangkat** (mis. berhalangan mendadak setelah dikonfirmasi): kejadian ini diperlakukan sebagai trigger baru pada 6.2 langkah 1 untuk site yang sama — bukan dianggap SLA breach dari assignment pertama selama re-assignment berikutnya masih dalam total SLA response time yang berlaku, namun jika total waktu tempuh gabungan melewati SLA, tetap tercatat sebagai breach terhadap trigger asli.

**Post-Cycle (Siklus Utama)**: setelah satu assignment reliever selesai (reliever kembali ke status tersedia di pool atau langsung berlanjut ke assignment berikutnya), dilakukan evaluasi ringkas — capaian SLA aktual vs target (dibandingkan terhadap data pada bagian ini), kesesuaian skill matrix dengan kebutuhan riil di lapangan (validasi pasca-penempatan, melengkapi kecocokan otomatis saat matching pada langkah 2), dan pembaruan status ketersediaan reliever di Pool Management (bagian 5). Hasil evaluasi ini menjadi input bagi: riwayat multi-assignment (bagian 6.3) untuk pertimbangan assignment berikutnya, indikator kelayakan Transisi ke Status Tetap (bagian 9) jika pola penempatan di site yang sama berulang secara konsisten, dan Reporting & Feedback Loop (bagian 8.2) sebagai data mentah frekuensi/durasi penggunaan reliever per site.

### 6.3 Sub-Cycle: Durasi & Multi-Assignment

Reliever dapat berpindah-pindah site dalam satu bulan; riwayat penempatan sementara dicatat terpisah dari riwayat mutasi permanen (kepemilikan struktur data domain Employee Data Governance).

**Pre-Cycle**: assignment baru diterima sementara reliever masih terkait assignment site sebelumnya (jika belum berakhir) atau baru saja berakhir.

**Life Cycle**: setiap perpindahan site berpotensi memicu *reissue* ID card/seragam sesuai client/site tujuan (kepemilikan proses domain Employee Equipment & ID Card Distribution) — proses reissue ini sendiri punya SLA yang harus cukup cepat agar tidak menjadi bottleneck baru bagi SLA response time assignment reliever secara keseluruhan (bagian 6.2).

**Business rule**: SLA reissue perlengkapan untuk reliever multi-assignment wajib lebih ketat dari SLA reissue reguler (karyawan mutasi permanen), mengingat sifat mendesak assignment reliever.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Reliever | String (alfanumerik) | REL-00123 | Rujukan ke ID pada Pool Management (bagian 5) |
| ID Assignment Sebelumnya | String (alfanumerik) | ASG-00045 | Rujukan ke assignment site sebelumnya |
| ID Assignment Baru | String (alfanumerik) | ASG-00046 | Assignment site tujuan saat ini |
| Tanggal Mulai Assignment Baru | Tanggal (DD-MM-YYYY) | 03-08-2026 | Tanggal reliever mulai bertugas di site baru |
| Status Reissue ID Card/Seragam | Enum | Diproses / Selesai / Terlambat | Status proses reissue perlengkapan |
| SLA Reissue Target (jam) | Numerik | 24 | Batas waktu maksimal reissue sesuai business rule di atas |
| SLA Reissue Aktual (jam) | Numerik | 30 | Waktu aktual penyelesaian reissue |

**Integrasi antar tahap**: proses reissue ID card/seragam terintegrasi dengan domain Employee Equipment & ID Card Distribution sebagai pemilik proses reissue itu sendiri, dan dengan SLA Response Time Replacement (bagian 6.2) sebagai batas toleransi keseluruhan. **Jalur kegagalan**: jika reissue benar-benar terlambat melewati SLA reissue yang ditetapkan sementara reliever sudah tiba dan bertugas di site tujuan, reliever untuk sementara tetap bekerja menggunakan ID card/seragam site sebelumnya sampai reissue tuntas — dicatat sebagai pelanggaran SLA reissue internal (terpisah dari SLA Response Time Replacement yang sudah tercapai di bagian 6.2), dan berisiko menimbulkan kendala akses fisik/verifikasi identitas di site client baru apabila client menerapkan kontrol akses ketat berbasis ID card.

**Post-Cycle**: riwayat multi-assignment terakumulasi sebagai data pengalaman/eksposur reliever ke berbagai site — relevan sebagai pertimbangan Transisi ke Status Tetap (bagian 9) dan sebagai bahan evaluasi kelayakan versi skill matrix reliever tersebut (bagian 5).

### 6.4 Sub-Cycle: Eskalasi jika Buffer/Reliever Tidak Cukup

**Pre-Cycle**: pencarian pada pool site/region terdekat (bagian 6.2, langkah 2) tidak menghasilkan kandidat yang tersedia.

**Life Cycle**:
- **Actor**: HR Ops regional, HR Pusat (eskalasi), Account Manager (komunikasi risiko ke client).
- **Proses bisnis**: sistem mengeskalasi pencarian ke HR Pusat untuk opsi *cross-region reliever* (menarik dari pool wilayah lain, dengan konsekuensi waktu tempuh lebih lama); jika tetap tidak ditemukan kandidat dalam waktu wajar, sistem memicu notifikasi risiko SLA breach ke Account Manager untuk dikomunikasikan proaktif ke client sebelum breach benar-benar terjadi.
- **Business rule**: notifikasi risiko proaktif ke client (sebelum breach terjadi) diperlakukan berbeda dari notifikasi breach yang sudah terjadi pada mekanisme penalti SLA (domain PKS) — komunikasi proaktif berpotensi meredam dampak relasional meski secara kontraktual penalti tetap dapat berlaku jika breach akhirnya terjadi.

**Approval Workflow**: eskalasi cross-region tidak memerlukan approval finansial tambahan kecuali menimbulkan biaya perjalanan/akomodasi signifikan, yang mengikuti matrix approval finansial standar.
- **Skenario Setuju** — approval finansial (jika diperlukan) disetujui dalam SLA yang dipercepat; reliever cross-region diberangkatkan.
- **Skenario Perlu Revisi (Opsi Cross-Region Alternatif)** — approver finansial menilai opsi cross-region yang diajukan (wilayah asal reliever tertentu) bukan pilihan paling efisien biaya dibanding opsi wilayah lain yang juga memiliki kandidat tersedia; HR Ops regional diminta mengajukan ulang dengan opsi wilayah asal yang berbeda tanpa mengulang seluruh proses eskalasi dari awal.
- **Skenario Tolak** — approver finansial menilai biaya perjalanan/akomodasi tidak proporsional terhadap nilai kontrak/risiko penalti yang dihindari; kasus ini langsung diteruskan sebagai notifikasi risiko SLA breach ke Account Manager tanpa menunggu opsi cross-region lebih lanjut.
- **Skenario Tidak Ditemukan Kandidat Sama Sekali (Eksepsi)** — baik pool lokal maupun cross-region tidak menghasilkan kandidat; kasus dieskalasi ke HR Pusat & Direksi sebagai isu kapasitas struktural (bukan sekadar isu assignment satu kejadian), dengan Account Manager mengomunikasikan risiko breach ke client secara proaktif sesuai business rule di atas.

**Notifikasi/trigger**: notifikasi eskalasi ke HR Pusat, notifikasi risiko ke Account Manager & client.

**Dokumen/output**: log eskalasi (alasan pool kosong, opsi yang dicoba, hasil akhir).

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Eskalasi | String (alfanumerik) | ESC-00012 | ID unik kejadian eskalasi pool kosong |
| Site/Wilayah Asal | String | Site Cikarang / Wilayah Jabodetabek | Lokasi kebutuhan awal |
| Wilayah Cross-Region Dicoba | String | Wilayah Jawa Tengah | Wilayah yang dicoba pada opsi cross-region |
| Status Pencarian | Enum | Ditemukan / Tidak Ditemukan | Hasil pencarian kandidat cross-region |
| Estimasi Biaya Travel/Akomodasi | Numerik (Rupiah) | 1.500.000 | Dasar keputusan Approval Workflow eskalasi |
| Waktu Eskalasi Dimulai | Tanggal-waktu | 17-08-2026 14:05 | Timestamp trigger eskalasi ke HR Pusat |

**Integrasi antar tahap**: hasil eskalasi (baik kandidat cross-region ditemukan maupun eksepsi tidak ditemukan sama sekali) menjadi input langsung ke mekanisme penalti SLA pada domain PKS, dan frekuensinya menjadi masukan Reporting & Feedback Loop (bagian 8.2) serta revisi baseline rasio buffer (bagian 3). **Jalur kegagalan**: jika notifikasi risiko SLA breach ke Account Manager terlambat disampaikan (mis. proses eskalasi internal ke HR Pusat berlarut-larut sebelum notifikasi ke Account Manager terkirim), client kehilangan kesempatan mitigasi proaktif dan breach yang akhirnya terjadi berisiko diperlakukan sebagai kegagalan ganda (operasional dan relasional) pada evaluasi domain PKS.

**Post-Cycle**: frekuensi eskalasi per site/region menjadi indikator kuat bahwa baseline rasio buffer (bagian 3) untuk wilayah tersebut perlu direvisi naik — masukan langsung ke Reporting & Feedback Loop (bagian 8.2).

### 6.5 Variasi: Assignment untuk Penempatan WFH/Remote

Konsep "assign pengganti fisik ke site" (bagian 6.2–6.3) tidak langsung berlaku untuk posisi remote. Penggantian tenaga WFH lebih berupa **reassignment akses/akun sistem client** (kepemilikan proses domain IT Asset & System Access Provisioning) ketimbang *reissue* ID card/seragam fisik — sehingga SLA response time-nya berpotensi lebih cepat (tidak ada kendala jarak/geografis), namun tetap tunduk pada klausul WFH yang berlaku di PKS terkait (kepemilikan proses domain PKS).

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Akun/Akses Sistem Client | String (alfanumerik) | ACC-00789 | Rujukan ke domain IT Asset & System Access Provisioning |
| Jenis Akses | Enum | VPN / Aplikasi Client / Email Dedicated | Jenis akses yang direassign |
| Status Revoke Akses Lama | Enum | Selesai / Dalam Proses | Status pencabutan akses pemegang sebelumnya |
| Status Provisioning Akses Baru | Enum | Selesai / Dalam Proses | Status pemberian akses ke reliever pengganti |
| SLA Response Time Provisioning | Numerik (jam) | 4 | Target waktu penggantian akses, lebih cepat dari SLA reissue fisik (bagian 6.3) |

---

## 7. Payroll Reliever/Buffer

Perhitungan gaji reliever/buffer memerlukan penanganan khusus dibanding karyawan reguler: basis hari kerja aktual per site yang bisa berbeda-beda dalam satu bulan (termasuk implikasi UMP/UMK berbeda antar-site/wilayah jika reliever berpindah wilayah upah minimum dalam periode yang sama), *split billing* per site/client sesuai proporsi hari kerja di masing-masing site, serta prorata & konsolidasi lintas site ke dalam satu slip gaji per periode. Elaborasi penuh mekanisme kalkulasi ini adalah kepemilikan proses domain Payroll & Billing (mengikuti prinsip Prorata Gaji dan Validasi UMP/UMK yang berlaku umum) — di sini ditegaskan bahwa data sumber prorata & split billing berasal dari riwayat multi-assignment (bagian 6.3), bukan dari catatan mutasi permanen.

**Pre-Cycle**: riwayat multi-assignment periode berjalan (bagian 6.3) sudah lengkap tercatat per reliever — tanggal mulai/akhir tiap penempatan site, dan wilayah upah minimum tiap site — sebagai prasyarat sebelum payroll run dijalankan; data yang belum lengkap pada titik ini berisiko menghasilkan split billing/prorata yang tidak akurat.

**Life Cycle**:
- **Actor**: Payroll & Billing (kalkulasi & eksekusi), Finance (validasi billing per client), HR Ops regional (sumber data riwayat multi-assignment).
- **Proses bisnis**: sistem menghitung proporsi hari kerja aktual reliever per site dalam satu periode payroll, menerapkan basis UMP/UMK site yang berlaku pada tanggal kerja bersangkutan (bukan basis tunggal), memecah *split billing* ke masing-masing client/site sesuai proporsi tersebut, lalu mengonsolidasikan seluruh site ke dalam satu slip gaji per reliever untuk periode itu.
- **Business rule & validasi**: jika reliever berpindah wilayah upah minimum dalam satu periode payroll yang sama, komponen gaji wajib dihitung terpisah per segmen wilayah/tanggal berlaku (bukan dirata-ratakan), dan split billing ke tiap client wajib mencerminkan proporsi hari kerja riil di site tersebut — split yang tidak proporsional berisiko menimbulkan dispute billing dengan client pada domain Payroll & Billing.

**Post-Cycle**: hasil split billing & prorata lintas site menjadi salah satu komponen data biaya buffer yang dipakai pada Cost & Utilization Monitoring (bagian 8.1) dan Reporting & Feedback Loop (bagian 8.2), untuk dibandingkan terhadap revenue billing yang relevan.

---

## 8. Monitoring, Utilisasi & Pelaporan

### 8.1 Sub-Siklus: Cost & Utilization Monitoring

**Pre-Cycle**: data assignment (bagian 6.2) dan status pool (bagian 5) tersedia sebagai sumber data.

**Life Cycle**:
- **Actor**: Manpower Planning/Finance (evaluator), sistem (agregasi data).
- **Proses bisnis**: dashboard menampilkan tingkat utilisasi buffer (idle vs assigned) per site/region secara berkala, dibandingkan dengan biaya mempertahankan buffer idle vs manfaat SLA yang terjaga (dihindarinya penalti breach pada domain PKS).
- **Business rule & validasi**: evaluasi rasio buffer ideal dilakukan berkala (mis. kuartalan), membandingkan biaya idle terhadap frekuensi assignment aktual dan frekuensi eskalasi (bagian 6.4) — rasio yang menghasilkan idle tinggi tanpa mengurangi frekuensi eskalasi mengindikasikan alokasi buffer yang tidak tepat sasaran (bukan sekadar kurang/lebih secara jumlah).

**Approval Workflow**: sub-siklus ini bersifat monitoring/pelaporan (dashboard evaluatif) dan tidak menghasilkan keputusan transaksional yang langsung mengubah status assignment/anggaran — karenanya **tidak memerlukan approval formal**. Keputusan tindak lanjut atas hasil evaluasi (mis. revisi baseline rasio buffer) baru tunduk pada Approval Workflow di titik prosesnya sendiri (bagian 3, konfirmasi Account Manager).

**Dokumen/output**: dashboard utilisasi & biaya buffer per site/region/client.

**Post-Cycle**: hasil evaluasi menjadi salah satu input revisi baseline rasio buffer (bagian 3).

### 8.2 Sub-Siklus: Reporting & Feedback Loop ke Manpower Planning

**Pre-Cycle**: data bahan laporan sudah tersedia — hasil Cost & Utilization Monitoring (bagian 8.1), log frekuensi eskalasi pool kosong (bagian 6.4), riwayat multi-assignment (bagian 6.3), dan baseline rasio buffer berjalan (bagian 3) — dengan periode pelaporan yang sudah ditetapkan (mis. bulanan/kuartalan) mengikuti kalender evaluasi Manpower Planning.

**Life Cycle**:
- **Actor**: Manpower Planning (penerima & pengolah feedback untuk perencanaan periode berikutnya), HR Ops regional (penyedia data operasional pool & eskalasi), Finance (data biaya buffer vs revenue billing).
- **Proses bisnis**: laporan frekuensi & durasi penggunaan reliever per site/client disusun, dibandingkan terhadap biaya buffer vs revenue billing yang relevan (kepemilikan data finansial domain Payroll & Billing), lalu dikonsolidasikan sebagai umpan balik ke proses Manpower Planning periode berikutnya — mencakup pula sinyal dari frekuensi eskalasi (bagian 6.4) dan pola multi-assignment (bagian 6.3) sebagai indikator kebutuhan revisi ukuran pool per wilayah.
- **Business rule & validasi**: laporan wajib disusun berbasis data satu periode penuh (bukan snapshot sesaat), agar sinyal revisi baseline mencerminkan pola berulang, bukan anomali satu kejadian yang tidak representatif.

**Notifikasi/trigger**: notifikasi ke Manpower Planning saat laporan periode siap direview.

**Dokumen/output**: laporan Reporting & Feedback Loop periode berjalan (frekuensi/durasi assignment, biaya vs revenue, sinyal eskalasi & multi-assignment).

**Post-Cycle**: siklus perencanaan (bagian 3) dimulai ulang dengan baseline yang sudah disesuaikan berdasarkan laporan ini — menutup satu putaran penuh perencanaan-eksekusi-evaluasi.

---

## 9. Sub-Siklus: Transisi Reliever ke Status Tetap

**Pre-Cycle**: pola assignment seorang reliever menunjukkan penempatan konsisten dan berulang di satu site tertentu dalam jangka waktu yang mengindikasikan kebutuhan permanen, bukan lagi sekadar cover ad-hoc.

**Life Cycle**:
- **Actor**: Site Manager (pengusul), HR Ops (proses konversi), reliever yang bersangkutan (pihak yang menyetujui).
- **Proses bisnis**:
  1. Site Manager mengajukan usulan konversi status.
  2. HR Ops mengevaluasi kelayakan, melalui sub-langkah:
     - **2a. Peninjauan riwayat kinerja** — HR Ops memeriksa riwayat kinerja reliever yang bersangkutan (hasil evaluasi kinerja periode berjalan, termasuk catatan proses Discipline jika ada) untuk memastikan memenuhi standar konversi.
     - **2b. Peninjauan riwayat multi-assignment** — riwayat penempatan reliever (bagian 6.3) ditelaah sebagai indikator pengalaman dan konsistensi penempatan di site yang diusulkan.
     - **2c. Pengecekan ketersediaan slot headcount** — dikonfirmasi ke Manpower Planning apakah slot headcount reguler tersedia di site tersebut; jika salah satu dari ketiga sub-langkah ini tidak terpenuhi, proses bercabang ke Skenario Tolak atau Skenario Perlu Revisi pada Approval Workflow di bawah.
  3. Jika seluruh sub-langkah 2a-2c terpenuhi, status kontrak dikonversi dari PKWT fleksibel/harian lepas ke status penempatan reguler sesuai taksonomi kontrak kerja yang berlaku.
- **Business rule & validasi**: konversi status memicu perhitungan ulang masa kerja untuk keperluan hak-hak yang terkait masa kerja — apakah masa kerja sebagai reliever diperhitungkan (berkelanjutan) atau dihitung ulang dari tanggal konversi, ditetapkan sebagai kebijakan baku dan didokumentasikan eksplisit saat konversi terjadi.
- **Approval Workflow**: mengikuti matrix Garis Komando kategori administratif HR; memerlukan konfirmasi ketersediaan anggaran headcount reguler dari Manpower Planning sebelum konversi final disetujui.
  - **Skenario Setuju** — HR Ops mengonfirmasi kelayakan dan Manpower Planning mengonfirmasi ketersediaan slot anggaran; konversi status berjalan efektif sesuai tanggal yang ditetapkan.
  - **Skenario Tolak** — riwayat kinerja tidak memenuhi standar konversi, atau tidak ada slot headcount reguler tersedia di site tersebut; reliever tetap berstatus pool dengan pemberitahuan alasan, dapat diajukan ulang pada evaluasi periode berikutnya.
  - **Skenario Perlu Revisi (Slot Tersedia di Site Lain)** — kelayakan individu terpenuhi tapi slot headcount reguler tidak tersedia di site saat ini; usulan direvisi menjadi konversi dengan penempatan di site lain yang memiliki slot terbuka (jika reliever bersedia), alih-alih ditolak sepenuhnya.

**Notifikasi/trigger**: notifikasi ke Payroll untuk migrasi skema perhitungan gaji dari basis harian/fleksibel ke basis reguler.

**Dokumen/output**: dokumen konversi status kontrak, pembaruan status di Pool Management (reliever yang dikonversi keluar dari pool aktif).

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Reliever | String (alfanumerik) | REL-00123 | Rujukan ke Pool Management (bagian 5) |
| Tanggal Usulan Konversi | Tanggal (DD-MM-YYYY) | 10-08-2026 | Tanggal Site Manager mengajukan usulan |
| Site Tujuan Konversi | String | Site Bekasi | Site tempat reliever diusulkan menetap |
| Skor Riwayat Kinerja | Numerik (skala 1-5) | 4.2 | Hasil sub-langkah 2a |
| Jumlah Riwayat Multi-Assignment | Numerik | 6 site dalam 12 bulan | Hasil sub-langkah 2b |
| Ketersediaan Slot Headcount | Enum | Tersedia / Tidak Tersedia | Hasil sub-langkah 2c |
| Tanggal Efektif Konversi | Tanggal (DD-MM-YYYY) | 01-09-2026 | Menjadi acuan cut-off migrasi skema payroll |
| Status Kontrak Baru | Enum | PKWT Reguler / PKWTT | Sesuai taksonomi domain Kontrak Kerja |

**Integrasi antar tahap**: migrasi skema perhitungan gaji dari basis harian/fleksibel (bagian 7) ke basis reguler terintegrasi dengan domain Payroll & Billing sebagai pemilik proses kalkulasi, dan dengan pembaruan status di Pool Management (bagian 5) sebagai pemilik data ketersediaan. **Jalur kegagalan**: jika notifikasi migrasi skema payroll belum sempat diproses oleh domain Payroll pada payroll run terdekat (mis. tanggal efektif konversi jatuh di tengah periode payroll yang sudah dikunci), risiko yang timbul adalah pemrosesan ganda (sebagian periode masih dihitung dengan skema lama, sebagian belum tersinkron ke skema baru) atau delay pembayaran sebagian komponen gaji sampai periode payroll berikutnya — dokumen konversi wajib mencantumkan tanggal efektif yang jelas agar Payroll dapat menentukan cut-off periode secara tepat.

**Post-Cycle**: slot yang ditinggalkan reliever tersebut di pool (bagian 5) menjadi kebutuhan baru bagi Pool Management untuk direkrut kembali agar ukuran pool wilayah tetap sesuai baseline.

---

## 10. Siklus Terpisah: Ramp-up/Ramp-down (Scaling Terencana)

Berbeda dari Reliever/Buffer (bagian 2-9) yang menangani penggantian **ad-hoc** untuk mempertahankan headcount yang sudah disepakati, Ramp-up/Ramp-down menangani perubahan **terencana** naik/turunnya total headcount suatu site/client.

### 10.1 Pre-Cycle

Pemicu ramp-up/ramp-down teridentifikasi dari salah satu dari empat sumber: amandemen PKS yang menambah/mengurangi lingkup kerja (kepemilikan proses domain PKS), fluktuasi musiman yang terprediksi (kepemilikan proses Manpower Planning), onboarding client baru yang memerlukan pemenuhan headcount awal masif (kepemilikan proses domain PKS, bagian Client Operational Onboarding), atau akhir proyek pada Project-Based Deployment (kepemilikan proses domain Project-Based Deployment).

### 10.2 Life Cycle — Ramp-up

**Actor**: Talent Acquisition (mass recruitment), Manpower Planning (koordinasi target), HR Ops regional (penarikan dari pool buffer sebagai percepatan awal).

**Proses bisnis**: mass recruitment sprint dijalankan dengan target waktu ketat (kepemilikan proses domain Job Portal); sebagai percepatan awal sebelum rekrutmen baru rampung, pool Reliever/Buffer existing (bagian 5) dapat ditarik sementara untuk mengisi kebutuhan awal, dengan status assignment tercatat sebagai bagian riwayat multi-assignment (bagian 6.3), bukan penempatan permanen otomatis.

**Business rule**: penarikan dari pool buffer untuk keperluan ramp-up tidak boleh menurunkan rasio buffer wilayah asal di bawah baseline (bagian 3) tanpa mitigasi — penarikan besar-besaran memerlukan koordinasi eksplisit dengan Manpower Planning wilayah asal.

**Approval Workflow**: rencana ramp-up (penambahan headcount, baik melalui mass recruitment maupun penarikan sementara dari pool buffer) memerlukan konfirmasi ketersediaan anggaran headcount tambahan dari Finance/Manpower Planning sebelum dieksekusi, mengikuti matrix Garis Komando kategori keputusan anggaran.
- **Skenario Setuju** — Finance/Manpower Planning mengonfirmasi anggaran headcount tambahan tersedia sesuai target ramp-up; mass recruitment sprint dan/atau penarikan pool buffer dijalankan sesuai rencana.
- **Skenario Tolak** — anggaran tidak tersedia, atau dasar/target ramp-up dinilai belum solid (mis. amandemen PKS yang mendasarinya belum final); usulan ramp-up ditunda sampai dasar/anggaran jelas.
- **Skenario Perlu Revisi (Skala Bertahap)** — anggaran tersedia namun tidak mencukupi untuk seluruh target sekaligus; rencana direvisi menjadi bertahap (mis. kebutuhan mendesak dipenuhi lebih dulu dari pool buffer, sisanya menyusul dari rekrutmen baru pada anggaran periode berikutnya) alih-alih ditolak seluruhnya.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Rencana Ramp-up | String (alfanumerik) | RMP-00034 | ID unik rencana penambahan headcount |
| Site/Client Tujuan | String | Client XYZ - Site Surabaya | Lokasi penambahan headcount |
| Target Headcount Tambahan | Numerik | 50 | Jumlah posisi yang perlu dipenuhi |
| Jumlah Dipenuhi dari Pool Buffer | Numerik | 8 | Percepatan awal dari pool existing (bagian 5) |
| Jumlah dari Rekrutmen Baru | Numerik | 42 | Sisa dipenuhi mass recruitment sprint |
| Timeline Target Pemenuhan | Tanggal (DD-MM-YYYY) | 30-09-2026 | Batas waktu pemenuhan penuh |
| Status Anggaran | Enum | Disetujui / Ditolak / Revisi Bertahap | Hasil Approval Workflow di atas |

**Integrasi antar tahap**: penarikan pool buffer untuk ramp-up terintegrasi dengan Pool Management (bagian 5) dan tercatat sebagai bagian riwayat multi-assignment (bagian 6.3); mass recruitment sprint terintegrasi dengan domain Job Portal. **Jalur kegagalan**: jika penarikan pool buffer untuk ramp-up tidak segera tercatat sebagai riwayat multi-assignment (bagian 6.3) di sistem, reliever yang ditarik berisiko masih tampil berstatus "tersedia" di pool wilayah asal dan ter-assign ganda (double-booking) untuk kebutuhan darurat lain di wilayah tersebut.

### 10.3 Life Cycle — Ramp-down

**Actor**: HR Ops, Site Manager site asal & site tujuan (jika ada redeployment).

**Proses bisnis**: opsi **redeployment** ke site/client lain dalam grup diprioritaskan sebelum opsi offboarding; jika redeployment melibatkan perubahan entitas pemberi kerja (bukan hanya site dalam entitas yang sama), proses mengikuti Sub-rogasi/Pengalihan Tenaga Kerja (kepemilikan proses domain PKS). Jika tidak ada site tujuan yang tersedia, karyawan masuk proses offboarding dengan kategori PHK karena alasan pengurangan pekerjaan sesuai Peraturan Pemerintah Nomor 35 Tahun 2021.

**Business rule**: keputusan ramp-down wajib mengevaluasi opsi redeployment ke seluruh site dalam grup (bukan hanya site dalam client yang sama) sebelum opsi offboarding dipertimbangkan — offboarding karena ramp-down yang sebetulnya masih punya opsi redeployment yang belum dieksplorasi berisiko menimbulkan dispute ketenagakerjaan.

**Approval Workflow**: mengikuti matrix Garis Komando kategori administratif HR untuk redeployment; kategori PHK karena pengurangan pekerjaan memerlukan approval berjenjang sampai HR Manager/Legal sesuai matrix keputusan berdampak tinggi.
- **Skenario Setuju Redeployment** — site tujuan tersedia dan disetujui Site Manager tujuan; karyawan dipindah tanpa melalui proses PHK.
- **Skenario Setuju PHK (Redeployment Tidak Tersedia)** — HR Manager/Legal mengonfirmasi seluruh opsi redeployment dalam grup sudah dieksplorasi tanpa hasil; PHK dengan kategori pengurangan pekerjaan disetujui sesuai ketentuan kompensasi PP 35/2021.
- **Skenario Tolak Usulan Offboarding (Redeployment Belum Dieksplorasi Penuh)** — Legal/HR Manager menilai opsi redeployment belum benar-benar dieksplorasi seluruh site dalam grup sesuai business rule di atas; usulan offboarding dikembalikan untuk pengecekan opsi redeployment tambahan sebelum diajukan ulang — mencegah risiko dispute ketenagakerjaan karena opsi yang terlewat.
- **Skenario Perlu Revisi (Sebagian Redeployment, Sebagian Offboarding)** — dari sekelompok karyawan yang terdampak ramp-down, sebagian punya opsi redeployment dan sebagian tidak; usulan dipecah menjadi dua jalur paralel per individu, bukan diputuskan sebagai satu keputusan kolektif seragam.

**Dokumen/output**: rencana ramp-up/ramp-down per site (target headcount, timeline, sumber pemenuhan), dokumen redeployment atau dokumen offboarding sesuai hasil keputusan.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Rencana Ramp-down | String (alfanumerik) | RMD-00021 | ID unik rencana pengurangan headcount |
| Site Asal | String | Site Cikarang | Lokasi pengurangan headcount |
| Jumlah Headcount Dikurangi | Numerik | 15 | Jumlah posisi terdampak |
| Opsi Redeployment Dieksplorasi | Enum | Ya / Tidak | Wajib "Ya" sebelum offboarding dapat disetujui |
| Site Tujuan Redeployment | String | Site Tangerang (client lain, entitas sama) | Kosong jika tidak ada opsi tersedia |
| Kategori Keputusan | Enum | Redeployment / PHK Pengurangan Pekerjaan | Hasil Approval Workflow di atas |
| Tanggal Efektif | Tanggal (DD-MM-YYYY) | 15-09-2026 | Tanggal keputusan berlaku |

**Integrasi antar tahap**: opsi redeployment lintas entitas terintegrasi dengan domain PKS (Sub-rogasi/Pengalihan Tenaga Kerja), dan hasil akhir ramp-down memperbarui baseline headcount yang menjadi rujukan Pool Management (bagian 5) dan baseline rasio buffer (bagian 3). **Jalur kegagalan**: jika proses redeployment lintas entitas belum tuntas secara administratif (dokumen sub-rogasi belum berlaku efektif) sementara karyawan sudah mulai bekerja di site/entitas tujuan, timbul periode ambigu status kepesertaan BPJS dan payroll — karyawan wajib tetap tercatat pada entitas asal sampai dokumen sub-rogasi resmi berlaku efektif, untuk menghindari kekosongan status kepesertaan.

**Post-Cycle**: hasil ramp-up/ramp-down memperbarui baseline headcount aktif site yang bersangkutan, menjadi rujukan baru bagi perhitungan rasio buffer periode berikutnya (bagian 3).

### 10.4 Sub-Siklus: Mobilization/Demobilization Tracking Dashboard (Khusus Project-Based Deployment)

Melengkapi ramp-up/ramp-down generik di atas dengan tracking spesifik untuk Project-Based Deployment, karena karakternya berbeda dari ramp-up/ramp-down site reguler — terikat milestone/SOW, bukan headcount berkelanjutan.

**Mobilization (Pre-Cycle proyek)**: tracking status kesiapan staffing sebelum tanggal mulai proyek — *skill-matching* selesai, provisioning IT/akses klien siap (kepemilikan proses domain IT Asset & System Access), training teknis proyek tuntas (kepemilikan proses domain Training), kontrak/SOW individu terbit (kepemilikan proses domain Kontrak Kerja) — dengan status agregat "X dari Y posisi proyek siap mobilize" sebagai *early-warning* risiko proyek mulai terlambat karena staffing belum lengkap.

**Demobilization (Post-Cycle proyek)**: tracking status *wind-down* mendekati/setelah tanggal akhir proyek — knowledge transfer selesai (kepemilikan proses domain Project-Based Deployment), *deliverable acceptance*/*sign-off* (kepemilikan proses domain Project-Based Deployment), status transisi tiap anggota tim (redeploy ke proyek lain/placement reguler/offboarding, mengikuti pola bagian 10.3).

**Business rule**: kedua dashboard ini terpisah dari Requisition Fulfillment Dashboard (kepemilikan proses domain Talent Acquisition) karena levelnya bukan pengisian posisi kosong satu-per-satu, melainkan koordinasi timeline kesiapan/wind-down kolektif satu tim proyek sebagai satu kesatuan.

**Actor**: Project Manager, Talent Acquisition, HR Ops.

**Notifikasi/trigger**: notifikasi eskalasi ke Project Manager & Manpower Planning saat status agregat mobilisasi menunjukkan risiko keterlambatan mendekati tanggal mulai proyek.

**Dokumen/output**: dashboard status mobilisasi/demobilisasi per proyek.

---

## 11. Ringkasan Ketentuan Kunci

- Reliever (reaktif, dipicu kejadian) dan Buffer (proaktif/standby, dipicu kebutuhan kesiapsiagaan) adalah dua konsep berbeda yang menentukan basis perhitungan kebutuhan dan pemicu assignment secara terpisah.
- Rasio buffer per site tidak boleh lebih rendah dari ambang minimal yang tersirat klausul SLA PKS, terlepas hasil perhitungan historis — dan wajib dikonfirmasi Account Manager sebelum berlaku sebagai baseline.
- Assignment reliever mengikuti urutan matching (skill matrix → ketersediaan → prioritas geografis) sebelum diajukan approval; kewenangan override darurat oleh Site Manager tetap memerlukan validasi/dokumentasi retroaktif, dan override yang ditolak retroaktif tidak membatalkan assignment yang sudah berjalan, hanya dicatat sebagai pelanggaran prosedur.
- Eskalasi pool kosong berjenjang: cross-region terlebih dahulu, baru notifikasi risiko proaktif ke client sebelum SLA breach benar-benar terjadi — bukan menunggu breach terjadi untuk memberi tahu client.
- Transisi reliever ke status tetap memerlukan konfirmasi ganda: kelayakan individu (HR Ops) dan ketersediaan slot anggaran headcount reguler (Manpower Planning) — kelayakan tanpa slot tidak otomatis ditolak, dapat dialihkan ke site lain yang punya slot terbuka.
- Ramp-down wajib mengevaluasi opsi redeployment ke seluruh site dalam grup sebelum offboarding dipertimbangkan; usulan offboarding yang belum menuntaskan eksplorasi redeployment wajib ditolak/dikembalikan, bukan langsung diproses sebagai PHK.

---

## 12. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Perencanaan Buffer | Penetapan rasio | Kalkulasi historis, validasi terhadap SLA PKS | Manpower Planning, Account Manager | Konfirmasi Account Manager | Klausul SLA response time | TBD |
| Pool Management | Pemeliharaan database | Pencatatan skill matrix & status ketersediaan | HR Ops regional, sistem | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Assignment Reliever | Trigger & matching | Pelaporan kebutuhan, pencarian pool, assignment | Site Supervisor, HR Ops regional | Matrix Garis Komando (rutin: Site Manager) | Klausul SLA response time replacement | TBD |
| Assignment Reliever | Multi-assignment & reissue | Perpindahan site, reissue ID card/seragam | HR Ops, tim equipment | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Assignment Reliever | Eskalasi pool kosong | Cross-region search, notifikasi risiko SLA breach | HR Ops regional, HR Pusat, Account Manager | Approval finansial jika ada biaya perjalanan | Klausul SLA & penalti | TBD |
| Monitoring & Pelaporan | Evaluasi utilisasi & biaya | Dashboard idle vs assigned, evaluasi rasio ideal | Manpower Planning, Finance | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Transisi Status | Konversi reliever ke tetap | Usulan, evaluasi kelayakan, konversi kontrak | Site Manager, HR Ops | Matrix Garis Komando administratif HR | Tidak berlaku langsung | TBD |
| Ramp-up/Ramp-down | Scaling terencana | Mass recruitment / redeployment / offboarding | Talent Acquisition, HR Ops, Site Manager | Matrix Garis Komando sesuai kategori keputusan | Klausul amandemen PKS/onboarding client | TBD |
| Mobilization Tracking | Kesiapan staffing proyek | Skill-matching, provisioning, training, kontrak SOW | Project Manager, Talent Acquisition, HR Ops | Eskalasi ke Manpower Planning jika berisiko telat | Klausul SOW proyek | TBD |
