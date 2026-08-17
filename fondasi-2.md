# PKS/Perjanjian Kerja Sama — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

PKS/Perjanjian Kerja Sama adalah payung kontraktual utama yang menjadi *source of truth* bagi seluruh hubungan bisnis antara perusahaan outsourcing dan client — ruang lingkup pekerjaan, jumlah tenaga kerja, lokasi, jangka waktu, nilai kontrak, dan seluruh SLA operasional yang mengikat kedua pihak. Domain ini mencakup siklus penuh PKS dari klasifikasi jenis pekerjaan yang boleh dialihdayakan, drafting-negosiasi-approval, keterkaitan dengan domain lain (rekrutmen, payroll & billing, operasional lapangan, disiplin, offboarding), amandemen, monitoring masa berlaku, pemutusan, kepatuhan & manajemen risiko, tata kelola multi-PKS, onboarding operasional client baru, klausul penempatan WFH/remote, sub-rogasi tenaga kerja, governance dokumen & klausul kontrak, hingga kerangka pricing/rate card sebagai basis negosiasi bill rate.

---

## 2. Klasifikasi Jenis Pekerjaan yang Boleh Dialihdayakan

### 2.1 Pre-Cycle

Sebelum skrining dimulai, dua prasyarat wajib tersedia: (1) proposal awal lingkup pekerjaan calon client (jumlah tenaga, jenis pekerjaan yang diminta) sudah disiapkan Sales/BD sebagai objek yang akan diklasifikasikan; (2) daftar enam kategori positive list Permenaker 7/2026 dan prinsip pekerjaan penunjang PP 35/2021 sudah tersedia sebagai acuan baku yang tetap bagi Legal — bukan ditafsirkan ulang dari nol setiap kali ada calon client baru.

### 2.2 Life Cycle

**Actor**: Legal (skrining awal), Sales/BD (pengusul lingkup kerja calon client), HR Manager/Direksi (keputusan lanjut/tolak untuk lingkup pekerjaan ambigu).

**Business rule & validasi**: klasifikasi ruang lingkup pekerjaan yang dapat dialihdayakan tunduk pada dua lapis regulasi yang perlu dibaca bersamaan. Peraturan Pemerintah Nomor 35 Tahun 2021 menetapkan prinsip umum bahwa pekerjaan yang dialihdayakan harus bersifat **pekerjaan penunjang** (bukan kegiatan pokok/*core business* client). Prinsip umum ini kemudian dipersempit secara eksplisit oleh **Peraturan Menteri Ketenagakerjaan Nomor 7 Tahun 2026 tentang Pekerjaan Alih Daya** (diundangkan 30 April 2026, sebagai tindak lanjut Putusan Mahkamah Konstitusi Nomor 168/PUU-XXI/2023) — regulasi ini menetapkan **daftar tertutup (positive list) enam bidang** yang boleh memakai tenaga alih daya: (1) layanan kebersihan, (2) penyediaan makanan dan minuman, (3) pengamanan, (4) penyediaan pengemudi dan angkutan pekerja, (5) layanan penunjang operasional, dan (6) pekerjaan penunjang di sektor pertambangan, perminyakan, gas, dan ketenagalistrikan. Pekerjaan di luar keenam kategori ini — termasuk sebagian pekerjaan berbasis profesional/IT/back-office processing yang disebut pada klausul WFH/Remote (bagian 16) — **tidak lagi otomatis memenuhi syarat dialihdayakan** hanya dengan dalih "bersifat penunjang" secara umum sebagaimana rezim PP 35/2021 sebelumnya; jenis pekerjaan tersebut wajib dipetakan secara eksplisit ke dalam salah satu dari enam kategori (paling mendekati kategori "layanan penunjang operasional") sebelum PKS dapat diproses, atau ditolak di tahap skrining jika tidak dapat dipetakan.

**Proses bisnis**:
1. Sales/BD mengajukan lingkup pekerjaan calon client sebagai bagian proposal awal ke Legal.
2. Legal memeriksa apakah lingkup kerja yang diajukan cocok langsung dengan salah satu dari enam kategori positive list.
3. Untuk lingkup yang tidak cocok langsung, Legal menilai kelayakan pemetaan ke kategori terdekat (paling umum kategori "layanan penunjang operasional") berdasarkan karakteristik pekerjaan — jika tidak ada kategori yang relevan sama sekali, lingkup kerja ditolak di tahap skrining tanpa perlu eskalasi lebih lanjut.
4. Untuk kasus yang pemetaannya ambigu (Legal tidak dapat memutuskan sendiri lolos/tolak), kasus dieskalasi ke HR Manager/Direksi untuk keputusan lanjut/tolak.

**Approval Workflow**: keputusan skrining bersifat final di level Legal untuk lingkup kerja yang jelas cocok atau jelas tidak cocok dengan salah satu dari enam kategori; eskalasi ke HR Manager/Direksi hanya berlaku untuk lingkup kerja yang pemetaannya ambigu.
- **Skenario Setuju (Lolos Klasifikasi)** — lingkup kerja cocok langsung atau berhasil dipetakan secara wajar ke salah satu dari enam kategori positive list; status "lolos klasifikasi" diberikan dan proses dapat berlanjut ke Drafting, Negosiasi & Approval PKS (bagian 5).
- **Skenario Tolak** — lingkup kerja secara jelas berada di luar keenam kategori dan tidak dapat dipetakan dengan penalaran wajar (mis. lingkup kerja yang sebenarnya kegiatan pokok/*core business* client namun diajukan seolah penunjang); Legal menolak di tahap skrining dan mengembalikan proposal ke Sales/BD tanpa berlanjut ke Drafting.
- **Skenario Perlu Revisi (Ambigu, Menunggu Keputusan Berjenjang)** — pemetaan tidak jelas antara lolos/tolak (mis. lingkup pekerjaan campuran, sebagian jelas penunjang dan sebagian mendekati kegiatan pokok); kasus dieskalasi ke HR Manager/Direksi, disertai permintaan kejelasan/penyempitan lingkup kerja dari Sales/BD sebelum keputusan final diambil — bukan langsung ditolak maupun langsung diloloskan.
- **Skenario Eksepsi (PKS Existing dalam Masa Transisi Permenaker 7/2026)** — khusus untuk PKS yang sudah berjalan sebelum Permenaker 7/2026 diundangkan dengan lingkup kerja di luar enam kategori: klasifikasi ini tidak memblokir keberlanjutan operasional PKS existing selama masa transisi (maksimal dua tahun sejak diundangkan), namun status "belum lolos klasifikasi permanen" tetap dicatat sebagai item remediasi kepatuhan (lihat Post-Cycle) — eksepsi ini tidak berlaku untuk PKS baru atau perluasan lingkup kerja yang diajukan setelah Permenaker 7/2026 berlaku.

### 2.3 Post-Cycle

**Ketentuan transisi**: Permenaker 7/2026 memberi masa penerapan paling lama dua tahun sejak diundangkan untuk ketentuan jenis/bidang pekerjaan ini, dan PKS/perjanjian kerja yang sudah berjalan sebelum aturan ini tetap berlaku sampai kontraknya berakhir. Implikasinya bagi proses bisnis: PKS existing dengan lingkup pekerjaan di luar enam kategori tidak perlu dihentikan mendadak, tetapi wajib masuk daftar **remediasi kepatuhan** yang dipantau bersama proses Monitoring Masa Berlaku & Perpanjangan (bagian 11) — perpanjangan PKS semacam ini setelah masa transisi berakhir memerlukan tinjauan ulang lingkup pekerjaan sebelum disetujui.

**Integrasi antar tahap**: hasil klasifikasi ini menjadi gerbang pertama (gate) sebelum proses Drafting, Negosiasi & Approval PKS (bagian 5) dapat dimulai — PKS tidak dapat diproses lebih lanjut tanpa lingkup pekerjaan yang sudah lolos klasifikasi ini. **Jalur jika lingkup pekerjaan berubah setelah lolos gerbang namun sebelum PKS ditandatangani** (mis. client menambah cakupan pekerjaan di tengah negosiasi bagian 5): perubahan cakupan wajib memicu pengulangan klasifikasi untuk cakupan tambahan tersebut sebelum draft final disetujui — status "lolos klasifikasi" tidak otomatis berlaku untuk cakupan yang belum pernah diperiksa.

---

## 3. Struktur & Klausul Baku PKS

Data kontrak baku yang wajib termuat dalam setiap PKS: nomor kontrak, para pihak, ruang lingkup pekerjaan (hasil klasifikasi bagian 2), jumlah tenaga kerja yang dikomit, lokasi/site, jangka waktu, nilai kontrak, term pembayaran, dan klausul perpanjangan (otomatis vs manual). Struktur baku ini menjadi kerangka isian bagi proses Drafting (bagian 5) dan basis Governance Template Klausul PKS (bagian 18.5).

**Business rule**: PKS wajib secara eksplisit menegaskan bahwa hubungan kerja tetap antara perusahaan outsourcing dengan karyawan, bukan dengan client — klausul ini mencegah pekerjaan yang dialihdayakan berubah menjadi hubungan kerja langsung ke client (lihat bagian 4 mengenai skema hubungan kerja).

---

## 4. Skema Hubungan Kerja dalam PKS

Penegasan bahwa hubungan kerja tetap antara perusahaan outsourcing dan karyawan; klausul PKS wajib mencegah interpretasi bahwa pengalihan pekerjaan berubah menjadi hubungan kerja langsung karyawan-client. Prinsip ini menjadi rujukan validasi saat Legal mereview draft PKS (bagian 5) dan menjadi dasar penegasan Garis Komando (Fondasi 1, bagian 2.2) bahwa PIC client hanya berwenang pada arahan operasional harian, bukan keputusan hubungan kerja.

---

## 5. Siklus Utama: Drafting, Negosiasi & Approval PKS

### 5.1 Pre-Cycle

- **Prasyarat**: lingkup pekerjaan calon client sudah lolos Klasifikasi Jenis Pekerjaan (bagian 2); proposal awal (jumlah tenaga, lokasi, estimasi durasi) sudah disiapkan Sales/BD.
- **Persiapan pricing**: Rate Card Standar (bagian 18) sudah tersedia sebagai titik mulai negosiasi bill rate, dipetakan sesuai kategori posisi/grade dan wilayah penempatan.

### 5.2 Life Cycle

**Actor**: Sales/BD (pengusul & negosiator awal), Legal (review klausul & kepatuhan), Finance (review bill rate & margin), Direksi (approval akhir).

**Proses bisnis**:
1. Sales/BD menyusun proposal awal berdasarkan kebutuhan client dan Rate Card Standar.
2. Legal melakukan review klausul, dipecah menjadi sub-langkah karena masing-masing punya kriteria pemeriksaan berbeda:
   - **2a. Kepatuhan jenis pekerjaan** — memastikan lingkup kerja yang diajukan sesuai hasil Klasifikasi Jenis Pekerjaan (bagian 2), termasuk kesesuaian ke salah satu dari enam kategori Permenaker 7/2026.
   - **2b. Skema hubungan kerja** — memastikan klausul secara eksplisit menegaskan hubungan kerja tetap antara perusahaan outsourcing dan karyawan (bagian 4), tidak ada celah interpretasi hubungan kerja langsung ke client.
   - **2c. Klausul wajib kontekstual** — memeriksa klausul tambahan yang relevan sesuai konteks PKS (WFH/remote jika relevan — bagian 16; ketentuan khusus lain sesuai jenis pekerjaan).
   - **2d. Konsolidasi temuan** — hasil 2a-2c digabungkan menjadi satu status review Legal (lolos/perlu perbaikan klausul tertentu), diteruskan ke tahap negosiasi (langkah 3) hanya jika seluruh sub-pemeriksaan lolos atau perbaikan sudah dilakukan.
3. Negosiasi term (bill rate, SLA operasional — bagian 8, jumlah tenaga, jangka waktu) berjalan antara Sales/BD dan client, dengan Finance memantau dampak margin.
4. Jika hasil negosiasi menghasilkan bill rate di bawah Rate Card Standar (margin tertekan), diperlukan **Approval Deviasi dari Rate Card** — persetujuan tambahan Finance Manager/Direksi sebelum PKS dapat dilanjutkan ke tahap penandatanganan (lihat bagian 18.6).
5. Approval berjenjang sesuai Garis Komando (Fondasi 1): Sales/BD → Legal → Finance → Direksi, mengikuti matrix nilai kontrak.
6. Verifikasi kewenangan tanda tangan kedua pihak dilakukan sebelum penandatanganan (lihat bagian 18.2).
7. PKS ditandatangani dan disimpan ke Document Vault PKS (bagian 18.1).

**Data/field**: nomor draft PKS, versi negosiasi, term yang disepakati per iterasi, hasil review Legal (temuan & status), hasil evaluasi margin Finance, status approval per level.

**Business rule & validasi**: draft PKS yang belum lolos Klasifikasi Jenis Pekerjaan (bagian 2) tidak dapat memasuki tahap review Legal; skema eskalasi bill rate tahunan (mengikuti kenaikan UMP/UMK) wajib dicantumkan sebagai klausul baku, bukan opsional.

**Approval Workflow**: mengikuti matrix Garis Komando (Fondasi 1, bagian 3.1) kategori finansial/strategis — nilai kontrak di bawah ambang tertentu cukup Finance Manager, di atas ambang wajib eskalasi ke Direksi; Approval Deviasi Rate Card berjalan sebagai jalur tambahan di luar approval nilai kontrak biasa (lihat detail pada bagian 18.6 Kerangka Pricing/Rate Card).
- **Skenario Setuju** — seluruh level approval (Finance Manager dan/atau Direksi sesuai ambang) menyetujui; PKS berlanjut ke Verifikasi Kewenangan Tanda Tangan (bagian 18.2) dan penandatanganan.
- **Skenario Tolak** — Direksi/Finance Manager menilai term tidak layak (mis. margin terlalu tipis tanpa justifikasi strategis, atau klausul SLA berisiko tinggi bagi kapasitas operasional saat ini); draft PKS dikembalikan ke Sales/BD dengan alasan penolakan, negosiasi ulang dengan client dimulai dari term yang ditolak, bukan dari nol.
- **Skenario Perlu Revisi** — draft pada dasarnya disetujui secara prinsip namun ada klausul spesifik yang perlu disesuaikan (mis. skema eskalasi bill rate tahunan belum tercantum, atau klausul WFH/remote belum eksplisit); Legal/Finance mengembalikan dengan catatan klausul yang harus dilengkapi tanpa mengulang seluruh siklus negosiasi term lain yang sudah disepakati.
- **Skenario Eksepsi (Client Strategis/Tender Pemerintah)** — untuk client dengan urgensi khusus (mis. tenggat tender BUMN/pemerintah) yang memerlukan percepatan siklus approval: tetap wajib melalui seluruh level approval yang sama tanpa jalur pintas, namun SLA tiap level dipercepat dengan penetapan tenggat eksplisit oleh Direksi — bukan penghapusan salah satu level approval.

**Notifikasi/trigger**: notifikasi ke Legal saat draft baru masuk antrean review; notifikasi ke Finance saat term negosiasi menghasilkan margin di bawah ambang Rate Card.

**Dokumen/output**: dokumen PKS final bertanda tangan, riwayat versi negosiasi (tersimpan di Document Vault, bagian 18.1).

**Integrasi antar tahap**: PKS yang sudah ditandatangani memicu Client Operational Onboarding (bagian 15) dan menjadi basis Requisition Fulfillment (bagian 6); jumlah tenaga kerja yang dikomit di klausul menjadi target acuan yang dipantau sampai kandidat benar-benar ditempatkan.

### 5.3 Post-Cycle

PKS yang aktif masuk ke siklus pemeliharaan berkelanjutan: monitoring masa berlaku (bagian 11), potensi amandemen (bagian 10), dan menjadi rujukan tunggal bagi seluruh transaksi operasional & finansial yang mengalir dari client tersebut.

### 5.4 Sub-Siklus: Kerangka Pricing/Rate Card (lihat bagian 18 untuk detail penuh)

Rate Card Standar bukan bagian terpisah dari siklus drafting — ia adalah input wajib pada Pre-Cycle (5.1) dan pemicu Approval Deviasi pada Life Cycle (5.2, langkah 4). Elaborasi detail sub-siklus governance & versioning Rate Card ada di bagian 18.6 untuk menjaga topik Document/Governance Klausul tetap konsolidasi di satu tempat.

---

## 6. Linkage ke Talent Acquisition & Requisition Fulfillment Tracking

**Pre-Cycle**: PKS baru atau perpanjangan (bagian 5, bagian 11) menjadi trigger pembukaan job requisition.

**Life Cycle**: jumlah tenaga kerja yang dikomit di klausul PKS (bagian 3) menjadi target acuan yang dipantau lewat Requisition Fulfillment Tracking sampai kandidat benar-benar ditempatkan di site — bukan berhenti begitu status requisition dibuka. Untuk PKS yang melibatkan transfer/pengalihan karyawan existing (dari client lama ke client baru, atau saat pergantian vendor outsourcing di satu client), proses ini bercabang ke Sub-rogasi/Pengalihan Tenaga Kerja (bagian 17) alih-alih rekrutmen baru dari nol.

**Business rule**: gap antara jumlah dikomit di PKS dan jumlah aktual ditempatkan menjadi indikator risiko pemenuhan SLA Fulfillment (bagian 8) dan dipantau sebagai bagian evaluasi kinerja pemenuhan kontrak.

**Integrasi antar tahap**: fulfillment tracking ini adalah kepemilikan proses domain Talent Acquisition — di sini hanya dijelaskan sebagai titik pemicu dan target acuannya berasal dari klausul PKS. **Jalur kegagalan**: requisition yang terlanjur dibuka berdasarkan draft PKS yang belum ditandatangani penuh (mis. Sales/BD membuka requisition lebih awal untuk mengejar SLA Fulfillment, sebelum Verifikasi Kewenangan Tanda Tangan bagian 18.2 tuntas) berisiko menempatkan karyawan tanpa payung kontrak yang sah secara hukum; requisition semacam ini wajib berstatus "provisional/menunggu PKS final" dan tidak boleh berlanjut ke penempatan definitif sampai PKS benar-benar tertandatangani — mengikuti prinsip mitigasi risiko yang sama dengan mekanisme freeze requisition pada Penanganan Gap PKS Expired (bagian 11.2), meski triggernya berada di awal siklus, bukan di akhir masa berlaku.

---

## 7. Linkage ke Payroll & Billing

**Pre-Cycle**: bill rate per posisi, term pembayaran, dan skema eskalasi tahunan sudah final di klausul PKS (bagian 3) dan Rate Card (bagian 18.6) sebelum siklus billing periode berjalan mana pun dapat dieksekusi — parameter kontraktual ini adalah prasyarat, bukan sesuatu yang ditentukan saat invoice sedang disusun.

**Life Cycle**: bill rate per posisi tertaut langsung ke klausul PKS (bagian 3) dan Rate Card (bagian 18.6); term pembayaran pada PKS menentukan siklus invoice. Skema eskalasi bill rate tahunan (mengikuti kenaikan UMP/UMK) dan penalti/deduction atas pelanggaran SLA (bagian 8) memengaruhi nilai tagihan periode berjalan. Elaborasi penuh mekanisme kalkulasi & invoicing adalah kepemilikan proses domain Payroll & Billing — di sini PKS berperan sebagai sumber parameter kontraktualnya.

**Post-Cycle**: histori tagihan per periode menjadi dasar rekonsiliasi saat terjadi amandemen retroaktif (bagian 10) atau saat Pemutusan PKS (bagian 12, linkage billing di bagian 18.4) — periode yang sudah ditagih tidak dihapus, melainkan dikoreksi lewat mekanisme penyesuaian pada siklus billing berikutnya.

**Integrasi antar tahap**: parameter kontraktual (bagian 3, 18.6) mengalir satu arah ke domain Payroll & Billing; perubahan pada parameter tersebut (amandemen bagian 10, penalti SLA bagian 8.2) wajib dipropagasi sebelum periode billing berikutnya ditutup. **Jalur kegagalan**: billing belum sinkron saat amandemen retroaktif sudah efektif — jika Tanggal Efektif amandemen mundur ke periode yang invoice-nya sudah diterbitkan ke client (lihat Skenario Retroaktif vs Berlaku ke Depan, bagian 10), periode berjalan saat ini wajib dikoreksi lewat penyesuaian/nota kredit-debit pada siklus billing berikutnya, bukan menerbitkan ulang invoice yang sudah dikirim ke client; selama koreksi belum dilakukan, Addendum Registry (bagian 18.6) tetap menjadi rujukan tunggal term mana yang seharusnya berlaku agar tidak ada domain lain yang memakai term lama secara keliru.

---

## 8. Kerangka SLA — Taksonomi, Penalti & Pelaporan

SLA operasional yang tercantum di PKS (response time replacement, jam kerja/shift, standar kualifikasi tenaga) menjadi parameter monitoring operasional lapangan.

### 8.1 Taksonomi Jenis SLA

Empat jenis SLA yang dinegosiasikan sebagai klausul terpisah saat drafting PKS (bagian 5): **SLA Fulfillment** (waktu pengisian requisition, dipantau bagian 6), **SLA Response Time Replacement** (penggantian reliever, kepemilikan proses domain Reliever & Buffer), **SLA Penerbitan Dokumen** (dokumen penempatan karyawan), **SLA Kualitas/Kepatuhan** (hasil QA Audit lapangan).

### 8.2 Sub-Siklus: Monitoring SLA & Penalti

**Pre-Cycle**: ambang & metodologi penalti per jenis SLA sudah disepakati sebagai klausul PKS (persentase dari nilai invoice periode berjalan, atau nominal tetap per hari keterlambatan — dipilih & disepakati per klausul PKS, tidak seragam lintas-client).

**Life Cycle**:
- **Actor**: Sistem (monitoring otomatis capaian SLA), Account Manager (evaluasi & komunikasi ke client), Finance (kalkulasi penalti untuk dimasukkan ke billing).
- **Proses bisnis**: sistem memantau capaian aktual tiap jenis SLA dibanding target klausul; saat terjadi breach, sistem menghitung nilai penalti sesuai metodologi yang disepakati dan meneruskan ke proses billing periode berjalan; ambang batas breach berulang memicu eskalasi ke proses Amandemen PKS (bagian 10) atau, jika terus-menerus, menjadi salah satu dasar pertimbangan Pemutusan PKS (bagian 12).
- **Business rule**: breach SLA yang levelnya kritikal/berulang wajib didokumentasikan sebagai bagian evaluasi renewal (bagian 11), bukan sekadar catatan operasional yang hilang begitu periode berjalan selesai.
- **Approval Workflow**: penalti yang dihitung sistem masuk ke alur approval billing standar (kepemilikan domain Payroll & Billing); eskalasi ke Amandemen PKS mengikuti approval bagian 10.
- **Skenario Diterima Tanpa Dispute** — client tidak mengajukan keberatan atas penalti dalam batas waktu yang ditetapkan klausul PKS; penalti final masuk ke invoice periode berjalan.
- **Skenario Dispute Client** — client mengajukan keberatan (mis. menganggap breach disebabkan force majeure atau kesalahan pengukuran); Account Manager & Finance meninjau bukti pendukung, dan penalti dapat disesuaikan (dikurangi/dibatalkan) hanya dengan approval Finance Manager — status invoice terkait ditahan sampai dispute selesai, tidak diteruskan dengan angka yang masih disengketakan.
- **Skenario Breach Berulang (Eskalasi Wajib)** — breach jenis SLA yang sama terjadi melebihi ambang frekuensi yang disepakati dalam periode tertentu; kasus ini wajib dieskalasi ke proses Amandemen PKS (bagian 10) untuk peninjauan ulang term SLA, bukan sekadar penalti berulang tanpa tindak lanjut struktural.
- **Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Klausul SLA | String (format SLA-{kode_pks}-{nomor_urut}) | SLA-PKS0231-02 | Identifier unik klausul SLA per PKS |
| Jenis SLA | Enum (Fulfillment/Response Time Replacement/Penerbitan Dokumen/Kualitas-Kepatuhan) | Response Time Replacement | Mengacu taksonomi bagian 8.1 |
| Ambang Target | Numerik (satuan sesuai jenis: jam/hari/persentase) | 24 | Satuan: jam, target maksimal sebelum breach |
| Metodologi Penalti | Enum (Persentase Invoice/Nominal Tetap per Hari) | Persentase Invoice | Disepakati per klausul PKS, tidak seragam lintas-client |
| Nilai Penalti Terhitung | Numerik (IDR) | 4500000 | Hasil kalkulasi sistem saat breach terjadi |
| Tanggal Breach | Tanggal (YYYY-MM-DD) | 2026-07-14 | Tanggal capaian aktual melampaui Ambang Target |
| Status Dispute | Enum (Tidak Ada/Diajukan/Selesai) | Diajukan | Status keberatan client atas penalti terhitung |

- **Notifikasi/trigger**: notifikasi ke Account Manager & client saat breach terjadi; notifikasi ke Finance untuk kalkulasi penalti.
- **Dokumen/output**: dashboard pelaporan performa SLA konsolidasi per client/PKS, menunjukkan tren pemenuhan dari waktu ke waktu.

**Post-Cycle**: dashboard performa SLA menjadi bahan evaluasi wajib saat Monitoring Masa Berlaku & Perpanjangan (bagian 11) dan negosiasi ulang term saat renewal.

**Integrasi antar tahap**: kerangka ini adalah rujukan tunggal bagi seluruh titik lain dalam BRD yang menyebut SLA — domain lain (Fulfillment, Reliever & Buffer, Dokumen Penempatan, QA Audit) tidak mendefinisikan ulang taksonomi/metodologi penaltinya sendiri, melainkan menaati taksonomi dan metodologi yang ditetapkan di sini.

**Pertimbangan Non-Fungsional**: monitoring SLA berjalan terus-menerus (near real-time) lintas seluruh PKS/site aktif secara bersamaan — volume pemantauan bertambah seiring pertumbuhan jumlah client, sehingga mekanisme deteksi breach & kalkulasi penalti wajib tetap akurat dan tidak tertunda meski jumlah PKS aktif meningkat signifikan.

---

## 9. Linkage ke Discipline & Offboarding

**Pre-Cycle**: klausul hak client meminta penggantian tenaga (*replacement request*) tanpa harus melalui proses PHK, serta klausul dampak Pemutusan PKS terhadap status karyawan, sudah tercantum baku di PKS (bagian 3) sebelum kasus disiplin/offboarding aktual terjadi — bukan disepakati ad hoc saat kasus muncul.

**Life Cycle**: PKS memuat klausul hak client meminta penggantian tenaga (*replacement request*) tanpa harus melalui proses PHK — permintaan ini diajukan lewat Client Portal dan tetap melalui approval internal Garis Struktural (Fondasi 1, bagian 2.2) sebelum dieksekusi, bukan keputusan sepihak client. Pemutusan PKS (bagian 12) berdampak langsung pada status karyawan yang ditempatkan di client tersebut — dijabarkan detail pada bagian 12.

**Post-Cycle**: hasil eksekusi replacement request atau offboarding tercatat sebagai bagian riwayat kepatuhan client (bagian 13) — pola replacement request yang berulang/tidak wajar dari satu client menjadi bahan evaluasi hubungan bisnis, setara breach SLA berulang (bagian 8.2).

**Integrasi antar tahap**: proses ini adalah kepemilikan domain Employee Discipline & Offboarding — di sini PKS berperan sebagai sumber klausul hak client dan pemicu dampak status karyawan. **Jalur kegagalan**: billing/payroll periode berjalan yang sudah memproses replacement request atau offboarding berdasarkan term SLA/klausul lama, padahal amandemen retroaktif (bagian 10) sudah efektif mengubah term tersebut sebelum periode payroll ditutup — kasus ini memerlukan rekonsiliasi retroaktif yang sama seperti pada Linkage ke Payroll & Billing (bagian 7), agar penggantian tenaga/offboarding yang sudah dieksekusi tidak dianggap keliru hanya karena term berubah belakangan.

---

## 10. Sub-Siklus: Amandemen & Perubahan PKS

**Pre-Cycle**: perubahan jumlah tenaga, lingkup kerja, atau bill rate diajukan salah satu pihak di tengah periode kontrak — baik dipicu kebutuhan operasional (SO berulang yang bergeser jadi kebutuhan permanen, kepemilikan proses domain Billing), breach SLA berulang (bagian 8.2), maupun inisiatif renegosiasi client.

**Life Cycle**:
- **Actor**: Account Manager (pengusul dari sisi internal), Legal (review dampak klausul), Finance (review dampak margin/billing), Direksi (approval jika signifikan).
- **Proses bisnis**: perubahan direview ulang melalui alur yang setara proses Drafting (bagian 5) namun berbasis PKS existing, bukan dari nol; approval ulang mengikuti matrix Garis Komando sesuai nilai/dampak perubahan; ditentukan eksplisit apakah perubahan berlaku retroaktif atau ke depan saja, mengikuti mekanisme Tanggal Efektif (kepemilikan konsep domain Employee Data Governance).
- **Business rule**: amandemen yang mengubah lingkup pekerjaan wajib diperiksa ulang terhadap Klasifikasi Jenis Pekerjaan (bagian 2) — perluasan lingkup ke jenis pekerjaan di luar enam kategori yang diizinkan Permenaker 7/2026 wajib ditolak meski PKS induk sudah berjalan.
- **Approval Workflow**: mengikuti matrix Garis Komando kategori finansial/strategis, setara approval PKS baru untuk perubahan signifikan (nilai/lingkup besar); perubahan minor (mis. penyesuaian jumlah tenaga kecil dalam ambang) dapat memakai jalur approval lebih ringkas sesuai kebijakan internal.
- **Skenario Setuju** — amandemen disetujui pada seluruh level yang relevan; adendum ditandatangani dan Addendum Registry (bagian 18.6) diperbarui.
- **Skenario Tolak** — perubahan yang diajukan ditolak (mis. perluasan lingkup pekerjaan gagal lolos pemeriksaan ulang Klasifikasi Jenis Pekerjaan pada bagian 2, atau dampak margin dinilai tidak dapat diterima); PKS existing tetap berlaku dengan term lama tanpa perubahan.
- **Skenario Perlu Revisi** — perubahan disetujui secara prinsip namun cakupan/nilai perlu disesuaikan (mis. penambahan jumlah tenaga disetujui sebagian, bukan penuh sesuai permintaan); pengaju melengkapi/menyesuaikan draft amandemen sebelum difinalisasi.
- **Skenario Retroaktif vs Berlaku ke Depan (Klarifikasi Wajib)** — untuk setiap amandemen yang disetujui, approver wajib secara eksplisit menyatakan Tanggal Efektif dan apakah perubahan berlaku retroaktif; amandemen yang disetujui tanpa kejelasan ini dikembalikan sebagai "Perlu Revisi" meski substansinya sudah disepakati, karena berdampak langsung pada rekonsiliasi billing periode yang sudah berjalan.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nomor Amandemen/Adendum | String (alfanumerik, format ADD-{kode_pks}-{nomor_urut}) | ADD-PKS0231-03 | Identifier unik, urut per PKS induk |
| PKS Induk | String (referensi nomor kontrak) | PKS-0231 | Rujukan ke bagian 3 |
| Jenis Perubahan | Enum (Jumlah Tenaga/Lingkup Kerja/Bill Rate/Lainnya) | Bill Rate | Menentukan fungsi mana yang perlu dinotifikasi |
| Tanggal Pengajuan | Tanggal (YYYY-MM-DD) | 2026-08-10 | Tanggal Account Manager mengajukan |
| Tanggal Efektif | Tanggal (YYYY-MM-DD) | 2026-09-01 | Wajib diisi eksplisit saat approval (lihat Skenario Retroaktif) |
| Status Retroaktif | Enum (Retroaktif/Berlaku ke Depan) | Berlaku ke Depan | Wajib eksplisit, tidak boleh kosong saat disetujui |
| Status Approval | Enum (Diajukan/Disetujui/Ditolak/Perlu Revisi) | Disetujui | Mengikuti Approval Workflow di atas |

**Notifikasi/trigger**: notifikasi ke seluruh fungsi terkait (Payroll untuk perubahan bill rate, Talent Acquisition untuk perubahan jumlah tenaga) begitu amandemen efektif.

**Dokumen/output**: dokumen amandemen/adendum, tercatat sebagai versi baru di Document Vault (bagian 18.1) dan memperbarui Addendum Registry (bagian 18.6) sebagai ringkasan term aktif.

**Integrasi antar tahap**: amandemen yang disetujui memicu update Addendum Registry (bagian 18.6) sebagai rujukan term aktif, serta notifikasi ke Payroll & Billing (bagian 7) untuk penyesuaian konfigurasi. **Jalur kegagalan**: jika adendum sudah ditandatangani namun Addendum Registry belum sempat diperbarui saat proses billing/payroll periode berjalan sudah dieksekusi menggunakan term lama — periode tersebut wajib dikoreksi pada siklus billing berikutnya dengan rujukan Tanggal Efektif adendum, bukan dibiarkan sebagai selisih yang tidak dijelaskan; kondisi registry yang belum sinkron dengan dokumen adendum yang sudah sah wajib dapat dideteksi (bukan diasumsikan selalu otomatis sinkron), terutama untuk amandemen berstatus Retroaktif yang efektifnya mundur ke periode yang invoice-nya sudah terbit.

**Post-Cycle**: konfigurasi payroll & billing terkait wajib diperbarui mengikuti klausul baru dengan Tanggal Efektif yang sesuai (kepemilikan proses domain Payroll) — bukan otomatis berubah begitu amandemen ditandatangani.

---

## 11. Sub-Siklus: Monitoring Masa Berlaku, Perpanjangan & Penanganan Gap PKS Expired

### 11.1 Monitoring & Reminder

**Pre-Cycle**: tanggal jatuh tempo PKS terdaftar sejak PKS ditandatangani (bagian 5).

**Life Cycle**: sistem mengirim reminder otomatis mendekati jatuh tempo (mis. H-60, H-30, H-14); status renewal (otomatis/manual) dipantau; dashboard performa SLA (bagian 8.2) dan riwayat breach menjadi bahan evaluasi keputusan renewal.

**Post-Cycle**: hasil monitoring (status mendekati jatuh tempo, riwayat reminder terkirim, evaluasi performa SLA) menjadi dasar keputusan renewal — jika renewal tuntas sebelum jatuh tempo, siklus monitoring untuk PKS/adendum baru dimulai ulang dari Pre-Cycle bagian ini; jika mendekati H-14 tanpa renewal tuntas, proses beralih ke Sub-Siklus Penanganan Gap PKS Expired (bagian 11.2).

### 11.2 Sub-Siklus: Penanganan Gap PKS Expired

**Pre-Cycle**: mendekati H-14 tanpa renewal tuntas.

**Life Cycle**:
1. Sistem memicu status **Pending Renewal**, mengaktifkan **perpanjangan sementara otomatis** (mis. 30 hari, dikonfigurasi sesuai kebijakan internal) sambil negosiasi berjalan — operasional & billing tetap mengacu term PKS lama selama periode ini.
2. Status ini tercermin di Client Portal agar client aware ada urgensi renewal.
3. Jika melewati batas perpanjangan sementara tanpa PKS baru diteken: sistem **freeze requisition baru** untuk client tersebut (operasional existing tidak dihentikan, tapi tidak ada penambahan headcount baru) dan memicu eskalasi otomatis ke Direksi dengan tenggat tegas.

**Business rule**: freeze requisition adalah mekanisme mitigasi risiko hukum (mencegah penempatan karyawan baru tanpa payung kontrak yang jelas), bukan sanksi terhadap client.

**Approval Workflow**: eskalasi ke Direksi mengikuti matrix Garis Komando kategori finansial/strategis, dengan SLA ketat mengingat risiko hukum operasional tanpa kontrak.
- **Skenario Setuju Perpanjangan** — Direksi menyetujui kelanjutan negosiasi dengan perpanjangan sementara diperpanjang lagi (jika negosiasi sudah mendekati kesepakatan final); status "Pending Renewal" berlanjut dengan tenggat baru yang lebih ketat.
- **Skenario Tolak Perpanjangan (Keputusan Tidak Melanjutkan)** — Direksi menilai negosiasi tidak akan mencapai kesepakatan yang dapat diterima (mis. client menolak eskalasi bill rate wajib UMP/UMK); proses beralih ke Pemutusan PKS (bagian 12) dengan notice period dihitung sejak keputusan ini.
- **Skenario Freeze Diperpanjang dengan Syarat** — Direksi mengizinkan freeze requisition tetap berjalan lebih lama dari batas standar namun dengan syarat tambahan (mis. laporan progres negosiasi mingguan ke Direksi); bukan perpanjangan tanpa batas, tetap ada tenggat baru yang eksplisit.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Status Renewal | Enum (On Track/Pending Renewal/Freeze/Selesai) | Pending Renewal | Status utama yang dipantau dashboard |
| Tanggal Mulai Perpanjangan Sementara | Tanggal (YYYY-MM-DD) | 2026-09-03 | Diisi otomatis oleh sistem saat status Pending Renewal aktif |
| Durasi Perpanjangan Sementara (hari) | Numerik | 30 | Dikonfigurasi sesuai kebijakan internal |
| Tanggal Mulai Freeze Requisition | Tanggal (YYYY-MM-DD) | 2026-10-03 | Terisi hanya jika negosiasi melewati batas perpanjangan sementara |
| Tenggat Eskalasi Direksi | Tanggal (YYYY-MM-DD) | 2026-10-10 | SLA ketat, memicu eskalasi otomatis jika terlewati |

**Notifikasi/trigger**: notifikasi berjenjang ke Account Manager, Direksi, dan client (via Client Portal) pada tiap ambang waktu (H-60/H-30/H-14/status freeze).

**Dokumen/output**: log status Pending Renewal & Freeze, riwayat perpanjangan sementara.

**Integrasi antar tahap**: status freeze requisition berkoordinasi dengan Talent Acquisition & Requisition Fulfillment Tracking (bagian 6) — requisition baru untuk client bersangkutan ditolak sistem selama status freeze aktif. **Jalur kegagalan**: requisition yang statusnya sudah "dibuka" tepat sebelum status freeze diaktifkan (mis. requisition dibuka H-15, sementara freeze baru aktif di H-14 karena negosiasi baru dinyatakan gagal setelahnya) tidak otomatis dibatalkan oleh sistem — kebijakan wajib menetapkan eksplisit apakah requisition yang sudah berjalan tetap dilanjutkan sampai selesai atau ikut dibekukan, agar tidak ada requisition yang menggantung tanpa status jelas.

**Post-Cycle**: PKS baru yang akhirnya diteken menutup status Pending Renewal dan mereset siklus monitoring; jika client memutuskan tidak melanjutkan, proses beralih ke Pemutusan PKS (bagian 12).

---

## 12. Sub-Siklus: Pemutusan PKS, Site Baru & Penutupan Site Parsial

### 12.1 Pemutusan PKS Penuh

**Pre-Cycle**: notifikasi pemutusan dari salah satu pihak, sesuai klausul masa pemberitahuan (notice period) pada PKS.

**Life Cycle**:
- **Actor**: Account Manager, Legal, HR (untuk nasib karyawan), Finance (settlement akhir).
- **Proses bisnis**: masa transisi berjalan sesuai notice period; nasib karyawan yang ditempatkan di client tersebut ditentukan — dipindah ke site/client lain (diprioritaskan, mengikuti pola Ramp-down domain Reliever & Buffer), atau offboarding jika tidak ada site tujuan; dokumentasi serah terima akhir disusun bersama client.
- **Business rule**: kewajiban masing-masing pihak (sisa pembayaran, pengembalian aset client-owned — bagian 15.2, kewajiban karyawan) dituntaskan sebelum status PKS ditutup penuh.
- **Approval Workflow**: mengikuti matrix Garis Komando kategori finansial/strategis; keputusan nasib karyawan (offboarding massal) memerlukan approval HR Manager & Legal.
- **Skenario Setuju (Nasib Karyawan Terkonfirmasi)** — rencana redeploy/offboarding untuk seluruh karyawan terdampak disetujui HR Manager & Legal; eksekusi berjalan sesuai notice period.
- **Skenario Tolak Rencana Redeploy** — HR Manager menilai rencana redeploy ke site lain tidak realistis (mis. site tujuan tidak punya kapasitas headcount tambahan); rencana dikembalikan untuk direvisi menjadi opsi offboarding atau site tujuan alternatif.
- **Skenario Perlu Revisi (Kewajiban Belum Tuntas)** — dokumentasi serah terima aset client-owned atau settlement finansial belum lengkap saat notice period akan berakhir; penutupan status PKS ditahan sampai kewajiban dituntaskan, tidak difinalisasi dengan kewajiban yang masih outstanding.
- **Skenario Eksepsi (Pemutusan Sepihak Tanpa Notice Period Penuh)** — client memutus PKS lebih cepat dari notice period yang disepakati (mis. force majeure/kondisi bisnis mendesak); kasus ini dieskalasi langsung ke Direksi & Legal untuk penilaian implikasi hukum/kompensasi sebelum proses transisi karyawan dipercepat mengikuti tenggat yang lebih singkat.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nomor Notice Pemutusan | String (alfanumerik, format NTC-{kode_pks}-{tahun}) | NTC-PKS0231-2026 | Identifier unik notifikasi pemutusan |
| Pihak Pemberi Notice | Enum (Perusahaan Outsourcing/Client) | Client | Menentukan pemicu Approval Workflow/eksepsi |
| Tanggal Notice | Tanggal (YYYY-MM-DD) | 2026-08-17 | Tanggal notifikasi pemutusan diterima |
| Notice Period (hari) | Numerik | 90 | Sesuai klausul PKS terkait |
| Tanggal Efektif Pemutusan | Tanggal (YYYY-MM-DD) | 2026-11-15 | Tanggal Notice + Notice Period, atau lebih cepat jika Skenario Eksepsi |
| Jumlah Karyawan Terdampak | Numerik | 42 | Total karyawan di site/client yang diputus |
| Status Nasib Karyawan | Enum (Redeploy/Offboarding/Belum Ditentukan) | Redeploy | Per karyawan terdampak |
| Status Kewajiban Settlement | Enum (Outstanding/Tuntas) | Outstanding | Dicek sebelum status PKS ditutup penuh |

**Notifikasi/trigger**: notifikasi ke Account Manager, Legal, HR, dan Finance begitu notice pemutusan diterima/diajukan; notifikasi eskalasi ke Direksi & Legal khusus untuk Skenario Eksepsi (notice period dipersingkat); notifikasi ke karyawan terdampak begitu status nasib karyawan (redeploy/offboarding) ditetapkan.

**Dokumen/output**: dokumentasi serah terima akhir, invoice final pro-rata (kepemilikan proses domain Billing).

**Post-Cycle**: status PKS ditutup di sistem; Document Vault (bagian 18.1) tetap menyimpan riwayat PKS untuk kebutuhan audit meski sudah tidak aktif.

### 12.2 Penutupan Site Parsial (Berbeda dari Pemutusan Penuh)

**Pre-Cycle**: berbeda dari trigger Pemutusan Penuh (12.1) yang dipicu notice pemutusan atas seluruh PKS, trigger di sini adalah keputusan client menutup **satu site tertentu** saja (mis. relokasi operasional, efisiensi cabang) sementara PKS dan site-site lain di bawahnya tetap berjalan — prasyaratnya adalah konfirmasi eksplisit dari client bahwa PKS induk dan site lain tidak terdampak, agar tidak disalahartikan sebagai awal Pemutusan Penuh.

**Life Cycle**: karyawan di site yang ditutup mengikuti jalur redeploy ke site lain milik client yang sama (diprioritaskan, berbeda dari Pemutusan Penuh yang redeploy-nya ke site/client lain) atau offboarding (jika tidak ada site tujuan) — **tanpa** memutus PKS/site lain yang masih berjalan. Proses approval & dokumentasi setara skala kecil dari Pemutusan Penuh (12.1) — Actor, Approval Workflow, dan skenario Setuju/Tolak/Perlu Revisi mengikuti pola yang sama — namun cakupannya dibatasi pada satu site, dan tidak memerlukan settlement/invoice final penuh atas seluruh PKS karena kontrak dengan client tetap berjalan.

**Post-Cycle**: statusnya di sistem tetap "PKS Aktif" dengan satu site dinonaktifkan (bukan "PKS Ditutup" seperti pada 12.1) — perbedaan status ini penting agar dashboard monitoring PKS (bagian 11) tidak keliru menganggap seluruh hubungan dengan client tersebut sudah berakhir; riwayat penutupan site tersimpan sebagai bagian histori PKS di Document Vault (bagian 18.1).

### 12.3 Site Baru di Bawah Client Existing (Berbeda dari Client Operational Onboarding)

Kalau client yang sudah punya PKS aktif membuka lokasi baru dengan PKS & term yang sama (cuma menambah site) — berbeda dari Client Operational Onboarding (bagian 15, levelnya client benar-benar baru) dan berbeda dari Ramp-up (levelnya menambah headcount di site yang sudah terdaftar, kepemilikan proses domain Reliever & Buffer).

**Life Cycle**: asesmen kelayakan site baru (kepemilikan proses domain Visit Management), registrasi site sebagai lokasi resmi di sistem sebelum karyawan dapat ditempatkan ke sana, penentuan apakah butuh amandemen PKS (bagian 10) atau cukup adendum site di bawah PKS yang sama tergantung skala penambahan.

---

## 13. Kepatuhan & Risk Management PKS

Checklist kepatuhan legal per PKS — izin usaha alih daya/PPJP jika berlaku (kini juga wajib memastikan lingkup pekerjaan sesuai enam kategori Permenaker 7/2026, bagian 2), Nomor Induk Berusaha, sertifikasi relevan — dipantau berkala sebagai bagian audit trail perubahan PKS, dengan eskalasi ke Legal/Direksi jika ditemukan dispute atau ketidaksesuaian kepatuhan.

**Business rule**: checklist kepatuhan wajib direview ulang setiap kali ada amandemen (bagian 10) atau perubahan regulasi yang relevan (misal Permenaker baru terkait alih daya), bukan hanya diperiksa sekali saat PKS pertama kali ditandatangani.

---

## 14. Multi-PKS dalam Satu Client

Satu client dapat memiliki beberapa PKS terpisah untuk lingkup kerja berbeda (misal PKS untuk layanan kebersihan dan PKS terpisah untuk layanan pengamanan, mengikuti kategori Permenaker 7/2026 yang berbeda). Konsolidasi pelaporan tersedia baik per-PKS maupun gabungan per-client, agar Account Manager dapat memantau hubungan bisnis dengan satu client secara menyeluruh tanpa kehilangan traceability per kontrak.

---

## 15. Sub-Siklus: Client Operational Onboarding & SOP Capture/Change Management

### 15.1 Pre-Cycle

PKS sudah ditandatangani (bagian 5); untuk site takeover dari vendor outsourcing lama, kesepakatan transisi dengan vendor sebelumnya sudah dikonfirmasi.

### 15.2 Life Cycle

**Actor**: Account Manager, tim Visit Management (asesmen site), HR Ops (Training Induction), IT (setup Client Portal).

**Proses bisnis** — tiga proses berjalan **paralel** sebelum status "client aktif beroperasi penuh" tercapai:
1. **Asesmen site awal & penangkapan SOP client** — kondisi fisik dan risk level site dinilai (kepemilikan proses domain Visit Management); SOP client (jam operasional/shift, standar seragam/APD, protokol keselamatan, kontak eskalasi PIC) didokumentasikan sebagai artefak terkelola, menjadi rujukan Training Induction dan klausul Dokumen Penempatan. Untuk site takeover, proses ini mencakup transfer pengetahuan operasional/SOP dari vendor sebelumnya, bukan cuma transfer karyawan.
2. **Setup akses Client Portal** — kepemilikan proses domain Client Portal, dipicu begitu PKS aktif.
3. **Ramp-up** (jika PKS mensyaratkan pemenuhan headcount awal skala besar) — kepemilikan proses domain Reliever & Buffer.

**Business rule & validasi — SOP Change Management**: jika client mengubah SOP di tengah periode kontrak (misal aturan APD baru), perubahan wajib dipropagasi ke Training (retraining jika perlu), klausul Dokumen Penempatan yang di-*reissue*, dan kriteria checklist Visit Management — dengan audit trail versi SOP untuk melacak SOP mana yang berlaku di periode tertentu (relevan saat dispute kepatuhan SOP pada investigasi Discipline).

**Business rule & validasi — Client-Owned Asset Usage Governance**: untuk aset milik client yang dipakai karyawan penempatan selama bertugas (mesin produksi, kendaraan client, perangkat khusus) — beda eksplisit dari aset vendor (ID card/laptop/seragam milik perusahaan outsourcing) — governance mencakup dokumentasi serah terima *penggunaan* (bukan kepemilikan) saat karyawan mulai bertugas, kewajiban perawatan dasar sesuai SOP client, dan linkage liabilitas jika terjadi kerusakan/kehilangan (tanggung jawab finansial mengikuti klausul PKS — ditanggung perusahaan outsourcing sebagai risiko bisnis, atau karyawan individual jika terbukti kelalaian, kepemilikan proses domain Employee Discipline). Dikecualikan untuk karyawan internal karena internal tidak menggunakan aset pihak ketiga/client.

**Approval Workflow**: asesmen site & SOP capture tidak memerlukan approval berjenjang tambahan di luar hasil kerja tim terkait; SOP Change Management yang berdampak biaya (retraining massal, reissue dokumen massal) memerlukan notifikasi ke Finance untuk estimasi dampak biaya.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nama Site | String | Site Gudang Cikarang 2 | Rujukan lokasi hasil registrasi Visit Management |
| Risk Level Site | Enum (Rendah/Menengah/Tinggi) | Menengah | Hasil asesmen awal Visit Management |
| Versi SOP Client | String (format v{mayor}.{minor}) | v1.2 | Bertambah tiap SOP Change Management terjadi |
| Tanggal Efektif Versi SOP | Tanggal (YYYY-MM-DD) | 2026-08-01 | Menandai SOP mana yang berlaku di periode tertentu |
| Status Setup Client Portal | Enum (Belum/Proses/Selesai) | Selesai | Dipantau paralel terhadap dua proses lain |
| Status Ramp-up | Enum (Tidak Berlaku/Proses/Selesai) | Proses | "Tidak Berlaku" jika PKS tidak mensyaratkan ramp-up skala besar |

**Notifikasi/trigger**: notifikasi ke HR Ops & Visit Management saat SOP client versi baru diterbitkan (memicu retraining/reissue dokumen jika perlu); notifikasi ke Finance saat SOP Change Management diperkirakan berdampak biaya; notifikasi ke Account Manager saat ketiga proses paralel (asesmen & SOP, setup Portal, Ramp-up) seluruhnya berstatus selesai.

**Dokumen/output**: dokumen SOP client versi terkini (dengan riwayat versi), catatan serah terima aset client-owned per karyawan.

**Integrasi antar tahap**: ketiga proses paralel bergantung pada status PKS aktif (bagian 5) sebagai prasyarat bersama, dan hasilnya (SOP terdokumentasi) menjadi rujukan Training Induction serta klausul Dokumen Penempatan. **Jalur kegagalan**: jika Ramp-up (kepemilikan domain Reliever & Buffer) mulai menempatkan karyawan ke site sebelum asesmen site & penangkapan SOP selesai terdokumentasi — karyawan berisiko bertugas tanpa rujukan protokol keselamatan/APD yang jelas; kebijakan wajib menahan penempatan definitif sampai SOP client minimal versi awal terdokumentasi, meski proses lain (setup Client Portal, bagian administratif Ramp-up) dapat tetap berjalan lebih dulu secara paralel.

### 15.3 Post-Cycle

Status "client aktif beroperasi penuh" tercapai setelah ketiga proses paralel selesai; SOP client yang terdokumentasi menjadi rujukan permanen bagi Training, Visit Management, dan Dokumen Penempatan sepanjang PKS berjalan.

---

## 16. Klausul Penempatan WFH/Remote

**Pre-Cycle**: kategori posisi yang diusulkan client sudah lolos Klasifikasi Jenis Pekerjaan (bagian 2) — kesesuaian terhadap enam kategori Permenaker 7/2026 tetap wajib diperiksa untuk jenis pekerjaan berbasis profesional/IT/back-office processing sebelum opsi WFH/remote dipertimbangkan sama sekali; tidak ada jalur WFH/remote yang melewati gerbang klasifikasi ini.

**Life Cycle**: untuk kategori posisi yang dapat dikerjakan dari luar site client (call center/back-office processing/IT support berbasis BPO), PKS wajib eksplisit menyatakan apakah arrangement WFH/remote diizinkan, termasuk syarat keamanan data (kepemilikan proses domain IT Asset & System Access) dan standar produktivitas/monitoring pengganti pengawasan fisik. SLA response time replacement untuk posisi remote lazimnya berbeda dari posisi on-site (kepemilikan proses domain Reliever & Buffer) — tidak ada kendala geografis site, tapi ada kendala ketersediaan device/akses remote.

**Post-Cycle**: klausul WFH/remote yang sudah disepakati menjadi rujukan permanen bagi setup akses IT (kepemilikan domain IT Asset & System Access) dan penetapan SLA Response Time Replacement varian remote (kepemilikan domain Reliever & Buffer) sepanjang PKS berjalan; perubahan kebijakan WFH/remote di tengah kontrak (mis. client menarik izin WFH) diperlakukan sebagai amandemen klausul (bagian 10), bukan penyesuaian informal tanpa jejak dokumen.

---

## 17. Sub-Siklus: Sub-rogasi/Pengalihan Tenaga Kerja & Masa Kerja Berkelanjutan

Bagian ini adalah elaborasi kanonik proses sub-rogasi/novasi ketenagakerjaan — sisi struktur data (satu Employee ID dengan banyak *employment record*) dielaborasi pada domain Employee Data Governance, dirujuk balik dari sini.

### 17.1 Pre-Cycle

Trigger: pergantian vendor outsourcing di client yang sama (site takeover, bagian 15) atau perpindahan client dalam satu holding. Perbedaan mendasar dengan mutasi biasa ditetapkan sebagai gerbang klasifikasi: **mutasi** tetap dalam satu badan hukum yang sama; **sub-rogasi/novasi** melibatkan perubahan pemberi kerja (dari PT vendor lama ke PT vendor baru — meski keduanya dalam grup yang sama, atau bahkan lintas perusahaan outsourcing berbeda jika terjadi pengambilalihan dari kompetitor).

### 17.2 Life Cycle

**Actor**: HR Ops vendor lama & vendor baru, Legal (kedua entitas), karyawan (pihak yang menyetujui).

**Proses bisnis**:
1. Karyawan yang sudah bekerja di site tersebut dapat dialihkan langsung tanpa melalui siklus resign-rehire penuh.
2. Dokumen **Perjanjian/Surat Pengalihan Tugas** disusun, menyatakan kontinuitas hak, ditandatangani vendor lama, vendor baru, dan karyawan sebagai bukti persetujuan tiga pihak.
3. Hak yang wajib dijaga berkelanjutan: masa kerja (dihitung dari tanggal mulai kerja asli, bukan reset ke tanggal alih tugas, jika disepakati demikian dalam kesepakatan alih tugas — basis hitung pesangon/UPMK kepemilikan domain Payroll), cuti tahunan terakumulasi, dan eligibility THR proporsional.
4. Status ini tercermin sebagai penerbitan ulang (bukan penerbitan baru dari nol) pada proses Dokumen Penempatan, kategori "transfer antar vendor".

**Data/field**: dokumen Perjanjian/Surat Pengalihan Tugas, tanggal efektif pengalihan, vendor lama & baru, saldo hak yang dibawa (cuti, masa kerja kumulatif).

**Business rule & validasi**: histori payroll periode sebelum alih tugas tetap diarsipkan terpisah per entitas vendor lama untuk keperluan pelaporan pajak (1721-A1 per entitas pemberi kerja — kepemilikan domain Payroll), meski masa kerja untuk hak-hak karyawan dihitung berkelanjutan.

**Approval Workflow**: memerlukan persetujuan tiga pihak (vendor lama, vendor baru, karyawan) atas dokumen Perjanjian Pengalihan; tidak ada jalur emergency override mengingat implikasi hukum ketenagakerjaan & perpajakan lintas-entitas.
- **Skenario Setuju Tiga Pihak** — seluruh pihak menandatangani; employment record baru diaktifkan pada tanggal efektif yang disepakati, dan proses berlanjut ke pembaruan Dokumen Penempatan (langkah 4).
- **Skenario Karyawan Menolak** — karyawan tidak bersedia dialihkan (novasi ketenagakerjaan secara hukum memerlukan persetujuan pekerja, tidak dapat dipaksakan sepihak oleh kedua vendor); proses sub-rogasi dibatalkan untuk karyawan tersebut, dan kasus dialihkan ke jalur standar — offboarding dari vendor lama (jika site benar-benar berpindah tangan) atau redeploy ke site lain milik vendor lama jika tersedia.
- **Skenario Vendor Baru Menolak Menerima** — jarang terjadi karena cakupan pengalihan lazimnya sudah disepakati di level kontrak sebelum proses ini dimulai, namun jika muncul ketidaksesuaian data karyawan yang baru terungkap (mis. riwayat disiplin yang belum diketahui vendor baru saat negosiasi), proses ditunda dan dieskalasi ke Legal kedua vendor untuk renegosiasi cakupan pengalihan.
- **Skenario Dokumen Tidak Lengkap (Perlu Revisi)** — prinsip pengalihan sudah disepakati namun dokumen pendukung belum lengkap (khususnya persetujuan tertulis final dari karyawan); status ditahan sebagai "Menunggu Kelengkapan Dokumen", tidak diproses sebagai penolakan, dan employment record lama tetap berjalan sampai dokumen lengkap.

**Notifikasi/trigger**: notifikasi ke Payroll kedua entitas untuk memulai/mengakhiri employment record sesuai tanggal efektif.

**Dokumen/output**: dokumen Perjanjian/Surat Pengalihan Tugas (tersimpan di Document Vault, bagian 18.1).

**Integrasi antar tahap**: konsekuensi struktur data (satu Employee ID, dua *employment record* terpisah per entitas) dielaborasi penuh pada domain Employee Data Governance — bagian ini berfokus pada pemicu kontraktual, dokumen, dan kontinuitas hak yang menjadi tanggung jawab domain PKS.

### 17.3 Post-Cycle

Riwayat pengalihan tersimpan permanen sebagai bagian Document Vault PKS dan menjadi rujukan jika terjadi dispute mengenai kontinuitas hak di kemudian hari.

---

## 18. Sub-Siklus: Document Vault PKS & Governance Klausul

### 18.1 Document Repository/Vault per PKS

**Life Cycle**: satu titik penyimpanan terkelola untuk seluruh dokumen terkait satu PKS — dokumen asli bertanda tangan, riwayat amandemen/adendum (bagian 10), dokumen pendukung negosiasi, korespondensi legal. Versioning eksplisit menandai versi PKS mana yang berlaku di periode tertentu — penting saat dispute soal klausul mana yang sah berlaku.

**Business rule**: setara konsep Document Vault per Karyawan pada domain Employee Data Governance, tetapi levelnya per-kontrak B2B.

**Pertimbangan Non-Fungsional**: dokumen PKS bertanda tangan memiliki nilai pembuktian hukum — integritas file (tidak dapat diubah diam-diam setelah tersimpan) dan riwayat akses (siapa membuka/mengunduh dokumen versi mana, kapan) wajib terjaga setara standar keamanan dokumen legal, bukan sekadar penyimpanan file biasa.

### 18.2 Verifikasi Kewenangan Tanda Tangan (Signing Authority)

**Pre-Cycle**: sebelum PKS ditandatangani (bagian 5, langkah 6).

**Life Cycle**: validasi bahwa penandatangan dari pihak client benar-benar berwenang secara legal mewakili perusahaan (dokumen pendukung: akta perusahaan, surat kuasa jika diwakilkan — tersimpan di Document Vault, 18.1); validasi setara juga berlaku untuk pihak internal perusahaan outsourcing (siapa berwenang tanda tangan PKS sesuai nilai kontrak, mengikuti matrix Garis Komando).

**Business rule**: PKS yang ditandatangani tanpa verifikasi kewenangan yang lolos berisiko batal demi hukum — verifikasi ini bersifat gerbang wajib, bukan langkah opsional.

**Approval Workflow (Gerbang Verifikasi)**:
- **Skenario Lolos** — dokumen kewenangan (akta perusahaan/surat kuasa) valid dan sesuai; penandatanganan dapat dilanjutkan.
- **Skenario Tidak Lolos** — penandatangan yang diajukan client tidak tercantum dalam akta/tidak memiliki surat kuasa yang sah; Legal menahan proses penandatanganan dan meminta client menunjuk penandatangan yang sah atau melengkapi surat kuasa sebelum melanjutkan — bukan penandatanganan bersyarat yang "disempurnakan belakangan".
- **Skenario Perlu Dokumen Tambahan** — kewenangan pada dasarnya sah namun dokumen pendukung belum lengkap/kedaluwarsa (mis. surat kuasa sudah lewat masa berlaku); proses ditahan sampai dokumen diperbarui, dicatat sebagai status "Menunggu Verifikasi Kewenangan".

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Nama Penandatangan | String | Budi Santoso | Nama pihak yang diajukan untuk menandatangani |
| Pihak | Enum (Client/Perusahaan Outsourcing) | Client | Menentukan dokumen pendukung yang relevan |
| Jabatan | String | Direktur Utama | Dicocokkan terhadap akta perusahaan |
| Nomor Surat Kuasa | String (kosong jika penandatangan adalah pihak yang tercantum langsung di akta) | SK-2026-0087 | Wajib jika penandatangan bukan yang tercantum di akta |
| Masa Berlaku Surat Kuasa | Tanggal (YYYY-MM-DD) | 2026-12-31 | Dicek terhadap tanggal rencana penandatanganan |
| Status Verifikasi | Enum (Lolos/Tidak Lolos/Menunggu Dokumen Tambahan) | Lolos | Hasil Approval Workflow (Gerbang Verifikasi) |

**Integrasi antar tahap**: gerbang ini adalah prasyarat wajib sebelum langkah penandatanganan pada Drafting, Negosiasi & Approval PKS (bagian 5, langkah 6-7) dan sebelum dokumen berstatus final bertanda tangan di Document Vault (bagian 18.1). **Jalur kegagalan**: jika proses penandatanganan terlanjur berjalan (salah satu pihak sudah membubuhkan tanda tangan) sebelum verifikasi kewenangan pihak lain tuntas — dokumen berstatus "ditandatangani sebagian, menunggu verifikasi" dan tidak dianggap sah mengikat sampai verifikasi kedua pihak selesai, mencegah anggapan keliru bahwa tanda tangan satu pihak saja sudah cukup menjadikan PKS berlaku.

### 18.3 Jaminan Pelaksanaan (Performance Bond)

Untuk PKS bernilai besar/klien tertentu yang mensyaratkan jaminan (umum di tender pemerintah/BUMN): pelacakan status jaminan pelaksanaan — nominal, bentuk (bank garansi/deposito), masa berlaku, syarat pencairan oleh client jika terjadi wanprestasi. Reminder otomatis mendekati masa berlaku jaminan berjalan bersamaan dengan monitoring masa berlaku PKS (bagian 11).

### 18.4 Linkage Pemutusan PKS ke Billing

Melengkapi Pemutusan PKS (bagian 12) dengan dampak finansial: invoice final pro-rata sampai tanggal efektif pemutusan, status Retensi Pembayaran yang mungkin masih outstanding tetap mengikuti klausul pencairan meski PKS sudah berakhir, dan Outstanding Payment/Aging yang sudah berjalan tetap ditagih terlepas status PKS aktif/berakhir — elaborasi penuh mekanisme ini kepemilikan proses domain Payroll & Billing.

### 18.5 Governance Template Klausul PKS

Standardisasi klausul baku (template) per jenis PKS (reguler/*project-based*-SOW/dst) yang sudah divalidasi Legal, sebagai basis draft awal saat negosiasi PKS baru (bagian 5) — mengurangi risiko klausul non-standar lolos tanpa review Legal penuh. Deviasi dari template baku memerlukan mekanisme approval khusus (setara Approval Deviasi Rate Card, bagian 18.6).

### 18.6 Kerangka Pricing/Rate Card & Addendum Registry

**Pre-Cycle**: Rate Card Standar periode berjalan sudah ditetapkan/direvisi (mengikuti kenaikan UMP/UMK/UMSP/UMSK tahunan atau evaluasi biaya operasional) sebelum siklus negosiasi PKS mana pun dimulai — Rate Card bukan disusun dadakan per negosiasi, melainkan tersedia sebagai input baku pada Pre-Cycle Drafting PKS (bagian 5.1).

**Life Cycle**:
- **Rate Card Standar**: daftar baku bill rate per kategori posisi/grade dan wilayah (mengikuti variasi UMP/UMK/UMSP/UMSK — kepemilikan domain Payroll), menjadi titik mulai negosiasi (bagian 5) — bukan tarif final otomatis, karena tiap PKS bisa deviasi sesuai hasil negosiasi.
- **Struktur Rate Card**: biaya gaji + BPJS porsi perusahaan + provisi THR + margin, dengan margin dapat berbeda per tier client atau kompleksitas site.
- **Approval Deviasi dari Rate Card**: negosiasi yang menghasilkan bill rate di bawah Rate Card Standar (margin tertekan) memerlukan approval tambahan Finance Manager/Direksi sebelum PKS dapat ditandatangani — mencegah negosiasi Sales/BD mengorbankan margin tanpa sepengetahuan Finance; skenario Setuju/Tolak/Perlu Revisi atas deviasi ini mengikuti percabangan yang sudah dijabarkan pada Approval Workflow bagian 5.2.
- **Addendum Registry — Active Current-Terms Tracking**: berbeda dari Document Vault (levelnya penyimpanan file) — Addendum Registry adalah ringkasan status term aktif per PKS: begitu ada amandemen/adendum (bagian 10), registry menandai term mana dari dokumen mana yang **sedang berlaku** (bukan cuma arsip riwayat pasif), dikonsumsi langsung oleh konfigurasi payroll (kepemilikan domain Payroll) sebagai rujukan tunggal term yang valid saat ini — mencegah sistem hilir memakai klausul dari versi PKS/adendum yang sudah tidak berlaku.

**Post-Cycle**: **Versioning & Riwayat Rate Card** — Rate Card direvisi berkala (mengikuti kenaikan UMP/UMK tahunan atau evaluasi biaya operasional); riwayat versi yang berlaku saat suatu PKS dinegosiasikan tersimpan sebagai bagian Addendum Registry, menjadi rujukan jika terjadi dispute mengenai versi Rate Card mana yang berlaku pada saat negosiasi tertentu terjadi — versi Rate Card yang sudah digantikan tidak dihapus, hanya ditandai tidak berlaku lagi untuk negosiasi baru.

---

## 19. Ringkasan Ketentuan Kunci

- Lingkup pekerjaan yang boleh dialihdayakan dibatasi ke enam kategori positive list Permenaker 7/2026 (masa transisi 2 tahun sejak diundangkan) — ini adalah gerbang kanonik tunggal yang wajib dilalui sebelum PKS dapat diproses, dan wajib diperiksa ulang setiap ada amandemen yang mengubah lingkup kerja.
- Draft PKS wajib menegaskan hubungan kerja tetap antara perusahaan outsourcing dan karyawan, bukan client — mencegah interpretasi hubungan kerja langsung.
- Approval drafting PKS mengikuti matrix finansial/strategis Garis Komando, dengan Approval Deviasi Rate Card sebagai jalur tambahan terpisah saat bill rate hasil negosiasi berada di bawah Rate Card Standar.
- Empat jenis SLA (Fulfillment, Response Time Replacement, Penerbitan Dokumen, Kualitas/Kepatuhan) memakai taksonomi & metodologi penalti tunggal yang ditetapkan di domain ini — tidak didefinisikan ulang oleh domain lain yang mengonsumsinya.
- Amandemen yang memperluas lingkup kerja wajib diperiksa ulang terhadap gerbang klasifikasi jenis pekerjaan, dan setiap amandemen wajib menyatakan eksplisit apakah berlaku retroaktif atau ke depan.
- PKS yang mendekati jatuh tempo tanpa renewal tuntas masuk mekanisme perpanjangan sementara otomatis, diikuti freeze requisition (bukan sanksi, melainkan mitigasi risiko hukum) jika negosiasi berlarut melewati batas.
- Sub-rogasi/pengalihan tenaga kerja antar-vendor memerlukan persetujuan tiga pihak (vendor lama, vendor baru, karyawan) — tidak dapat dipaksakan sepihak, dengan kontinuitas masa kerja & hak-hak karyawan dijaga eksplisit di dokumen Perjanjian Pengalihan.
- Verifikasi kewenangan tanda tangan (signing authority) adalah gerbang wajib sebelum penandatanganan PKS — PKS yang ditandatangani tanpa verifikasi lolos berisiko batal demi hukum.

---

## 20. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Klasifikasi Pekerjaan | Skrining lingkup kerja | Pemetaan lingkup ke enam kategori Permenaker 7/2026 | Legal, Sales/BD, HR Manager/Direksi | Legal: Setuju (lolos)/Tolak; eskalasi terpisah ke HR Manager/Direksi untuk kasus ambigu (Perlu Revisi) | Klausul ruang lingkup pekerjaan | TBD |
| Drafting & Negosiasi PKS | Penyusunan & review | Proposal, review Legal, negosiasi term, approval berjenjang | Sales/BD, Legal, Finance, Direksi | Matrix Garis Komando kategori finansial/strategis | Seluruh klausul PKS | TBD |
| Drafting & Negosiasi PKS | Approval Deviasi Rate Card | Review margin, approval Finance Manager/Direksi | Finance, Direksi | Approval tambahan di luar approval nilai kontrak | Klausul bill rate | TBD |
| Kerangka SLA | Monitoring & penalti | Deteksi breach, kalkulasi penalti, pelaporan performa | Sistem, Account Manager, Finance | Eskalasi ke Amandemen PKS jika berulang | Klausul SLA & penalti | TBD |
| Amandemen PKS | Perubahan term | Pengajuan, review ulang, approval, update konfigurasi hilir | Account Manager, Legal, Finance, Direksi | Setara approval PKS baru untuk perubahan signifikan | Klausul amandemen | TBD |
| Monitoring Masa Berlaku | Penanganan Gap Expired | Pending Renewal, perpanjangan sementara, freeze requisition, eskalasi | Sistem, Account Manager, Direksi | Eskalasi otomatis ke Direksi | Klausul jangka waktu & renewal | TBD |
| Pemutusan PKS | Pemutusan penuh | Notice period, nasib karyawan, serah terima akhir | Account Manager, Legal, HR, Finance | Matrix Garis Komando kategori finansial/strategis | Klausul pemutusan & notice period | TBD |
| Pemutusan PKS | Penutupan site parsial | Redeploy/offboarding karyawan site tertentu | HR, Account Manager | Setara skala kecil dari pemutusan penuh | Klausul multi-site | TBD |
| Client Operational Onboarding | Asesmen & SOP Capture | Asesmen site, dokumentasi SOP, setup portal, ramp-up (paralel) | Account Manager, Visit Management, IT, Reliever & Buffer | Tidak ada approval berjenjang tambahan | Klausul SOP & site | TBD |
| Sub-rogasi Tenaga Kerja | Pengalihan employment record | Penandatanganan Perjanjian Pengalihan, pembukaan/penutupan record | HR Ops (dua entitas), karyawan | Approval tiga pihak | Klausul sub-rogasi & masa kerja berkelanjutan | TBD |
| Document Vault PKS | Verifikasi Signing Authority | Validasi kewenangan tanda tangan kedua pihak | Legal | Gerbang wajib sebelum tanda tangan | Klausul keabsahan kontrak | TBD |
| Document Vault PKS | Jaminan Pelaksanaan | Pelacakan status & reminder masa berlaku bank garansi | Finance, Legal | Tidak ada approval terpisah | Klausul jaminan pelaksanaan | TBD |
