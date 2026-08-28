# Employee Data Governance — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Employee Data Governance mendefinisikan setiap karyawan sebagai satu entitas data (*employee identity*) yang konsisten dan dapat ditelusuri sepanjang seluruh siklus hidupnya — mulai dari kandidat, menjadi karyawan aktif, sampai alumni, termasuk saat berpindah client, legal entity, atau vendor dalam grup perusahaan. Domain ini berlaku untuk dua populasi yang cakupannya perlu dibedakan secara eksplisit di setiap bagian: **karyawan penempatan** (ditempatkan di site/client melalui skema outsourcing) dan **karyawan internal** (back-office/korporat). Perbedaan cakupan per populasi diuraikan secara konsolidasi pada bagian 10.

Ruang lingkup domain ini mencakup: struktur & kategori data master karyawan, pembentukan identitas unik (Employee ID), kontinuitas identitas lintas status (kandidat–karyawan–alumni–rehire), governance perubahan data pribadi, kontinuitas identitas saat pengalihan vendor/legal entity, kontrol akses data di kanal client, pengelolaan dokumen identitas, serta kerangka governance data (kepemilikan, kualitas, retensi, dan konsistensi lintas sistem). Domain ini **tidak** mencakup mekanisme Tanggal Efektif (perubahan future-dated/retroaktif) dan penentuan Status PTKP secara rinci — keduanya dibahas tuntas di domain Payroll dan pada proses mutasi organisasi; di sini keduanya hanya dirujuk sebagai titik integrasi.

---

## 2. Struktur Master Data Karyawan

### 2.1 Definisi & Kategori Data

Master data karyawan dipisah menjadi dua kategori dengan karakteristik dan proses governance yang berbeda:

- **Data Identitas Inti** — NIK KTP, nama sesuai KTP, tempat/tanggal lahir, jenis kelamin, status kawin, NPWP, nomor kepesertaan BPJS Ketenagakerjaan, nomor kepesertaan BPJS Kesehatan, nomor rekening bank untuk payroll (termasuk nama bank dan nama pemilik rekening), kepemilikan smartphone pribadi (Ya/Tidak — basis kriteria deployment kiosk & delinasi kanal ESS bagi karyawan tanpa device pribadi, linkage Fondasi 5A poin 3, Multi-Channel Access), kontak darurat (nama, hubungan keluarga sesuai KK, nomor telepon — basis verifikasi proxy saat karyawan tidak dapat mengakses ESS mandiri karena sakit berat/rawat inap, linkage Fondasi 5A bagian 9a, Delegasi Akses Proxy). Bersifat legal/administratif, jarang berubah, dan wajib divalidasi terhadap dokumen sumber sebelum disimpan.
- **Data Organisasi/Penempatan** — posisi, grade, site (untuk karyawan penempatan) atau departemen/fungsi korporat (untuk karyawan internal), status kontrak (PKWT/PKWTT/PKHL/Kemitraan/Magang — taksonomi lengkap Tahap B, B.6 poin 10), status probation (khusus PKWTT), legal entity pemberi kerja. Bersifat dinamis, berubah mengikuti mutasi/penempatan, dan mengikuti mekanisme Tanggal Efektif yang dikelola oleh proses mutasi organisasi.

### 2.2 Pre-Cycle — Sebelum Data Dimasukkan ke Sistem

- **Prasyarat**: dokumen sumber Data Identitas Inti (KTP, KK, NPWP, buku rekening bank, kartu BPJS jika sudah ada dari pekerjaan sebelumnya) sudah diterima dan lolos verifikasi kelengkapan dokumen pada proses rekrutmen/onboarding.
- **Persiapan**: penentuan skema pengisian Data Organisasi berdasarkan populasi karyawan (site/client/PKS untuk penempatan, departemen/fungsi untuk internal) sebelum entri data dimulai, mengikuti hasil requisition/offer yang sudah disepakati.

### 2.3 Life Cycle — Proses Entri & Pemeliharaan Data

**Actor**: HR Ops (entri & validasi Data Identitas Inti), Site Manager/Department Head (konfirmasi Data Organisasi), Finance/Payroll (co-validasi data rekening bank).

**Proses bisnis**:
1. HR Ops menerima dokumen sumber dari proses onboarding dan melakukan entri Data Identitas Inti ke sistem.
2. Sistem melakukan validasi format per field (format NIK 16 digit, format NPWP, format nomor rekening sesuai daftar bank yang didukung).
3. HR Ops/Site Manager melakukan entri Data Organisasi sesuai hasil penempatan/penugasan yang sudah disepakati di proses rekrutmen.
4. Data yang sudah lengkap dan tervalidasi menjadi status **Aktif** dan dikonsumsi oleh seluruh modul hilir (payroll, client portal, ESS, dokumen penempatan).

**Data/field yang dibutuhkan** (level kolom):

| Field | Kategori | Wajib/Opsional | Tipe Data | Contoh Nilai | Validasi |
|---|---|---|---|---|---|
| NIK KTP | Identitas Inti | Wajib | String, 16 karakter numerik | `3175012501900002` | 16 digit numerik, unik lintas legal entity; 6 digit pertama harus cocok dengan tabel kode wilayah administratif yang valid |
| Nama sesuai KTP | Identitas Inti | Wajib | String, huruf kapital, tanpa gelar | `SITI RAHMAWATI` | Sesuai dokumen sumber; tidak boleh mengandung gelar akademik/keagamaan kecuali tercantum di KTP |
| Tempat/Tanggal Lahir | Identitas Inti | Wajib | String (tempat) + Date (format `YYYY-MM-DD`) | `Bandung, 1990-01-25` | Format tanggal valid; tanggal lahir menghasilkan usia minimal 18 tahun pada tanggal mulai kerja |
| Jenis Kelamin | Identitas Inti | Wajib | Enum | `L` atau `P` | Salah satu dari dua nilai baku |
| Status Kawin | Identitas Inti | Wajib | Enum | `TK`, `K`, `K/1`, `K/2`, `K/3` | Salah satu dari kategori baku; memengaruhi kategori PTKP |
| NPWP | Identitas Inti | Opsional (memengaruhi tarif PPh 21) | String numerik, 15-16 digit | `09.123.456.7-012.000` | Format sesuai standar NPWP; jika kosong, tarif PPh 21 non-NPWP berlaku |
| No. Kepesertaan BPJS TK | Identitas Inti | Wajib sebelum payroll pertama | String numerik | `01234567890` | Validasi format cabang terdaftar |
| No. Kepesertaan BPJS Kesehatan | Identitas Inti | Wajib sebelum payroll pertama | String numerik, 13 digit | `0001234567890` | — |
| No. Rekening Bank | Identitas Inti | Wajib | String numerik, panjang bervariasi per bank (10-16 digit) | `1234567890` (format BCA) | Validasi nama pemilik rekening = nama karyawan; kode bank harus terdaftar di tabel bank yang didukung sistem payroll |
| Kepemilikan Smartphone Pribadi | Identitas Inti | Wajib diisi saat onboarding | Boolean | `Ya` / `Tidak` | Basis kriteria deployment kiosk per site & delinasi kanal ESS (linkage Fondasi 5A poin 3) — diverifikasi ulang saat data quality berkala (bagian 9.2) untuk menangkap perubahan status kepemilikan device |
| Kontak Darurat — Nama | Identitas Inti | Wajib diisi saat onboarding | String | `Siti Aminah` | Basis verifikasi proxy (linkage Fondasi 5A bagian 9a, Delegasi Akses Proxy) |
| Kontak Darurat — Hubungan Keluarga | Identitas Inti | Wajib diisi saat onboarding | Enum | `Suami/Istri`, `Orang Tua`, `Anak`, `Saudara Kandung` | Divalidasi terhadap dokumen Kartu Keluarga saat proxy diaktifkan |
| Kontak Darurat — Nomor Telepon | Identitas Inti | Wajib diisi saat onboarding | String numerik | `081234567890` | Diverifikasi ulang saat data quality berkala (bagian 9.2), sama seperti field kontak lain |
| Posisi/Grade | Organisasi | Wajib | String/kode grade | `Security Officer, Grade 2` | Sesuai struktur organisasi yang berlaku |
| Site/Client/PKS (penempatan) atau Departemen/Fungsi (internal) | Organisasi | Wajib | Referensi ID (site/PKS) atau string (departemen) | `Site-JKT-014 / PKS-2026-0087` | Sesuai populasi karyawan |
| Status Kontrak | Organisasi | Wajib | Enum | `PKWT`, `PKWTT`, `PKHL`, `Kemitraan`, `Magang` | Taksonomi lengkap & kriteria pembeda tiap nilai dirujuk ke Tahap B, B.6 poin 10 (Taksonomi Status Hubungan Kerja E2E) — field ini hanya menyimpan nilai klasifikasinya, bukan mendefinisikan ulang |
| Status Probation | Organisasi | Kondisional (wajib diisi hanya jika Status Kontrak = `PKWTT`) | Enum | `Aktif`, `Selesai`, `Tidak Berlaku` | Berlaku eksklusif untuk PKWTT (PKWT/PKHL/Kemitraan/Magang otomatis `Tidak Berlaku` — PKWT secara hukum tidak diperkenankan mensyaratkan masa percobaan); durasi maksimal & kriteria keputusan akhir masa percobaan dirujuk ke Tahap B, B.6 poin 11 |
| Legal Entity | Organisasi | Wajib | Referensi ID legal entity | `PT Prima Buana Team` | Satu dari legal entity dalam holding — nilai field ini **terikat** pada Status Kontrak/populasi karyawan (lihat Business rule di bawah dan bagian 10), bukan pilihan bebas independen |
| Kategori Penempatan | Organisasi | Wajib untuk populasi Karyawan Penempatan; tidak berlaku untuk Karyawan Internal (lihat Business rule di bawah) | Enum | `SO`, `Non-SO (Reguler)` | Membedakan penempatan dalam cakupan komitmen headcount reguler PKS (`Non-SO (Reguler)`, mayoritas karyawan penempatan) vs penempatan tambahan yang berasal dari Sales/Service Order di luar komitmen PKS (`SO`) — field di-*set* sejak requisition (linkage Approval MPR, Tahap B B.1.A poin 9) dan mengalir ke seluruh titik hilir (Requisition Fulfillment Dashboard, Offer Letter, Dokumen Penempatan, Monitoring & Perpanjangan Kontrak Kerja, Konsolidasi Billing F.2); status berubah otomatis dari `SO` ke `Non-SO (Reguler)` jika SO diperpanjang berulang sampai eskalasi ke Amandemen PKS — kriteria lengkap dirujuk ke Tahap B (paragraf Employee Data & Organization) dan F.2 (bullet SR/SO), field ini hanya menyimpan nilai klasifikasinya, bukan mendefinisikan ulang |

**Business rule & validasi**: entri Data Identitas Inti wajib melalui pengecekan deduplikasi berbasis NIK KTP sebelum disimpan sebagai record baru (lihat bagian 3) untuk mencegah duplikasi kandidat/karyawan yang sebelumnya sudah pernah tercatat. Perubahan pada Data Organisasi mengikuti alur mutasi organisasi standar (bukan proses ini) dan menerapkan Tanggal Efektif sesuai kebijakan mutasi yang berlaku. **Struktur Legal Entity — Pemetaan ke Populasi Karyawan**: holding terdiri dari beberapa legal entity (PT), dengan pembagian peran struktural eksplisit — **1 PT bertindak sebagai induk perusahaan (holding) sekaligus rujukan tunggal untuk seluruh Karyawan Internal** (back-office/korporat, linkage bagian 10), sementara **seluruh PT operasional lainnya (di luar PT induk) berperan khusus sebagai *employer of record* untuk populasi Karyawan Penempatan** (tidak menaungi Karyawan Internal). Field Legal Entity karenanya bukan pilihan bebas: sistem memvalidasi konsistensi antara nilai Legal Entity dan populasi (Internal vs Penempatan, bagian 10) yang dipilih saat entri Data Organisasi — pemetaan silang yang tidak konsisten (mis. Karyawan Internal terdaftar di salah satu PT operasional) ditolak validasi. Karyawan Penempatan dapat dialokasikan ke PT operasional mana pun sesuai kebutuhan penempatan/PKS yang bersangkutan (bukan satu PT tunggal untuk seluruh populasi Penempatan) — daftar PT operasional yang berlaku dikelola sebagai data referensi legal entity (linkage **Garis Komando**, Fondasi 1 bagian 4/5), bukan daftar tetap yang di-hardcode di domain ini. **Cakupan populasi Kemitraan**: karena Kemitraan bukan hubungan kerja menurut UU Ketenagakerjaan (linkage Tahap B, B.6 poin 10), record populasi ini di Struktur Master Data Karyawan **dibatasi minimal untuk keperluan pelaporan** (Data Identitas Inti dasar + Status Kontrak = `Kemitraan`) — field/proses yang mengasumsikan hubungan kerja penuh (Approval Workflow onboarding standar pada bagian ini, Governance Perubahan Data Pribadi bagian 5 secara penuh, siklus Payroll/BPJS sebagai employer) **tidak berlaku** untuk populasi ini, mengikuti perjanjian kemitraan tersendiri di luar cakupan domain ini. **Cakupan populasi Kategori Penempatan**: field ini hanya relevan untuk populasi Karyawan Penempatan yang direkrut melalui siklus requisition/PKS standar (bagian 10) — tidak berlaku untuk Karyawan Internal (konsep Sales/Service Order melekat pada siklus PKS yang tidak berlaku untuk populasi ini) maupun untuk populasi Kemitraan (record dibatasi minimal sesuai catatan cakupan populasi Kemitraan di atas, di luar siklus requisition/MPR standar).

**Approval Workflow**: entri data awal (saat onboarding) tidak memerlukan approval terpisah — dianggap bagian dari proses onboarding yang sudah disetujui lewat offer/penempatan. Koreksi pada Data Identitas Inti pasca-entri awal mengikuti alur approval Governance Perubahan Data Pribadi (bagian 5).

**Notifikasi/trigger**: notifikasi otomatis ke Payroll saat Data Identitas Inti yang relevan untuk payroll (rekening bank, NPWP, status kawin) selesai divalidasi dan berstatus Aktif.

**Dokumen/output**: record master data karyawan berstatus Aktif, siap dikonsumsi modul hilir.

**Integrasi antar tahap**: Data Organisasi bersumber dari hasil requisition/offer (proses rekrutmen); Data Identitas Inti menjadi basis kredensial ESS (proses Digital Onboarding ke ESS); nomor rekening bank menjadi basis Payroll; NIK KTP menjadi kunci pencarian pada proses deduplikasi kandidat. Field Status Kontrak & Status Probation dikonsumsi oleh proses Kontrak Kerja (Tahap B, B.6) sebagai sumber kanonik status hubungan kerja saat itu — perubahan nilainya (konversi PKWT→PKWTT, keputusan akhir masa probation, dst.) dieksekusi oleh proses B.6 tersebut, bukan didefinisikan ulang di domain ini. Field Kategori Penempatan di-*set* sejak proses Approval MPR (Tahap B, B.1.A poin 9) — bukan dientri manual di domain ini — dan menjadi sumber kanonik yang dikonsumsi Requisition Fulfillment Dashboard, Offer Letter, Dokumen Penempatan, dan Monitoring & Perpanjangan Kontrak Kerja (Tahap B), serta Konsolidasi Billing (Tahap F, F.2); perubahan status dari `SO` ke `Non-SO (Reguler)` (eskalasi ke Amandemen PKS) dieksekusi oleh proses-proses tersebut, bukan didefinisikan ulang di domain ini. **Jalur jika data sumber belum lengkap**: jika hasil requisition/offer belum final saat entri Data Organisasi hendak dilakukan (mis. site penempatan final belum dikonfirmasi), record tetap dapat dibuat berstatus "Data Organisasi Sementara" dengan Data Identitas Inti tetap aktif untuk kebutuhan lain (mis. Document Vault) — bukan menahan seluruh record sampai seluruh data organisasi lengkap.

**Pertimbangan Non-Fungsional**: volume record aktif dapat mencapai puluhan ribu di seluruh site/legal entity sekaligus — pencarian & validasi deduplikasi (NIK KTP) wajib tetap responsif pada skala tersebut; Data Identitas Inti tergolong data pribadi sensitif dan wajib dienkripsi saat disimpan (*at rest*) dan saat dipertukarkan ke modul hilir (*in transit*), sejalan dengan kewajiban UU PDP.

### 2.4 Post-Cycle — Setelah Data Berstatus Aktif

Data yang sudah aktif masuk ke siklus pemeliharaan berkelanjutan: setiap perubahan Data Identitas Inti mengikuti Governance Perubahan Data Pribadi (bagian 5), setiap perubahan Data Organisasi mengikuti proses mutasi, dan seluruh data menjadi objek pemeriksaan kualitas berkala (bagian 9.2).

---

## 3. Pembentukan & Keunikan Nomor Induk Karyawan (Employee ID)

### 3.1 Pre-Cycle

- **Prasyarat**: skema penomoran Employee ID (format, apakah mengandung kode legal entity/site sebagai prefix) sudah ditetapkan sebagai kebijakan baku sebelum sistem menerima entri data baru.
- **Persiapan**: daftar Employee ID lama (termasuk milik alumni) harus tersedia sebagai basis pengecekan deduplikasi.

### 3.2 Life Cycle — Deduplication & Penerbitan ID

**Actor**: HR Ops (untuk entri karyawan baru), Sistem (proses otomatis deduplikasi), Recruiter/HR Ops B.1.A (untuk kandidat volume tinggi).

**Proses bisnis**:
1. Saat entri data baru (baik kandidat di tahap rekrutmen maupun karyawan baru di onboarding), sistem menjalankan pengecekan deduplikasi berbasis NIK KTP terhadap seluruh record yang sudah ada — termasuk record berstatus alumni.
2. Jika ditemukan kecocokan NIK KTP: sistem menandai sebagai **potensi duplikat** dan mengarahkan ke proses Kontinuitas Identitas Lintas Siklus (bagian 4) alih-alih membuat record baru.
3. Jika tidak ditemukan kecocokan: sistem menerbitkan Employee ID baru sesuai skema penomoran yang berlaku, unik lintas seluruh legal entity dalam holding (satu NIK KTP = satu Employee ID, meski karyawan pernah/sedang tercatat di lebih dari satu PT dalam grup).
4. Untuk kandidat volume tinggi yang mengisi data secara mandiri (misal via job portal), sistem juga menjalankan pengecekan fuzzy-match (kemiripan nama + nomor HP) untuk menangkap kasus data sedikit berbeda (typo nama, nomor HP baru) yang berpotensi lolos dari pengecekan NIK KTP eksak.

**Data/field**: NIK KTP (kunci utama pengecekan), nama, nomor HP (kunci pengecekan sekunder/fuzzy-match), riwayat status sebelumnya (jika ditemukan).

**Business rule & validasi**: keunikan Employee ID wajib dijaga lintas legal entity dalam satu holding; kasus mutasi antar-PT atau sub-rogasi/pengalihan vendor tidak menghasilkan Employee ID baru, melainkan employment record baru di bawah Employee ID yang sama (lihat bagian 6).

**Approval Workflow**: tidak memerlukan approval untuk penerbitan ID baru (kasus tanpa kecocokan) — bersifat otomatis oleh sistem. Untuk kasus potensi duplikat, HR Ops wajib mengambil satu dari tiga keputusan eksplisit:
- **Konfirmasi Duplikat (Setuju)** — HR Ops memverifikasi bahwa data yang cocok memang orang yang sama; record baru ditautkan ke Employee ID lama, proses berlanjut ke Kontinuitas Identitas Lintas Siklus (bagian 4).
- **Tolak sebagai Duplikat (Bukan Orang yang Sama)** — HR Ops menyimpulkan kecocokan NIK/nama/HP adalah kebetulan atau kesalahan input pada record lama; kasus ini dieskalasi ke HR Manager untuk investigasi lebih lanjut sebelum Employee ID baru dapat diterbitkan, mengingat NIK KTP seharusnya tidak mungkin identik untuk dua orang berbeda — indikasi kuat ada kesalahan entri di salah satu record.
- **Perlu Data Tambahan (Revisi)** — bukti yang tersedia belum cukup untuk memutuskan (mis. nama & HP mirip tapi NIK tidak identik, hasil fuzzy-match ambigu); HR Ops meminta dokumen tambahan dari kandidat/karyawan sebelum keputusan final diambil, status record ditahan sementara sebagai "Menunggu Verifikasi".

**Notifikasi/trigger**: notifikasi ke HR Ops saat sistem mendeteksi potensi duplikat, untuk verifikasi manual sebelum proses berlanjut.

**Dokumen/output**: Employee ID baru (jika bukan duplikat) atau tautan ke Employee ID lama beserta riwayat statusnya (jika duplikat/kontinuitas).

**Integrasi antar tahap**: hasil deduplikasi menjadi input langsung ke proses Kontinuitas Identitas Lintas Siklus (bagian 4); talent pool pada proses rekrutmen memakai mekanisme deduplikasi yang sama untuk mencegah record kandidat ganda. **Jalur jika status duplikat belum ter-sync ke sistem rekrutmen lain** (mis. HR Ops sudah mengonfirmasi suatu record sebagai duplikat/menautkannya ke Employee ID lama di sistem inti, namun sistem job portal/rekrutmen yang terpisah belum menerima pembaruan status tersebut): sistem rekrutmen yang belum ter-sync tetap memperlakukan kandidat sebagai record baru untuk sementara, sehingga berisiko memproses lamaran pada lowongan berbeda secara paralel dengan record yang seharusnya sudah tertaut; begitu sinkronisasi status duplikat diterima, kedua record digabungkan (merge) ke satu Employee ID yang sama dan seluruh aktivitas lamaran yang sempat berjalan terpisah pada sistem rekrutmen ditautkan ulang sebagai riwayat di bawah identitas yang sudah disatukan — bukan dihapus atau dianggap tidak sah.

### 3.3 Post-Cycle

Employee ID yang sudah diterbitkan menjadi kunci referensi permanen yang dipakai seluruh modul hilir sepanjang sisa siklus hidup karyawan tersebut, termasuk setelah menjadi alumni dan jika suatu saat di-rehire.

---

## 4. Kontinuitas Identitas Lintas Siklus (Candidate → Employee → Alumni → Rehire)

### 4.1 Prinsip Umum

Satu identitas yang sama harus tetap dikenali sistem meski kategorinya berpindah — kandidat yang gagal di satu lowongan lalu melamar lowongan lain memakai record yang sama (bukan duplikat); alumni yang di-rehire tetap terhubung ke identitas lamanya, bukan dianggap karyawan baru dari nol — namun riwayat per-periode-kerja (masa kerja pertama vs kedua) tetap dipisah untuk keperluan perhitungan pesangon/UPMK, kecuali diatur lain oleh kebijakan internal.

**Skenario: Kandidat Gagal di Satu Lowongan, Melamar Lowongan Lain**

- **Pre-Cycle**: kandidat yang sebelumnya berstatus "Gagal/Tidak Lolos" pada satu proses seleksi mengajukan lamaran baru untuk lowongan berbeda; pengecekan deduplikasi (bagian 3) mengidentifikasi kecocokan NIK KTP dengan record kandidat lama yang berstatus gagal tersebut.
- **Life Cycle**: sistem menautkan lamaran baru ke record kandidat yang sama alih-alih membuat record baru terpisah; status "Gagal" pada lowongan sebelumnya bersifat spesifik per-lowongan (tidak menjadi status permanen atas identitas kandidat) sehingga tidak menghalangi otomatis kelanjutan proses pada lowongan baru; riwayat hasil seleksi sebelumnya tetap dapat dilihat sebagai konteks pendukung bagi perekrut, dan proses seleksi untuk lowongan baru berjalan independen mengikuti alur rekrutmen standar.
- **Post-Cycle**: riwayat seluruh lamaran (baik gagal maupun berhasil) terkumpul di bawah satu identitas kandidat yang sama, menjadi basis riwayat pelamar yang dapat dirujuk pada proses seleksi berikutnya maupun analisis talent pool.

### 4.2 Sub-Siklus: Proses Rehire

Proses rehire memiliki siklusnya sendiri yang berbeda dari onboarding karyawan baru murni, karena melibatkan penyambungan ke riwayat lama.

**Pre-Cycle**:
- **Prasyarat**: kandidat yang melamar teridentifikasi sebagai alumni lewat pengecekan deduplikasi (bagian 3).
- **Pengecekan eligibility rehire**: status alumni saat resign/offboarding dulu (baik-baik/dengan catatan/blacklist) diperiksa sebagai syarat kelayakan sebelum proses rehire dilanjutkan.

**Life Cycle**:
1. HR Ops menautkan record baru ke Employee ID lama begitu status alumni & eligibility terkonfirmasi.
2. Data Identitas Inti lama ditarik ke record aktif (dengan opsi update jika ada perubahan, mengikuti Governance Perubahan Data Pribadi di bagian 5).
3. Dokumen lama yang masih valid (di Document Vault, bagian 8) tetap tertaut, dokumen yang sudah kedaluwarsa (misal SKCK) wajib diperbarui sebagai bagian syarat rehire.
4. Sistem membuka periode kerja baru (periode kerja ke-2, dst.) sebagai entitas riwayat terpisah dari periode kerja sebelumnya, sambil tetap berada di bawah Employee ID yang sama.

**Post-Cycle**:
- Basis hitung pesangon/UPMK untuk periode kerja baru dihitung dari tanggal mulai periode kerja saat ini, terpisah dari periode kerja sebelumnya, kecuali kebijakan internal menetapkan pengakuan masa kerja berkelanjutan.
- Histori kedua periode kerja tetap dapat ditelusuri dari satu Employee ID yang sama untuk kebutuhan pelaporan & audit.

**Actor**: HR Ops, HR Manager (approval eligibility rehire jika status alumni sebelumnya bukan "baik-baik").

**Data/field**: Employee ID lama (rujukan penautan), status alumni terakhir saat offboarding (baik-baik/dengan catatan/blacklist), alasan resign/PHK pada periode kerja sebelumnya, tanggal mulai periode kerja baru, nomor urut periode kerja (ke-2, ke-3, dst.), status dokumen lama di Document Vault (berlaku/kedaluwarsa), catatan keputusan eligibility rehire beserta approver.

**Business rule & validasi**: rehire untuk alumni dengan catatan (misal resign tanpa notice, atau riwayat pelanggaran disiplin) memerlukan approval tambahan dari HR Manager sebelum proses rehire dilanjutkan; alumni berstatus blacklist tidak dapat diproses rehire tanpa pencabutan status oleh pejabat berwenang.

**Approval Workflow**: rehire untuk alumni berstatus baik-baik — tidak perlu approval khusus di luar proses rekrutmen normal. Rehire untuk alumni dengan catatan — approval HR Manager wajib sebelum offer diterbitkan; SLA approval mengikuti SLA administratif HR standar (2x24 jam) dengan auto-escalate ke HR Director jika terlampaui.
- **Skenario Setuju** — HR Manager menilai catatan sebelumnya (mis. resign tanpa notice satu kali, tanpa pelanggaran berat) tidak menghalangi rehire; proses rehire dilanjutkan seperti biasa dengan catatan approval tersimpan di riwayat.
- **Skenario Tolak** — HR Manager menilai catatan sebelumnya cukup berat untuk menolak rehire (kategori alasan baku: pelanggaran disiplin berulang, resign mendadak yang merugikan operasional site, hasil investigasi Discipline yang belum tuntas); kandidat dinotifikasi penolakan tanpa detail investigasi internal, proses rekrutmen untuk kandidat ini dihentikan.
- **Skenario Perlu Klarifikasi (Revisi)** — catatan yang tercatat ambigu atau tidak lengkap (mis. alasan resign tidak terdokumentasi jelas di riwayat offboarding); HR Manager meminta HR Ops menelusuri riwayat lebih lanjut (wawancara mantan atasan, cek dokumen offboarding) sebelum keputusan final, dengan tenggat tambahan di luar SLA 2x24 jam standar.
- **Eskalasi Blacklist** — jika status alumni adalah blacklist, keputusan tidak berhenti di HR Manager; wajib melalui proses pencabutan status oleh pejabat berwenang (HR Director) terlebih dahulu sebelum HR Manager dapat mempertimbangkan rehire sama sekali.

**Notifikasi/trigger**: notifikasi ke HR Manager saat sistem mendeteksi kandidat rehire dengan catatan/blacklist.

**Dokumen/output**: catatan keputusan eligibility rehire (disetujui/ditolak beserta alasan), record employment period baru yang tertaut ke Employee ID lama.

**Integrasi antar tahap**: eligibility rehire bersumber dari data offboarding (alasan resign/PHK, catatan disiplin) yang tercatat saat periode kerja sebelumnya berakhir. **Jalur jika data offboarding tidak ditemukan/tidak lengkap** (mis. periode kerja sebelumnya terjadi sebelum sistem ini berjalan, atau catatan hilang): status eligibility default ke "Perlu Klarifikasi Manual" (bukan otomatis "baik-baik" maupun otomatis ditolak) — HR Ops wajib menelusuri arsip fisik/wawancara sebelum keputusan eligibility diambil.

---

## 5. Governance Perubahan Data Pribadi

### 5.1 Pre-Cycle

- **Prasyarat**: karyawan mengajukan permohonan perubahan data (ganti nama, alamat domisili, tanggungan keluarga, nomor rekening bank) melalui ESS atau HR Ops, disertai dokumen pendukung sesuai jenis perubahan (dokumen pengadilan/KTP baru untuk ganti nama; Kartu Keluarga/akta untuk tanggungan; buku rekening baru untuk perubahan rekening).

### 5.2 Life Cycle

**Actor**: Karyawan (pemohon), HR Ops (validator), Finance/Payroll (co-validator khusus perubahan rekening bank karena dampak langsung ke pencairan gaji).

**Proses bisnis**:
1. Karyawan mengajukan perubahan beserta dokumen pendukung.
2. HR Ops memvalidasi dokumen pendukung terhadap jenis perubahan yang diajukan, melalui sub-langkah berikut:
   - **2a. Pre-check format otomatis** — sistem memeriksa jenis file yang didukung (PDF/JPG/PNG), ukuran maksimal, dan resolusi minimum untuk foto dokumen, sebelum diteruskan ke pemeriksa manusia.
   - **2b. Pemeriksaan kesesuaian data** — HR Ops membandingkan nama/data pada dokumen dengan data yang tercatat di sistem, memastikan keterbacaan dan keberadaan tanda tangan/stempel resmi (khusus dokumen pengadilan/instansi).
   - **2c. Pemeriksaan indikasi manipulasi** — untuk dokumen berupa hasil pindai/foto, HR Ops memeriksa indikasi manipulasi digital (area yang tampak diedit, ketidaksesuaian jenis huruf/font pada teks yang seharusnya seragam).
   - **2d. Keputusan** — hasil 2a-2c dikonsolidasikan menjadi satu keputusan: Setuju / Tolak (dengan kategori alasan) / Perlu Revisi (dengan catatan spesifik) — lihat detail percabangan pada Approval Workflow di bawah.
3. Untuk perubahan nomor rekening bank: Finance/Payroll melakukan validasi tambahan (kecocokan nama pemilik rekening dengan nama karyawan) sebelum perubahan diterapkan, untuk mencegah kesalahan transfer gaji.
4. Setelah tervalidasi, perubahan diterapkan dengan Tanggal Efektif yang sesuai (mengikuti pola Tanggal Efektif organisasi), namun dengan syarat validasi dokumen tambahan yang tidak berlaku untuk perubahan data organisasi biasa.
5. Seluruh perubahan tercatat sebagai audit trail: siapa mengubah, kapan, dokumen pendukung apa.

**Data/field**: jenis perubahan, nilai lama, nilai baru, dokumen pendukung (referensi ke Document Vault), tanggal pengajuan, tanggal efektif.

**Business rule & validasi**: perubahan data pribadi tanpa dokumen pendukung yang valid wajib ditolak; perubahan nomor rekening bank wajib melalui validasi ganda (HR Ops + Finance/Payroll) mengingat dampaknya langsung terhadap kewajiban pembayaran upah tepat waktu sebagaimana diatur dalam Undang-Undang Nomor 13 Tahun 2003 tentang Ketenagakerjaan sebagaimana telah diubah dengan Undang-Undang Nomor 6 Tahun 2023 tentang Penetapan Peraturan Pemerintah Pengganti Undang-Undang Nomor 2 Tahun 2022 tentang Cipta Kerja Menjadi Undang-Undang, serta Peraturan Pemerintah Nomor 36 Tahun 2021 tentang Pengupahan yang mengatur konsekuensi keterlambatan pembayaran upah.

**Approval Workflow**: perubahan data identitas inti (nama, alamat, tanggungan, rekening bank) memerlukan approval berjenjang: HR Ops sebagai validator pertama, dengan approval tambahan Finance/Payroll khusus untuk perubahan rekening bank. SLA validasi mengikuti SLA administratif HR standar (2x24 jam); jika terlampaui, auto-escalate ke HR Manager.
- **Skenario Setuju** — dokumen pendukung lolos validasi penuh (2a-2c); perubahan diterapkan dengan Tanggal Efektif sesuai kebijakan; karyawan dinotifikasi perubahan berhasil.
- **Skenario Tolak** — dokumen pendukung tidak valid (kategori alasan wajib dipilih dari daftar baku: dokumen tidak terbaca/buram, nama pada dokumen tidak cocok dengan data sistem, dokumen sudah kedaluwarsa, dokumen terindikasi tidak asli); karyawan dinotifikasi beserta alasan penolakan dan dapat mengajukan ulang dengan dokumen baru — tidak ada batas jumlah pengajuan ulang, namun setiap percobaan tercatat terpisah di audit trail.
- **Skenario Perlu Revisi** (berbeda dari Tolak) — dokumen pada dasarnya valid tapi ada ketidaksesuaian minor yang dapat diperbaiki tanpa mengulang dari awal (mis. buku rekening sudah benar tapi nama pemilik berbeda satu huruf karena kesalahan ketik saat entri, atau dokumen kurang satu halaman); HR Ops mengirim status "Perlu Revisi" dengan catatan spesifik apa yang harus dilengkapi; karyawan melengkapi kekurangan tanpa mengunggah ulang seluruh dokumen dari nol.
- **Skenario Tolak Otomatis oleh Finance/Payroll (khusus rekening bank)** — validasi ganda menemukan nama pemilik rekening tidak cocok dengan nama karyawan yang mengajukan (indikasi rekening milik pihak lain); ditolak otomatis tanpa opsi override manual, karyawan wajib mengajukan ulang dengan rekening atas nama sendiri.
- **Skenario Eksepsi (Force Majeure)** — untuk karyawan yang kehilangan dokumen fisik akibat kondisi force majeure (bencana, kebakaran) sehingga tidak dapat memenuhi syarat dokumen standar dalam waktu wajar: ditangani lewat jalur eksepsi manual berjenjang ke HR Manager, dengan dokumen pengganti sementara (surat keterangan kehilangan dari kepolisian/kelurahan) yang berlaku terbatas sampai dokumen asli/pengganti resmi diperoleh — bukan override sistem otomatis, tetap melalui keputusan manusia terdokumentasi.

**Notifikasi/trigger**: notifikasi ke karyawan saat pengajuan disetujui/ditolak; notifikasi ke Payroll saat perubahan rekening bank efektif, agar disbursement periode berikutnya memakai data terbaru.

**Dokumen/output**: catatan audit trail perubahan data (before/after value, approver, dokumen pendukung), data pribadi yang sudah diperbarui di master data.

**Integrasi antar tahap**: perubahan nomor rekening bank menjadi input validasi sebelum proses Payroll Disbursement berikutnya berjalan; perubahan tanggungan keluarga di luar konteks pajak menjadi rujukan bagi proses santunan yang dikelola domain hubungan dengan Serikat Pekerja & PKB. **Jalur jika perubahan disetujui setelah payroll run periode berjalan sudah dimulai**: perubahan tidak menyisip ke run yang sedang berjalan (mencegah inkonsistensi hitungan di tengah proses); perubahan berlaku efektif mulai run berikutnya, dengan status "Menunggu Payroll Run Berikutnya" ditampilkan ke karyawan agar tidak dikira gagal.

### 5.3 Post-Cycle

Perubahan yang sudah diterapkan menjadi bagian dari audit trail permanen dan menjadi basis rekonsiliasi konsistensi lintas sistem (bagian 9.4) — seluruh modul hilir yang mengonsumsi data yang berubah wajib ter-update konsisten, bukan sebagian saja.

---

## 6. Kontinuitas Identitas Saat Sub-rogasi/Pengalihan Vendor

### 6.1 Pre-Cycle

- **Prasyarat**: keputusan sub-rogasi/pengalihan tenaga kerja antar-vendor/legal entity dalam grup sudah disepakati pada level kontrak (PKS lama berakhir/pergantian vendor, atau perpindahan client dalam satu holding), dengan dokumen Perjanjian/Surat Pengalihan Tugas yang ditandatangani vendor lama, vendor baru, dan karyawan sebagai bukti persetujuan tiga pihak.

### 6.2 Life Cycle

**Actor**: HR Ops vendor lama, HR Ops vendor baru, Payroll/Finance (kedua entitas), karyawan (sebagai pihak yang menyetujui pengalihan).

**Proses bisnis**:
1. Employee ID inti karyawan tetap sama; sistem membuka employment record baru di bawah legal entity/vendor baru, sementara employment record di legal entity/vendor lama ditutup dengan tanggal efektif pengalihan.
2. Riwayat payroll & dokumen pajak (1721-A1) periode sebelum pengalihan tetap diarsipkan terpisah di bawah legal entity lama; periode setelah pengalihan dicatat di bawah legal entity baru.
3. Hak yang wajib dijaga kontinuitasnya disalin ke employment record baru: masa kerja (dihitung dari tanggal mulai kerja aslinya, bukan reset ke tanggal alih tugas, jika disepakati demikian), saldo cuti tahunan terakumulasi, dan eligibility THR proporsional.

**Data/field**: Employee ID (tidak berubah), legal entity lama, legal entity baru, tanggal efektif pengalihan, saldo cuti yang dibawa, masa kerja kumulatif, dokumen Perjanjian/Surat Pengalihan Tugas.

**Business rule & validasi**: perbedaan mendasar dengan mutasi biasa — mutasi tetap dalam satu badan hukum yang sama, sedangkan sub-rogasi melibatkan perubahan pemberi kerja (perubahan NPWP pemotong pajak); masing-masing legal entity menghitung disetahunkan PPh 21 secara terpisah untuk porsi bulan kerja di entitasnya, menghasilkan dua laporan 1721-A1 terpisah dalam satu tahun kalender untuk satu karyawan, dengan masing-masing entitas menerapkan skema tarif pemotongan PPh Pasal 21 sesuai Peraturan Pemerintah Nomor 58 Tahun 2023 tentang Tarif Pemotongan Pajak Penghasilan Pasal 21 (tarif efektif rata-rata untuk pemotongan bulanan, dengan penghitungan ulang/disetahunkan pada masa pajak terakhir menggunakan tarif progresif Pasal 17 Undang-Undang Pajak Penghasilan).

**Approval Workflow**: memerlukan approval tiga pihak (vendor lama, vendor baru, karyawan) atas dokumen Perjanjian Pengalihan sebelum employment record baru dapat diaktifkan; tidak ada jalur emergency override mengingat implikasi hukum ketenagakerjaan & perpajakan lintas-entitas.
- **Skenario Setuju Tiga Pihak** — seluruh pihak menandatangani; employment record baru diaktifkan pada tanggal efektif yang disepakati.
- **Skenario Karyawan Menolak** — karyawan tidak bersedia dialihkan (novasi ketenagakerjaan secara hukum memerlukan persetujuan pekerja, tidak dapat dipaksakan sepihak oleh kedua vendor); kasus ini keluar dari jalur sub-rogasi dan dialihkan ke jalur standar — karyawan mengikuti proses Offboarding dari vendor lama (jika site/PKS benar-benar berakhir) atau tetap di vendor lama jika masih ada penempatan lain yang tersedia.
- **Skenario Vendor Baru Menolak Menerima** — jarang terjadi (biasanya sudah disepakati di level kontrak sebelum proses ini dimulai), namun jika terjadi ketidaksesuaian data karyawan yang baru terungkap saat proses (mis. riwayat disiplin yang belum diketahui vendor baru saat negosiasi kontrak), proses ditunda dan dieskalasi ke Legal kedua vendor untuk renegosiasi cakupan pengalihan.
- **Skenario Dokumen Tidak Lengkap (Revisi)** — salah satu pihak sudah menyetujui prinsip pengalihan tapi dokumen pendukung (khususnya dari sisi karyawan, mis. belum ada persetujuan tertulis final) belum lengkap; status ditahan sebagai "Menunggu Kelengkapan Dokumen", tidak diproses sebagai penolakan.

**Notifikasi/trigger**: notifikasi ke Payroll kedua entitas untuk memulai/mengakhiri employment record sesuai tanggal efektif; notifikasi ke karyawan mengenai perubahan legal entity pemberi kerja.

**Dokumen/output**: dokumen Perjanjian/Surat Pengalihan Tugas (tersimpan di Document Vault), dua employment record (lama ditutup, baru dibuka) di bawah satu Employee ID.

**Integrasi antar tahap**: proses ini bertaut langsung dengan proses Sub-rogasi/Pengalihan Tenaga Kerja pada domain PKS (pemicu kontraktual), serta dengan mekanisme disetahunkan & Multi-Entitas Pajak pada domain Payroll. **Jalur jika PKS mentrigger sub-rogasi sebelum employment record baru siap** (mis. tanggal efektif pengalihan pada dokumen PKS/Perjanjian Pengalihan sudah lewat, namun aktivasi employment record baru di vendor baru — termasuk entri Data Identitas Inti & Data Organisasi — belum selesai): employment record lama tidak ditutup sampai employment record baru benar-benar siap dan tervalidasi, untuk mencegah jeda tanpa employment record aktif (termasuk tanpa kepesertaan BPJS) bagi karyawan yang bersangkutan; tanggal efektif administratif disesuaikan mengikuti tanggal aktivasi riil, dengan selisih hari didokumentasikan sebagai catatan pengecualian. **Jalur jika dua entitas vendor menghitung PPh 21 dengan asumsi tanggal efektif berbeda** (mis. vendor lama menganggap pengalihan efektif akhir bulan, vendor baru menganggap efektif awal bulan berikutnya, sehingga muncul tumpang tindih atau kekosongan hari kerja yang terhitung pajak): kedua entitas wajib merekonsiliasi satu tanggal efektif tunggal yang disepakati bersama sebelum laporan 1721-A1 masing-masing diterbitkan final — laporan tidak diterbitkan sampai rekonsiliasi tanggal efektif selesai, untuk mencegah penghitungan disetahunkan ganda atau hilang atas porsi hari yang sama.

### 6.3 Post-Cycle

Riwayat dua employment record tetap dapat ditelusuri dari satu Employee ID yang sama untuk kebutuhan audit maupun rekonsiliasi data pendukung SPT Tahunan pribadi karyawan, meski secara administratif keduanya terpisah per entitas.

---

## 7. Kontrol Akses Identitas di Client Portal & Kepatuhan Data Pribadi

### 7.1 Pre-Cycle

- **Prasyarat**: klasifikasi field Data Identitas Inti ke dalam dua kategori — field yang boleh terekspos ke client (nama, posisi) dan field yang wajib disembunyikan (NIK, NPWP, data keluarga, rekening bank) — ditetapkan sebagai kebijakan baku sebelum akses portal client dibuka.

### 7.2 Life Cycle

**Actor**: Sistem (penegakan kontrol akses), HR Ops/IT (administrator kebijakan klasifikasi field), PIC client (pengguna akses baca terbatas).

**Proses bisnis**: setiap permintaan data karyawan melalui Client Portal disaring sesuai klasifikasi field yang berlaku; perusahaan outsourcing tetap menjadi pengendali data tunggal atas seluruh field identitas inti — client tidak pernah mendapat akses tulis ke data ini, hanya akses baca terbatas untuk field operasional (misal data kehadiran yang relevan untuk keperluan verifikasi kehadiran/billing).

**Data/field**: daftar field yang diklasifikasikan sebagai "terekspos ke client" vs "disembunyikan", per jenis akses (baca/tulis).

**Business rule & validasi**: sesuai kepatuhan regulasi pelindungan data pribadi yang berlaku di Indonesia (Undang-Undang Nomor 27 Tahun 2022 tentang Pelindungan Data Pribadi, yang telah berlaku penuh sejak Oktober 2024) — pembatasan akses field bersifat wajib, bukan opsional konfigurasi bebas; peraturan pelaksana teknis atas undang-undang tersebut masih dalam proses harmonisasi legislatif pada saat penyusunan dokumen ini, sehingga ketentuan operasional berikutnya perlu disesuaikan begitu peraturan pelaksana tersebut diundangkan.

**Approval Workflow**: perubahan klasifikasi field (menambah/mengurangi field yang boleh terekspos ke client) memerlukan approval HR Manager & fungsi kepatuhan data pribadi internal sebelum diterapkan — tidak dapat diubah sepihak oleh Account Manager/tim client-facing.
- **Skenario Setuju** — kedua approval (HR Manager & fungsi kepatuhan data pribadi internal) menyetujui; perubahan klasifikasi diterapkan pada tanggal efektif yang ditetapkan, dan seluruh sesi Client Portal aktif menyesuaikan tampilan field pada penyegaran berikutnya.
- **Skenario Tolak** — salah satu approver menilai field yang diajukan untuk diekspos berpotensi melanggar prinsip minimalisasi data pribadi (data yang diekspos melebihi kebutuhan operasional client); permintaan ditolak dengan catatan alasan, dan pengaju (Account Manager/tim client-facing) dapat mengajukan ulang dengan justifikasi kebutuhan bisnis yang lebih spesifik.
- **Skenario Perlu Revisi** — pengajuan disetujui sebagian (misal dari lima field yang diajukan, tiga disetujui dan dua ditolak) atau memerlukan pembatasan tambahan (misal field disetujui tapi hanya untuk PIC client dengan peran tertentu, bukan seluruh pengguna client); status dikembalikan ke pengaju untuk konfirmasi cakupan revisi sebelum diterapkan.
- **Skenario Eksepsi (Permintaan Mendesak dari Client)** — untuk kasus client meminta akses field tambahan dengan urgensi bisnis tinggi (misal keperluan audit klien mendadak) di luar siklus reguler: tetap wajib melalui kedua approval yang sama, namun SLA dipercepat menjadi maksimal 1x24 jam — tidak ada jalur yang melewati approval fungsi kepatuhan data pribadi sama sekali, mengingat implikasi regulasi UU PDP.

**Notifikasi/trigger**: tidak ada notifikasi transaksional rutin; perubahan kebijakan klasifikasi field dikomunikasikan sebagai pengumuman internal ke tim HR Ops & IT.

**Dokumen/output**: kebijakan klasifikasi field yang berlaku (versi terkini), log akses baca client atas data karyawan untuk keperluan audit.

**Integrasi antar tahap**: kebijakan klasifikasi field pada bagian ini adalah rujukan kanonik yang dipakai langsung oleh proses penyediaan data karyawan di Client Portal — Client Portal tidak mendefinisikan ulang field mana yang boleh diakses, melainkan mengonsumsi klasifikasi yang ditetapkan di sini. Sebagai topik yang berpasangan (bukan sumber yang sama): pengaturan consent & kepatuhan data pribadi dari **sisi karyawan sebagai subjek data** (berbeda dari akses baca client yang dibahas di sini) dikelola tersendiri pada domain akses mandiri karyawan (ESS), sebagai dua sisi kepatuhan yang saling melengkapi, bukan saling menggantikan. **Jalur jika kebijakan klasifikasi gagal ter-sinkron ke Client Portal** (mis. Client Portal masih menampilkan field versi lama akibat cache/delay sinkronisasi): dianggap insiden kepatuhan yang wajib dieskalasi segera ke IT & fungsi kepatuhan data pribadi, bukan dibiarkan sampai siklus penyegaran berikutnya seperti kondisi normal.

**Pertimbangan Non-Fungsional**: penegakan klasifikasi field wajib berjalan sebagai kontrol di level akses data (bukan hanya di level tampilan antarmuka) — mencegah PIC client mengakses field tersembunyi lewat jalur lain (API/ekspor data); segregasi antar-client (satu client tidak boleh melihat data client lain) adalah syarat keamanan mutlak, bukan sekadar filter tampilan.

### 7.3 Post-Cycle

Log akses baca client menjadi bahan audit kepatuhan berkala dan dasar investigasi apabila terjadi dugaan pelanggaran akses data.

---

## 8. Dokumen Identitas Terkelola (Document Vault per Karyawan)

Document Vault adalah satu titik penyimpanan terkelola untuk seluruh dokumen legal karyawan (KTP/ijazah/SKCK saat lamaran, Kartu Keluarga/akta nikah/akta lahir untuk keperluan status pajak, dokumen pengadilan untuk ganti nama, dan dokumen lain yang selama ini berpotensi tersebar di berbagai proses).

### 8.1 Sub-Siklus: Unggah & Versioning Dokumen

**Pre-Cycle**: dokumen fisik/digital sudah tersedia dari proses sumber (rekrutmen, governance perubahan data pribadi, atau proses lain yang mensyaratkan dokumen pendukung).

**Life Cycle**:
- **Actor**: HR Ops (pengunggah), karyawan (pengunggah mandiri via ESS untuk sebagian jenis dokumen).
- **Proses bisnis**: dokumen diunggah dan dikategorikan per jenis; jika dokumen sejenis sudah ada sebelumnya (versi lama), sistem menyimpan sebagai versi baru tanpa menghapus versi lama, dengan penanda versi mana yang berlaku saat ini.
- **Data/field**: jenis dokumen, nomor versi, tanggal unggah, tanggal berlaku dokumen (jika relevan, misal SKCK), tanggal kedaluwarsa (jika relevan), status (berlaku/kedaluwarsa/diarsipkan).
- **Business rule**: dokumen dengan masa berlaku (SKCK, izin kerja TKA) wajib mengisi field tanggal kedaluwarsa saat diunggah — field ini menjadi basis Sub-Siklus Monitoring Kedaluwarsa (8.2).
- **Approval Workflow**: tidak memerlukan approval manusia untuk unggah dokumen rutin, namun setiap unggahan melalui validasi otomatis sebelum diterima ke Document Vault; penggantian dokumen yang menjadi dasar perubahan data pribadi tetap mengikuti approval Governance Perubahan Data Pribadi (bagian 5).
  - **Skenario Diterima** — file lolos validasi format, ukuran, dan keterbacaan; tersimpan sebagai versi baru dengan status "Berlaku".
  - **Skenario Ditolak Otomatis** — file gagal validasi teknis (format tidak didukung, ukuran melebihi batas maksimum, atau dokumen hasil pindai buram/tidak terbaca oleh pemeriksaan resolusi minimum); pengunggah menerima pesan kesalahan spesifik dan diminta mengunggah ulang — tidak tersimpan sebagai versi apa pun (bukan versi gagal yang tercatat).
  - **Skenario Perlu Konfirmasi Ulang** — sistem mendeteksi kemungkinan dokumen salah kategori (misal jenis dokumen yang dipilih pengunggah tidak konsisten dengan pola dokumen serupa yang pernah diunggah sebelumnya, terdeteksi lewat pencocokan nama file/pola OCR dasar); pengunggah diminta mengonfirmasi ulang kategori sebelum unggahan difinalisasi — bukan penolakan, hanya jeda konfirmasi.
- **Notifikasi/trigger**: tidak ada notifikasi rutin untuk unggah dokumen biasa.
- **Dokumen/output**: dokumen tersimpan dengan riwayat versi lengkap.
- **Integrasi antar tahap**: dokumen berstatus "Berlaku" pada sub-siklus ini menjadi sumber dokumen pendukung tunggal bagi Governance Perubahan Data Pribadi (bagian 5) dan Sub-Siklus Proses Rehire (bagian 4.2) — kedua proses tersebut merujuk ke Document Vault, bukan menyimpan salinan dokumen terpisah. **Jalur jika dokumen yang dirujuk proses lain sedang dalam proses unggah ulang/revisi**: proses sumber (mis. pengajuan perubahan data pribadi yang sedang berjalan) tetap memakai versi dokumen terakhir yang berstatus "Berlaku" sampai versi baru selesai divalidasi — bukan menunggu tanpa kepastian atau memakai versi yang belum final.

**Post-Cycle**: dokumen versi lama tetap dapat ditelusuri untuk keperluan audit historis, meski tidak lagi jadi rujukan aktif.

### 8.2 Sub-Siklus: Monitoring Masa Berlaku & Reminder Kedaluwarsa

**Pre-Cycle**: dokumen dengan field tanggal kedaluwarsa sudah tercatat dari Sub-Siklus 8.1.

**Life Cycle**:
- **Actor**: Sistem (proses otomatis berkala), HR Ops (penindaklanjut reminder).
- **Proses bisnis**: sistem menjalankan pengecekan berkala (harian/mingguan) terhadap seluruh dokumen dengan tanggal kedaluwarsa, membandingkan dengan ambang batas reminder (misal H-30) yang dikonfigurasi per jenis dokumen.
- **Data/field**: jenis dokumen yang dipantau, tanggal berlaku & tanggal kedaluwarsa dokumen, ambang batas reminder per jenis dokumen (mis. H-30 untuk SKCK, H-60 untuk izin kerja TKA), tanggal reminder terkirim, status tindak lanjut (belum diperbarui/sudah diperbarui/kedaluwarsa).
- **Business rule**: ambang batas reminder berbeda per jenis dokumen sesuai tingkat kekritisan (izin kerja TKA lebih kritikal dari SKCK karena berdampak status keimigrasian, sehingga reminder-nya dimulai lebih awal).
- **Approval Workflow**: tidak memerlukan approval — bersifat notifikasi murni.
- **Notifikasi/trigger**: notifikasi otomatis ke HR Ops dan karyawan bersangkutan saat dokumen mendekati kedaluwarsa.
- **Dokumen/output**: daftar dokumen mendekati/sudah kedaluwarsa sebagai *exception list* yang wajib ditindaklanjuti.
- **Integrasi antar tahap**: *exception list* dari sub-siklus ini menjadi salah satu sumber pemeriksaan kelengkapan/kevalidan field pada Data Quality (bagian 9.2) dan menjadi flag kepatuhan khusus bagi dokumen izin kerja Tenaga Kerja Asing yang berdampak status keimigrasian. **Jalur jika reminder terlewat/tidak diterima** (mis. kontak HR Ops atau karyawan yang dituju sudah tidak aktif): status kedaluwarsa dokumen tetap berjalan mengikuti tanggal sistem terlepas dari status pengiriman notifikasi, dan dokumen tetap masuk *exception list* pada siklus pengecekan berikutnya — kegagalan notifikasi tidak menggugurkan status kepatuhan yang sudah lewat tanggal kedaluwarsa.

**Post-Cycle**: dokumen yang diperbarui menghasilkan versi baru (kembali ke Sub-Siklus 8.1); dokumen yang tidak diperbarui melewati tanggal kedaluwarsa berstatus "Kedaluwarsa" dan menjadi flag risiko kepatuhan (khususnya untuk izin kerja TKA yang berdampak legalitas keberadaan tenaga kerja asing).

---

## 9. Kerangka Governance Data Karyawan (Stewardship, Kualitas & Konsistensi Lintas Sistem)

Kerangka ini mengikat seluruh mekanisme pada bagian 2-8 menjadi satu governance menyeluruh, terdiri atas empat sub-siklus yang masing-masing berjalan dengan pola dan frekuensinya sendiri.

### 9.1 Sub-Siklus: Kepemilikan/Stewardship per Kategori Data

**Pre-Cycle**: penetapan matriks kepemilikan data per kategori sebagai kebijakan baku — Data Identitas Inti di bawah stewardship HR Ops, Data Organisasi/Penempatan di bawah HR Ops & Site Manager terkait, Data Payroll-sensitif (rekening bank) di bawah co-stewardship Finance/Payroll.

**Life Cycle**: setiap kali ditemukan inkonsistensi data (dari proses kualitas data 9.2 atau rekonsiliasi 9.4), sistem mengarahkan *exception* ke steward yang berwenang sesuai matriks, bukan ke HR Ops secara umum.

**Post-Cycle**: matriks stewardship direview berkala (misal tahunan) mengikuti perubahan struktur organisasi, memastikan tidak ada kategori data yang kehilangan pemilik yang jelas.

**Actor**: HR Ops, Site Manager, Finance/Payroll (sebagai steward), HR Manager (pemilik kebijakan matriks).

**Data/field**: kategori data (Identitas Inti/Organisasi-Penempatan/Payroll-sensitif), nama & jabatan steward per kategori, fungsi/departemen steward, tanggal efektif penetapan atau perubahan matriks, riwayat perubahan kepemilikan sebelumnya (steward lama, alasan realokasi).

**Business rule & validasi**: setiap kategori data wajib memiliki tepat satu steward utama yang bertanggung jawab menindaklanjuti *exception* — tidak boleh ada kategori data tanpa steward maupun kategori yang diklaim lebih dari satu steward secara bersamaan tanpa kejelasan pembagian tanggung jawab; penetapan/perubahan steward untuk Data Payroll-sensitif (rekening bank) wajib melibatkan kesepakatan bersama HR Manager dan Finance/Payroll mengingat sifat co-stewardship-nya.

**Approval Workflow**: perubahan matriks stewardship memerlukan approval HR Manager.
- **Skenario Setuju** — HR Manager menyetujui perubahan kepemilikan (misal realokasi steward karena perubahan struktur organisasi); matriks baru berlaku efektif sesuai tanggal yang ditetapkan dan dikomunikasikan ke seluruh steward terkait.
- **Skenario Tolak** — HR Manager menilai realokasi yang diajukan berisiko meninggalkan kategori data tanpa steward yang jelas atau tumpang tindih tanggung jawab dengan kategori lain; perubahan ditolak dan matriks lama tetap berlaku.
- **Skenario Perlu Revisi** — perubahan yang diajukan pada dasarnya disetujui secara prinsip tapi cakupan kategori data yang dipindahkan perlu diperjelas (misal pemindahan stewardship sebagian sub-kategori, bukan seluruh kategori); pengaju melengkapi rincian cakupan sebelum difinalisasi.

**Notifikasi/trigger**: notifikasi ke steward baru dan steward lama saat matriks stewardship berubah; notifikasi ke HR Manager saat *exception* yang diarahkan dari 9.2/9.4 tidak kunjung ditindaklanjuti steward terkait dalam batas waktu yang ditetapkan.

**Dokumen/output**: matriks kepemilikan data versi terkini per kategori data, riwayat perubahan matriks sebagai audit trail.

**Integrasi antar tahap**: menjadi rujukan penugasan *exception* pada 9.2 dan 9.4.

### 9.2 Sub-Siklus: Data Quality & Validasi Berkelanjutan

**Pre-Cycle**: jadwal pemeriksaan kualitas data berkala ditetapkan (rekomendasi kuartalan), mencakup tiga jenis pemeriksaan: duplikasi record yang lolos dari deduplikasi awal (bagian 3), data usang (nomor telepon/alamat tidak lagi valid), dan kelengkapan field wajib per kategori status (kandidat/aktif/alumni).

**Life Cycle**:
- **Actor**: Sistem (menjalankan pemeriksaan otomatis), HR Ops/steward terkait (menindaklanjuti hasil).
- **Proses bisnis**: sistem menjalankan pemeriksaan terhadap seluruh data aktif, dipecah menjadi tiga sub-langkah sesuai jenis pemeriksaan:
  - **1a. Deteksi duplikasi residual** — mencari record yang lolos dari deduplikasi awal berbasis NIK KTP (bagian 3), misal karena NIK sempat salah entri lalu dikoreksi belakangan.
  - **1b. Deteksi data usang** — memeriksa field kontak (nomor telepon/alamat) yang polanya mengindikasikan tidak valid (format tidak wajar, tidak pernah diperbarui dalam jangka waktu lama).
  - **1c. Deteksi kelengkapan field wajib** — memeriksa field wajib per kategori status (kandidat/aktif/alumni) yang masih kosong padahal sudah melewati tahapan yang mensyaratkannya.
  - **1d. Konsolidasi** — hasil 1a-1c digabungkan menjadi satu daftar *data quality exception* per kategori data (mengikuti matriks stewardship 9.1), bukan tiga daftar terpisah yang membingungkan steward penerima.
- **Data/field**: kategori pemeriksaan (duplikasi residual/data usang/kelengkapan field), field spesifik yang diperiksa (NIK KTP, nomor telepon, alamat, field wajib per kategori status kandidat/aktif/alumni), tanggal deteksi exception, kategori data pemilik (rujuk matriks 9.1), status tindak lanjut (baru/dalam proses/selesai).
- **Business rule**: setiap exception wajib ditindaklanjuti oleh steward terkait dalam batas waktu yang ditetapkan (mengikuti SLA administratif HR standar); exception yang tidak selesai dalam batas waktu dieskalasi ke HR Manager.
- **Approval Workflow**: tidak memerlukan approval untuk menjalankan pemeriksaan; tindak lanjut atas exception (misal penghapusan record duplikat) mengikuti approval Governance Perubahan Data Pribadi jika menyangkut perubahan data identitas inti.
- **Notifikasi/trigger**: notifikasi ke steward terkait setiap siklus pemeriksaan menghasilkan exception baru.
- **Dokumen/output**: laporan data quality exception per periode, log tindak lanjut.
- **Integrasi antar tahap**: hasil deteksi duplikasi residual pada sub-langkah 1a menjadi umpan balik untuk menyempurnakan aturan deduplikasi pada bagian 3; setiap exception diarahkan ke steward sesuai matriks kepemilikan pada 9.1. **Jalur jika exception tidak ditindaklanjuti dalam SLA**: eskalasi otomatis ke HR Manager sebagaimana diatur pada Business rule, dengan status exception tetap terbuka (bukan otomatis ditutup) sampai tindak lanjut nyata dilakukan oleh steward atau HR Manager.

**Post-Cycle**: hasil pemeriksaan periode berjalan menjadi baseline pembanding untuk pemeriksaan periode berikutnya, memantau tren perbaikan/perburukan kualitas data dari waktu ke waktu.

### 9.3 Sub-Siklus: Kebijakan Retensi & Pemusnahan Data

**Pre-Cycle**: kebijakan retensi konsolidasi per kategori data (identitas inti, dokumen pendukung, riwayat organisasi/payroll) ditetapkan — masa simpan aktif, masa arsip minimal, dan jadwal pemusnahan, dengan pengecualian data yang wajib disimpan lebih lama karena kewajiban hukum (dokumen pajak, bukti kepesertaan BPJS).

**Life Cycle**:
- **Actor**: Sistem (monitoring otomatis usia data), HR Ops/fungsi kepatuhan data pribadi (eksekusi pemusnahan).
- **Proses bisnis**: sistem memantau usia data terhadap kebijakan retensi yang berlaku; data yang melewati masa arsip minimal & tidak termasuk pengecualian kewajiban hukum ditandai sebagai kandidat pemusnahan.
- **Data/field**: kategori data, tanggal mulai retensi/usia data, masa simpan aktif, masa arsip minimal, tanggal jatuh tempo pemusnahan, status (aktif/arsip/kandidat pemusnahan/ditahan-Legal Hold/dimusnahkan), alasan Legal Hold (jika berlaku), tanggal tinjau ulang berikutnya.
- **Business rule**: pemusnahan data tunduk pada persyaratan kepatuhan Undang-Undang Pelindungan Data Pribadi (UU No. 27/2022) dan tidak dapat dijalankan otomatis tanpa tinjauan manusia untuk kategori data yang berpotensi masih dibutuhkan untuk kepentingan hukum yang sedang berjalan (misal dalam sengketa ketenagakerjaan).
- **Approval Workflow**: eksekusi pemusnahan data memerlukan approval fungsi kepatuhan data pribadi internal & HR Manager sebelum dijalankan — tidak ada jalur emergency/otomatis penuh. Pada titik keputusan ini hanya ada dua kemungkinan hasil non-verifikasi: mengeksekusi pemusnahan (Setuju) atau menahannya (Ditahan/Legal Hold); konsep "Tolak" dalam pengertian menolak suatu permintaan tidak relevan di sini karena tidak ada pihak yang mengajukan permintaan pemusnahan yang dapat ditolak — keputusan bersifat evaluasi kelayakan data itu sendiri, sehingga Legal Hold adalah satu-satunya jalur non-eksekusi yang berlaku.
  - **Skenario Setuju** — kedua approver memastikan data kandidat pemusnahan tidak termasuk pengecualian kewajiban hukum maupun kepentingan hukum yang sedang berjalan; pemusnahan dieksekusi dan dicatat dalam log kepatuhan.
  - **Skenario Ditahan (Legal Hold)** — fungsi kepatuhan data pribadi menemukan indikasi data terkait sengketa ketenagakerjaan yang masih berjalan atau potensi kebutuhan pembuktian hukum di masa depan; status data diubah menjadi "Ditahan (Legal Hold)" alih-alih dimusnahkan, dengan tanggal tinjau ulang berikutnya dijadwalkan mengikuti perkembangan status hukum terkait.
  - **Skenario Perlu Verifikasi Tambahan** — kandidat pemusnahan mencakup data yang statusnya ambigu (misal belum jelas apakah termasuk kategori yang wajib disimpan lebih lama karena kewajiban perpajakan); eksekusi ditunda sampai HR Ops/Finance mengonfirmasi klasifikasi data tersebut ke fungsi kepatuhan.
- **Notifikasi/trigger**: notifikasi ke HR Ops/fungsi kepatuhan saat data mencapai kandidat pemusnahan.
- **Dokumen/output**: log pemusnahan data (untuk kebutuhan audit kepatuhan), kebijakan retensi versi terkini.
- **Integrasi antar tahap**: kebijakan retensi berlaku atas seluruh kategori data yang didefinisikan pada Struktur Master Data (bagian 2) dan dokumen pada Document Vault (bagian 8); status "Ditahan (Legal Hold)" pada data yang terkait sengketa ketenagakerjaan bertaut dengan proses investigasi yang berjalan pada domain hubungan industrial. **Jalur jika kandidat pemusnahan ternyata masih dirujuk modul hilir aktif** (mis. dokumen masih dirujuk sebagai bukti pendukung pada proses lain yang belum selesai): eksekusi pemusnahan ditunda otomatis sampai rujukan tersebut dilepas atau proses terkait selesai, bukan dipaksakan mengikuti jadwal retensi standar.

**Post-Cycle**: log pemusnahan menjadi bukti kepatuhan yang dapat ditunjukkan saat audit kepatuhan data pribadi.

### 9.4 Sub-Siklus: Konsistensi Lintas Sistem (Single Source of Truth)

**Pre-Cycle**: daftar modul hilir yang mengonsumsi Data Identitas Inti (Payroll, Client Portal, akses mandiri karyawan, dokumen penempatan) didaftar sebagai basis rekonsiliasi, dengan jadwal rekonsiliasi berkala ditetapkan.

**Life Cycle**:
- **Actor**: Sistem (proses rekonsiliasi otomatis), HR Ops/IT (menindaklanjuti drift yang ditemukan).
- **Proses bisnis**: begitu ada perubahan Data Identitas Inti dengan Tanggal Efektif tertentu, sistem memverifikasi bahwa seluruh modul hilir yang terdaftar sudah memperbarui salinan datanya secara konsisten, bukan sebagian saja.
- **Data/field**: daftar modul hilir terdaftar (Payroll, Client Portal, ESS, dokumen penempatan), field yang direkonsiliasi (rekening bank, NPWP, status kawin, dan field Identitas Inti lain yang relevan), Tanggal Efektif perubahan pada sumber, status sinkronisasi per modul (sinkron/stale/drift), waktu deteksi drift, waktu penyelesaian.
- **Business rule**: modul hilir yang terdeteksi masih memakai data *stale* (belum ter-update sesuai Tanggal Efektif yang berlaku) ditandai sebagai *drift* dan wajib direkonsiliasi sebelum data tersebut dipakai untuk transaksi (misal payroll run tidak boleh berjalan dengan data rekening bank yang stale).
- **Approval Workflow**: tidak memerlukan approval — bersifat mekanisme teknis; drift yang berdampak transaksi kritikal (misal payroll) memicu eskalasi ke HR Manager & IT jika tidak terselesaikan sebelum batas waktu proses hilir terkait.
- **Notifikasi/trigger**: notifikasi ke IT/HR Ops saat drift terdeteksi.
- **Dokumen/output**: laporan rekonsiliasi per periode, log drift yang ditemukan & waktu penyelesaiannya.
- **Integrasi antar tahap**: rekonsiliasi ini menjadi lapisan verifikasi akhir atas integrasi hilir yang sudah disebutkan pada Struktur Master Data (bagian 2), Governance Perubahan Data Pribadi (bagian 5), Sub-rogasi/Pengalihan Vendor (bagian 6), dan Kontrol Akses Client Portal (bagian 7) — bukan menggantikan mekanisme notifikasi yang sudah berjalan pada masing-masing bagian tersebut, melainkan jaring pengaman jika notifikasi tersebut gagal terkirim/terproses. **Jalur jika drift ditemukan pada data yang sedang dipakai transaksi kritikal** (mis. payroll run yang sudah berjalan memakai data rekening bank yang ternyata stale): proses hilir terkait dihentikan sementara/ditandai untuk ditinjau ulang sebelum dilanjutkan, mengikuti Business rule yang melarang pemakaian data stale untuk transaksi.

**Pertimbangan Non-Fungsional**: proses rekonsiliasi berjalan otomatis berkala dan tidak boleh membebani performa modul hilir yang sedang aktif digunakan (mis. tidak dijalankan bersamaan dengan jam sibuk payroll run) — dijadwalkan pada jendela waktu yang tidak mengganggu ketersediaan sistem transaksional.

**Post-Cycle**: hasil rekonsiliasi periode berjalan menjadi indikator kesehatan integrasi data lintas sistem, dipantau sebagai metrik berkelanjutan.

---

## 10. Cakupan Populasi: Karyawan Penempatan vs Internal

Tabel berikut menjadi rujukan tunggal untuk membedakan penerapan setiap bagian pada dua populasi karyawan, agar tidak terjadi ambiguitas populasi mana yang dimaksud di titik pemakaian mana pun dalam domain lain yang merujuk balik ke sini:

| Bagian | Karyawan Penempatan | Karyawan Internal |
|---|---|---|
| Legal Entity (bagian 2) | Terdaftar di salah satu **PT operasional** (khusus populasi ini, alokasi sesuai penempatan/PKS — bukan PT induk) | Terdaftar **hanya** di **PT induk/holding** (rujukan tunggal) |
| Struktur Master Data (bagian 2) | Data Organisasi memakai site/client/PKS | Data Organisasi memakai departemen/fungsi korporat |
| Checklist Onboarding/Offboarding | Termasuk item lapangan (equipment/APD, dokumen penempatan client) | Item lapangan tidak applicable |
| Kompensasi | Basis UMP/UMK statutori mayoritas berlaku | Grade tertentu memakai Salary Structure/Band non-statutori |
| Akses Mandiri (ESS) | Interface sama, modul yang diagregasi sesuai transaksi penempatan | Interface sama, modul yang diagregasi sesuai transaksi internal |
| Kesejahteraan (shuttle/katering) | Relevan khusus site 24 jam/site jauh transportasi | Umumnya tidak applicable di kantor pusat |
| Rekrutmen | Volume tinggi, eksternal | Di luar cakupan domain ini kecuali titik singgung provisioning/onboarding |
| Kontrak Kerja | Mayoritas PKWT mengikuti siklus kontrak client | Mayoritas PKWTT pasca-masa-percobaan |
| Cuti & Shift | Hak cuti dasar berlaku sama; penjadwalan shift/roster khusus site-based | Umumnya jam kerja standar tanpa shift |
| Performance Management | KPI Klien (linkage B.9 poin 1) applicable, mengikuti klausul PKS | Murni KPI Internal, tidak ada KPI Klien |
| Training & Development | Training K3/BNSP/Client-Mandated relevan khusus posisi berisiko/site-based | Siklus L&D korporat sendiri, di luar cakupan domain ini — kecuali training kepatuhan lintas-populasi (linkage Fondasi 15 poin 6) |

---

## 11. Ringkasan Ketentuan Kunci

- Holding terdiri dari beberapa legal entity (PT) dengan peran struktural tetap: 1 PT sebagai induk perusahaan/rujukan tunggal Karyawan Internal, seluruh PT operasional lainnya khusus sebagai *employer of record* Karyawan Penempatan — field Legal Entity di Struktur Master Data (bagian 2) tunduk validasi konsistensi terhadap pemetaan ini, bukan pilihan bebas.
- Satu Employee ID bersifat permanen sepanjang siklus hidup (kandidat–karyawan–alumni–rehire) dan unik lintas seluruh legal entity dalam holding; mutasi maupun sub-rogasi tidak pernah menerbitkan Employee ID baru, melainkan employment record baru di bawah ID yang sama.
- Deduplikasi wajib berbasis NIK KTP sebagai kunci utama, dilengkapi fuzzy-match nama/nomor HP untuk kandidat volume tinggi; setiap potensi duplikat wajib melalui keputusan eksplisit HR Ops (Setuju/Tolak/Perlu Data Tambahan), tidak pernah diputuskan otomatis oleh sistem.
- Perubahan Data Identitas Inti pasca-entri awal wajib melalui Governance Perubahan Data Pribadi dengan dokumen pendukung — perubahan rekening bank secara khusus memerlukan validasi ganda HR Ops & Finance/Payroll mengingat dampak langsung ke pencairan gaji.
- Sub-rogasi/pengalihan vendor memerlukan persetujuan tiga pihak (vendor lama, vendor baru, karyawan) dan tidak dapat dipaksakan sepihak — kontinuitas masa kerja, saldo cuti, dan eligibility THR wajib disalin ke employment record baru, sementara riwayat payroll/pajak tetap diarsipkan terpisah per legal entity.
- Klasifikasi field yang boleh diekspos ke Client Portal ditetapkan secara tunggal di domain ini dan dikonsumsi (bukan didefinisikan ulang) oleh Client Portal — perubahan klasifikasi memerlukan approval ganda HR Manager & fungsi kepatuhan data pribadi.
- Retensi & pemusnahan data tunduk pada UU PDP (No. 27/2022) dan wajib melalui tinjauan manusia; data yang terindikasi terkait sengketa hukum yang masih berjalan wajib ditahan (legal hold), tidak dimusnahkan meski sudah melewati masa retensi standar.
- Konsistensi data lintas sistem dipantau lewat rekonsiliasi berkala berbasis Tanggal Efektif — modul hilir yang masih memakai data *stale* ditandai sebagai *drift* dan wajib direkonsiliasi sebelum dipakai untuk transaksi kritikal seperti payroll run.

---

## 12. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Struktur Master Data | Entri data baru | Validasi & entri Data Identitas Inti dan Data Organisasi | HR Ops, Site Manager, Finance/Payroll | Tidak ada approval terpisah (bagian dari onboarding) | Tidak berlaku langsung | TBD |
| Employee ID | Deduplikasi & penerbitan ID | Pengecekan NIK KTP, fuzzy-match, penerbitan/penautan ID | Sistem, HR Ops | Verifikasi manual HR Ops untuk potensi duplikat | Tidak berlaku langsung | TBD |
| Kontinuitas Identitas | Rehire | Pengecekan eligibility, penautan record lama, pembukaan periode kerja baru | HR Ops, HR Manager | Approval HR Manager untuk alumni dengan catatan | Tidak berlaku langsung | TBD |
| Governance Perubahan Data | Pengajuan & validasi perubahan | Pengajuan, validasi dokumen, validasi ganda untuk rekening bank | Karyawan, HR Ops, Finance/Payroll | HR Ops → Finance/Payroll (khusus rekening bank) | Tidak berlaku langsung | TBD |
| Sub-rogasi/Pengalihan Vendor | Pengalihan employment record | Penandatanganan Perjanjian Pengalihan, pembukaan/penutupan employment record | HR Ops (dua entitas), karyawan | Approval tiga pihak (vendor lama, vendor baru, karyawan) | Klausul Sub-rogasi & Masa Kerja Berkelanjutan pada PKS | TBD |
| Kontrol Akses Client Portal | Penegakan klasifikasi field | Penyaringan field sesuai klasifikasi terekspos/disembunyikan | Sistem, PIC Client | Approval HR Manager & fungsi kepatuhan data pribadi untuk perubahan klasifikasi | Klausul kerahasiaan data dalam PKS | TBD |
| Document Vault | Unggah & versioning | Unggah dokumen, penandaan versi berlaku | HR Ops, karyawan | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Document Vault | Monitoring kedaluwarsa | Pengecekan berkala, reminder H-30 | Sistem, HR Ops | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Governance Data | Kepemilikan/Stewardship | Penetapan & perubahan matriks kepemilikan data, penugasan exception ke steward berwenang | HR Ops, Site Manager, Finance/Payroll, HR Manager | Approval HR Manager untuk perubahan matriks stewardship | Tidak berlaku langsung | TBD |
| Governance Data | Data Quality Kuartalan | Pemeriksaan duplikasi/data usang/kelengkapan field, tindak lanjut exception | Sistem, steward terkait | Eskalasi ke HR Manager jika SLA tindak lanjut terlampaui | Tidak berlaku langsung | TBD |
| Governance Data | Retensi & Pemusnahan | Penandaan kandidat pemusnahan, approval, eksekusi, logging | Sistem, HR Ops, fungsi kepatuhan data pribadi, HR Manager | Approval fungsi kepatuhan data pribadi & HR Manager | Tidak berlaku langsung | TBD |
| Governance Data | Rekonsiliasi Lintas Sistem | Verifikasi update konsisten di modul hilir, penyelesaian drift | Sistem, HR Ops, IT | Eskalasi ke HR Manager & IT untuk drift kritikal | Tidak berlaku langsung | TBD |
