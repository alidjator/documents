# Visit Management — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Visit Management adalah mekanisme kontrol utama yang menjembatani perusahaan outsourcing, tenaga lapangan, dan client — sekaligus bukti kepatuhan terhadap SLA yang tercantum di PKS. Domain ini mencakup: taksonomi jenis kunjungan, perencanaan & penjadwalan, penanggung jawab per wilayah, siklus pelaksanaan visit (persiapan → pelaksanaan & capture data → temuan & tindak lanjut → approval/eskalasi), penanganan kebutuhan visit khusus, arah sebaliknya (client mengunjungi kantor outsourcing), pelaporan & analitik, nilai bukti hukum, varian untuk penempatan remote, serta Quality Assurance Audit sebagai siklus terpisah yang menilai kualitas output kerja, bukan sekadar kondisi kerja.

---

## 2. Jenis-jenis Visit

Lima kategori visit yang dibedakan eksplisit karena masing-masing punya trigger, actor, dan tujuan berbeda:

| Jenis | Arah | Trigger Utama | Tujuan |
|---|---|---|---|
| Site Visit | Internal → Site Client | Jadwal rutin (bagian 3) | Memantau kinerja tenaga, kepatuhan SOP/APD, kondisi kerja |
| Client Visit | Client → Kantor Outsourcing | Permintaan client/jadwal evaluasi | Evaluasi kontrak/PKS, komplain, negosiasi |
| Compliance/Audit Visit | Internal/Eksternal → Site | Jadwal audit berkala atau syarat PKS baru | Audit K3, kepatuhan BPJS/pajak, due diligence calon client |
| Emergency/Ad-hoc Visit | Internal → Site | Insiden (kecelakaan kerja, komplain berat, replacement darurat) | Respons cepat terhadap kejadian mendesak |
| Customer Visit (Field Sales) | Internal → Customer | Task harian sales lapangan | Aktivitas penjualan rutin |

Kategori ini menentukan jalur proses yang berlaku di bagian 5 (siklus utama) — Emergency/Ad-hoc Visit melewati sebagian tahap perencanaan terjadwal karena sifatnya reaktif.

---

## 3. Sub-Siklus: Perencanaan & Penjadwalan Visit

**Pre-Cycle**: klasifikasi risk level per site sudah ditetapkan (kepemilikan proses Fondasi terkait risk assessment site), klausul frekuensi visit minimum (jika diatur) sudah tercantum di PKS terkait.

**Life Cycle**:
- **Actor**: HR Ops/Site Coordinator (penjadwal), Account Manager (validasi terhadap klausul PKS).
- **Proses bisnis**: kalender kunjungan rutin disusun per site dengan frekuensi yang bervariasi mengikuti risk level (site berisiko tinggi/*high-value client* divisit lebih sering); penjadwalan otomatis mempertimbangkan SLA yang tercantum di PKS dan pengelompokan site per region/cluster agar rute kunjungan efisien untuk perusahaan dengan banyak site tersebar.
- **Data/field**: risk level site, frekuensi visit yang ditetapkan, cluster/region site, jadwal visit terjadwal per site (tanggal, penanggung jawab).
- **Business rule & validasi**: frekuensi visit tidak boleh lebih jarang dari ambang minimum yang dijanjikan di klausul PKS (jika ada) — pelanggaran terhadap frekuensi ini sendiri berpotensi jadi temuan breach SLA pada domain PKS.

**Approval Workflow**: kalender kunjungan yang disusun HR Ops/Site Coordinator wajib melalui validasi Account Manager sebelum diterbitkan berlaku efektif — validasi ini adalah approval substantif, bukan sekadar tinjauan pasif.
- **Skenario Setuju** — Account Manager mengonfirmasi kalender yang diajukan sudah sesuai klausul frekuensi PKS dan pengelompokan cluster/region; jadwal diterbitkan dan notifikasi dikirim ke penanggung jawab visit (bagian 4).
- **Skenario Tolak** — Account Manager menilai kalender melanggar ambang frekuensi minimum PKS atau salah alokasi wilayah/cluster; kalender dikembalikan ke HR Ops/Site Coordinator untuk disusun ulang, jadwal tidak diterbitkan.
- **Skenario Perlu Revisi** — kalender secara keseluruhan sudah sesuai klausul PKS namun ada penyesuaian minor (mis. satu-dua site belum masuk cluster yang tepat, tanggal bentrok hari libur site tertentu); HR Ops/Site Coordinator melengkapi bagian yang diminta tanpa menyusun ulang seluruh kalender.
- **Skenario Eksepsi (Perubahan Risk Level Mendadak)** — jika risk level site berubah (mis. insiden baru) setelah kalender diajukan namun sebelum disetujui, Account Manager dapat meminta penyesuaian frekuensi di luar siklus persetujuan reguler tanpa menunggu periode penjadwalan berikutnya.

**Notifikasi/trigger**: notifikasi ke penanggung jawab visit (bagian 4) saat jadwal baru diterbitkan; reminder mendekati tanggal visit terjadwal.

**Dokumen/output**: kalender visit per site/region, menjadi rujukan bagi Reporting & Analytics (bagian 8) untuk menghitung tingkat kepatuhan jadwal.

**Post-Cycle**: realisasi visit dibandingkan terhadap jadwal sebagai basis *compliance rate* (bagian 8); jadwal periode berikutnya disesuaikan berdasarkan evaluasi risk level terbaru (misal site yang baru mengalami insiden dinaikkan frekuensinya).

**Integrasi antar tahap**: klasifikasi risk level site (kepemilikan proses Fondasi terkait risk assessment site) adalah input utama penyusunan frekuensi kunjungan pada bagian ini. **Jalur jika data risk level site dari domain lain belum ter-update saat jadwal visit sudah diterbitkan**: kalender tetap mengikuti risk level terakhir yang tercatat pada saat penyusunan; begitu domain asal menerbitkan pembaruan risk level, jadwal yang sudah terbit ditandai "Berpotensi Usang" dan memicu notifikasi ke HR Ops/Site Coordinator serta Account Manager untuk peninjauan ulang frekuensi pada siklus berjalan — tidak menunggu periode penjadwalan berikutnya jika perubahan risk level tergolong signifikan (mis. naik ke kategori tertinggi akibat insiden).

---

## 4. Actor & Penanggung Jawab Visit

Penugasan visit mengikuti wilayah tanggung jawab (*territory assignment*): HR Ops/Site Coordinator untuk Site Visit rutin, Account Manager untuk Client Visit dan visit terkait evaluasi kontrak, tim K3/Legal dilibatkan khusus untuk Compliance/Audit Visit sesuai kebutuhan. Penugasan berbasis wilayah ini memastikan satu penanggung jawab memiliki akuntabilitas penuh atas seluruh site dalam wilayahnya, termasuk akuntabilitas atas tren temuan (bagian 8) di wilayah tersebut.

Peta wilayah tanggung jawab di atas bersifat rujukan lintas-siklus (*cross-cycle reference*) yang dipakai oleh seluruh sub-siklus visit (bagian 3, 5, 6, 7, 10) — bukan proses transaksional berdiri sendiri. Namun, **penetapan dan perubahan penanggung jawab wilayah itu sendiri** merupakan proses tersendiri yang dianalisis berikut:

**Pre-Cycle**: dipicu oleh penambahan site baru yang belum masuk wilayah manapun, atau perubahan personel (HR Ops/Site Coordinator/Account Manager resign/rotasi/promosi) yang sedang memegang suatu wilayah.

**Life Cycle**:
- **Actor**: HR Ops Manager/Manpower Planning (penetap wilayah untuk Site Visit), Account Manager Lead (penetap wilayah untuk Client Visit).
- **Proses bisnis**: site baru dipetakan ke wilayah/cluster existing, atau wilayah baru dibentuk jika volume site pada satu wilayah melampaui kapasitas wajar satu penanggung jawab; saat terjadi pergantian personel, wilayah dialihkan ke penanggung jawab pengganti disertai serah terima riwayat temuan terbuka dan jadwal aktif site dalam wilayah tersebut.
- **Data/field**: daftar site per wilayah, penanggung jawab aktif per wilayah, tanggal efektif pengalihan, daftar temuan terbuka yang diserahterimakan.
- **Business rule & validasi**: pengalihan wilayah wajib menyertakan serah terima riwayat temuan yang belum ditutup pada wilayah tersebut — akuntabilitas atas temuan berjalan tidak boleh terputus hanya karena pergantian penanggung jawab.

**Approval Workflow**: penetapan/perubahan peta wilayah memerlukan approval HR Ops Manager/Manpower Planning Lead (untuk wilayah Site Visit) atau Account Manager Lead (untuk wilayah Client Visit) sebelum efektif berlaku.
- **Skenario Setuju** — pemetaan wilayah baru/pengalihan disetujui dan efektif pada tanggal yang ditetapkan; notifikasi diterbitkan ke seluruh penanggung jawab terkait dan ke Reporting & Analytics (bagian 8) untuk pembaruan atribusi wilayah pada tren temuan.
- **Skenario Tolak** — usulan pemetaan dinilai tidak seimbang (mis. satu wilayah menjadi terlalu besar dibanding kapasitas wajar satu penanggung jawab); usulan dikembalikan untuk disusun ulang.
- **Skenario Perlu Revisi** — pemetaan secara umum dapat diterima namun ada site tertentu yang perlu dipindah cluster (mis. lebih dekat secara geografis ke wilayah lain); disesuaikan tanpa menyusun ulang seluruh peta wilayah.
- **Skenario Eksepsi (Kekosongan Mendadak)** — jika personel penanggung jawab keluar mendadak tanpa masa transisi, wilayah untuk sementara dirangkap oleh penanggung jawab wilayah terdekat/atasan langsung sampai pengganti tetap ditetapkan melalui jalur approval reguler di atas, agar tidak ada periode tanpa penanggung jawab atas temuan yang sedang berjalan.

**Post-Cycle**: peta wilayah yang telah diperbarui menjadi rujukan baku bagi seluruh sub-siklus visit (bagian 3, 5, 6, 7, 10) sampai terjadi perubahan berikutnya.

---

## 5. Siklus Utama: Pelaksanaan Visit

### 5.1 Pre-Cycle — Persiapan Visit

**Actor**: penanggung jawab visit sesuai jenis (bagian 4).

**Proses bisnis**: sebelum visit dilaksanakan, checklist persiapan disusun mencakup data kehadiran terakhir tenaga di site, riwayat komplain, riwayat pelanggaran/Surat Peringatan karyawan di site tersebut (kepemilikan data domain Employee Discipline), dan status kontrak PKS & masa berlakunya (kepemilikan data domain PKS). Notifikasi dikirim ke site supervisor/PIC client bahwa akan ada visit, kecuali untuk Emergency/Ad-hoc Visit yang sifatnya mendadak (notifikasi menyusul atau bersamaan dengan kedatangan).

**Data/field**: checklist persiapan (data kehadiran, riwayat komplain, riwayat SP, status PKS), jadwal notifikasi ke site.

**Business rule**: kelengkapan checklist persiapan adalah syarat sebelum visit dianggap valid dimulai — visit tanpa checklist persiapan berisiko kehilangan konteks penting saat temuan perlu dievaluasi (misal tidak menyadari sedang ada riwayat pelanggaran berjalan di site tersebut).

### 5.2 Life Cycle — Pelaksanaan & Data Capture Lapangan

**Actor**: penanggung jawab visit, tenaga/PIC client di site (pihak yang diwawancarai/memberi approval).

**Proses bisnis**:
1. Check-in di lokasi berbasis geolocation sebagai bukti kunjungan benar-benar terjadi di site.
2. Pengisian form visit terstruktur, dipecah per kategori pemeriksaan yang masing-masing punya kriteria penilaian sendiri:
   - **2a. Kondisi tenaga kerja** — kehadiran aktual dibanding jadwal, kelengkapan personel sesuai komitmen PKS.
   - **2b. Kepatuhan seragam/APD** — kesesuaian terhadap standar yang ditetapkan SOP client (domain PKS).
   - **2c. Kebersihan/kondisi site** — relevan khususnya untuk lini layanan cleaning/kebersihan.
   - **2d. Wawancara singkat** — dengan tenaga dan/atau PIC client, menangkap keluhan/observasi yang tidak tertangkap checklist terstruktur.
3. Dokumentasi foto sebagai bukti pendukung.
4. *Digital signature*/approval dari PIC client di lokasi sebagai bukti kunjungan diakui kedua pihak.
5. Check-out berbasis geolocation menandai akhir kunjungan.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Koordinat Check-in | Geolocation (latitude, longitude — numerik desimal) | -6.200000, 106.816666 | Dicatat otomatis saat check-in; dasar verifikasi kehadiran fisik di site |
| Waktu Check-in | Timestamp (tanggal & jam) | 2026-08-17 08:02:15 | Dipakai bersama koordinat untuk menghitung durasi kunjungan |
| Koordinat Check-out | Geolocation (latitude, longitude — numerik desimal) | -6.200015, 106.816701 | Selisih jarak terhadap koordinat check-in dapat divalidasi untuk mendeteksi anomali |
| Waktu Check-out | Timestamp (tanggal & jam) | 2026-08-17 09:15:40 | Menandai akhir kunjungan |
| Skor Kondisi Tenaga Kerja (2a) | Enum/numerik (skala 1-5) | "Sesuai" atau skor 4 | Kehadiran aktual vs jadwal, kelengkapan personel sesuai PKS |
| Skor Kepatuhan Seragam/APD (2b) | Enum | "Lengkap" / "Tidak Lengkap" | Kesesuaian terhadap standar SOP client |
| Skor Kebersihan/Kondisi Site (2c) | Numerik (skala 1-5) | 3 | Relevan khususnya lini layanan cleaning/kebersihan |
| Catatan Wawancara (2d) | Teks bebas (string, panjang variabel) | "Tenaga mengeluhkan shift malam berturut-turut" | Menangkap keluhan/observasi kualitatif |
| Foto Dokumentasi | File gambar (JPEG/PNG), dapat berupa array multi-file | visit_2026081701.jpg | Bukti pendukung visual, satu visit dapat memiliki lebih dari satu foto |
| Tanda Tangan Digital PIC Client | File/blob gambar vektor tanda tangan | signature_pic_00458.png | Kosong jika PIC tidak di tempat — memicu status "belum dikonfirmasi client" |
| Durasi Kunjungan | Numerik (menit) | 73 | Dihitung otomatis dari selisih waktu check-in dan check-out |

**Business rule & validasi**: kunjungan tanpa tanda tangan digital PIC client (misal PIC tidak di tempat) tetap dapat disimpan tapi ditandai status "belum dikonfirmasi client" — status ini relevan jika hasil visit kelak dipakai sebagai bukti dispute (bagian 9). Pengumpulan dan penyimpanan data geolocation, foto, dan tanda tangan digital yang melekat pada individu tenaga/PIC client tunduk pada UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi — pemrosesan dibatasi untuk tujuan verifikasi kunjungan dan pembuktian kepatuhan SLA, dengan akses dibatasi hanya pada penanggung jawab visit, reviewer temuan, dan pihak yang menangani dispute hukum sesuai bagian 9.

**Approval Workflow**: pelaksanaan visit itu sendiri tidak memerlukan approval — approval baru relevan pada tahap temuan (bagian 5.3).

**Pertimbangan Non-Fungsional**: data geolocation & foto adalah komponen bukti hukum (bagian 9) — integritas data (koordinat tidak dapat dipalsukan/dimanipulasi setelah tercatat) wajib dijaga setara standar bukti digital; volume foto & data terstruktur terkumpul dari ratusan/ribuan site secara berkelanjutan, sehingga penyimpanan wajib terskala tanpa degradasi performa saat diakses untuk investigasi/dispute.

**Notifikasi/trigger**: notifikasi otomatis ke sistem saat check-in/check-out tercatat, memungkinkan pemantauan real-time kunjungan yang sedang berjalan.

**Dokumen/output**: laporan visit terstruktur (form + foto + tanda tangan digital).

### 5.3 Post-Cycle — Temuan & Tindak Lanjut, Approval & Eskalasi

**Actor**: penanggung jawab visit (pelapor), Site Manager/HR Manager (reviewer untuk temuan mayor), domain tujuan eskalasi sesuai kategori temuan.

**Proses bisnis**:
1. Temuan dikategorikan: **positif**, **minor issue**, atau **major issue/butuh eskalasi**.
2. Setiap temuan dirutekan sesuai kategorinya ke domain pemilik proses yang relevan:
   - Temuan indisipliner → domain Employee Discipline.
   - Temuan kekurangan tenaga/permintaan replacement → domain Reliever & Buffer.
   - Temuan pelanggaran SLA/klausul PKS → domain PKS, termasuk dokumentasi untuk potensi dispute/penalti.
   - Temuan terkait keselamatan kerja → domain Incident Reporting/K3, mengacu pada kewajiban keselamatan kerja sesuai UU No. 1 Tahun 1970 tentang Keselamatan Kerja dan penerapan Sistem Manajemen Keselamatan dan Kesehatan Kerja (SMK3) sesuai PP No. 50 Tahun 2012.
3. Untuk temuan mayor, laporan wajib direview Site Manager/HR Manager sebelum ditutup — tidak dapat langsung ditutup oleh penanggung jawab visit yang membuat laporan (pemisahan pelapor dan penutup temuan mayor untuk menjaga akuntabilitas).
4. Tindak lanjut atas temuan memiliki SLA penyelesaian; jika tidak dijalankan dalam batas waktu, sistem mengeskalasi ke tingkat berikutnya mengikuti Garis Komando.

**Data/field**: kategori temuan, domain tujuan eskalasi, status tindak lanjut (terbuka/dalam proses/selesai), tanggal target penyelesaian, hasil review Site Manager/HR Manager (untuk temuan mayor).

**Business rule & validasi**: temuan yang dirutekan ke domain lain (Discipline/Reliever & Buffer/PKS/K3) tetap dilacak status penutupannya dari sisi Visit Management sebagai *originating record* — Visit Management tidak melepaskan kepemilikan atas temuan begitu dirutekan, melainkan memantau sampai domain tujuan mengonfirmasi penyelesaian.

**Approval Workflow**: temuan minor dapat ditutup langsung oleh penanggung jawab visit; temuan mayor memerlukan approval Site Manager/HR Manager sebelum status ditutup; SLA penyelesaian tindak lanjut mengikuti matrix Garis Komando kategori administratif HR kecuali domain tujuan eskalasi (misal K3 untuk temuan keselamatan kritikal) memiliki SLA spesifiknya sendiri.
- **Skenario Setuju (Temuan Ditutup)** — Site Manager/HR Manager mengonfirmasi tindak lanjut yang dilaporkan sudah memadai dan sesuai kategori temuan; status temuan ditutup dengan rujukan silang ke record penyelesaian di domain tujuan.
- **Skenario Tolak (Tindak Lanjut Belum Memadai)** — reviewer menilai tindak lanjut yang dilaporkan belum menuntaskan akar masalah (mis. replacement sudah dikirim tapi penyebab ketidakhadiran berulang belum ditelusuri); status dikembalikan ke penanggung jawab visit/domain tujuan untuk tindak lanjut tambahan, tidak ditutup meski sudah ada aktivitas penanganan.
- **Skenario Perlu Reklasifikasi** — reviewer menilai kategori temuan yang diajukan penanggung jawab visit tidak tepat (mis. temuan yang dilaporkan sebagai minor ternyata berdampak lebih luas dan seharusnya mayor, atau sebaliknya); temuan direklasifikasi dan mengikuti jalur approval sesuai kategori barunya sebelum dapat ditutup.
- **Skenario Eksepsi (Temuan Keselamatan Kritikal)** — untuk temuan yang dirutekan ke domain K3 dengan indikasi bahaya langsung (imminent danger): penutupan temuan tidak dapat dilakukan Site Manager/HR Manager sendiri, wajib menunggu konfirmasi tuntas dari domain K3 bahwa risiko sudah dimitigasi, terlepas dari SLA administratif HR standar — ketentuan ini selaras dengan kewajiban perusahaan menjaga keselamatan kerja sesuai UU No. 1 Tahun 1970 tentang Keselamatan Kerja dan penerapan SMK3 sesuai PP No. 50 Tahun 2012, sehingga temuan kategori ini tidak dapat diperlakukan setara temuan administratif biasa.

**Notifikasi/trigger**: notifikasi ke domain tujuan saat temuan dirutekan; notifikasi eskalasi jika SLA tindak lanjut terlampaui.

**Dokumen/output**: laporan visit final (termasuk kategori temuan & status tindak lanjut), rujukan silang ke record di domain tujuan eskalasi.

**Integrasi antar tahap**: bagian ini adalah titik masuk utama yang menghubungkan Visit Management ke empat domain lain — Employee Discipline, Reliever & Buffer, PKS, dan Incident Reporting/K3 — sebagai sumber temuan lapangan yang memicu proses di domain-domain tersebut. **Jalur jika domain tujuan tidak pernah mengonfirmasi penyelesaian** (temuan macet di domain tujuan melewati SLA yang wajar): status temuan di Visit Management berubah menjadi "Menunggu Konfirmasi Domain Tujuan — Terlambat" dan dieskalasi ke Site Manager/HR Manager sebagai isu lintas-domain, bukan dibiarkan berstatus "dalam proses" tanpa batas waktu.

---

## 6. Sub-Siklus: Visit untuk Kebutuhan Khusus

Tiga varian visit dengan tujuan spesifik. Ketiganya memakai form data capture dasar (bagian 5.2) dan kanal routing temuan (bagian 5.3) sebagai basis teknis yang sama, namun masing-masing memiliki Pre-Cycle, Life Cycle, Post-Cycle, Actor, Business rule, dan Approval Workflow tersendiri yang berbeda dari visit reguler — dibedah terpisah berikut ini.

### 6.1 Visit Onboarding

**Pre-Cycle**: dipicu oleh proses domain Onboarding saat tenaga baru dinyatakan siap ditempatkan; jadwal visit ditetapkan bertepatan/mendekati tanggal mulai kerja aktual, bukan mengikuti kalender rutin berbasis risk level (bagian 3).

**Life Cycle**:
- **Actor**: HR Ops/Site Coordinator (pelaksana visit), PIC client (penerima perkenalan), tenaga baru yang ditempatkan.
- **Proses bisnis**: 1) perkenalan tenaga baru ke PIC client di site; 2) verifikasi kesiapan penempatan — 2a. kelengkapan seragam/APD sesuai standar site, 2b. pemahaman SOP dasar site oleh tenaga baru, 2c. kecocokan data penempatan (posisi, shift, lokasi) terhadap dokumen onboarding; 3) konfirmasi PIC client bahwa tenaga diterima bertugas di site tersebut.
- **Data/field**: checklist kesiapan penempatan (kelengkapan APD, pemahaman SOP), hasil kecocokan data penempatan, konfirmasi penerimaan PIC client.
- **Business rule & validasi**: verifikasi kesiapan penempatan wajib selesai sebelum tenaga baru dinyatakan berstatus "siap bertugas mandiri" tanpa pendampingan; ketidaksesuaian data penempatan (mis. posisi berbeda dari perjanjian) wajib dieskalasi ke domain Onboarding sebelum visit dapat ditutup sebagai selesai.

**Approval Workflow**: status "siap bertugas mandiri" wajib disetujui Site Coordinator/Account Manager berdasarkan hasil verifikasi kesiapan penempatan — bukan otomatis diberikan begitu visit selesai dilaksanakan.
- **Skenario Setuju** — seluruh item checklist kesiapan terpenuhi dan PIC client mengonfirmasi kecocokan data penempatan; status tenaga baru diubah menjadi "siap bertugas mandiri".
- **Skenario Tolak** — ditemukan ketidaksesuaian material (mis. posisi/shift berbeda dari perjanjian penempatan, atau PIC client menolak menerima tenaga karena kompetensi tidak sesuai); visit onboarding dinyatakan gagal dan dieskalasi ke domain Onboarding untuk penempatan ulang/klarifikasi ke client.
- **Skenario Perlu Revisi** — kekurangan minor (mis. kelengkapan APD belum lengkap, dokumen administratif belum lengkap) yang dapat dilengkapi tanpa mengulang proses onboarding dari awal; status "siap bertugas mandiri" ditunda sampai kekurangan dilengkapi.
- **Skenario Eksepsi (Penempatan Darurat)** — penempatan mendesak (mis. replacement mendadak dari domain Reliever & Buffer) dapat berjalan dengan verifikasi dipercepat sebagian, dengan syarat verifikasi susulan dituntaskan dalam batas waktu yang ditetapkan kebijakan internal sebelum status penuh diberikan.

**Post-Cycle**: hasil visit onboarding menjadi bagian arsip data penempatan tenaga (rujukan silang domain Onboarding), dan menjadi baseline pembanding jika kelak timbul temuan indisipliner/kinerja pada tenaga tersebut (bagian 5.3 dan 11.3).

### 6.2 Visit Offboarding/Serah Terima Site

**Pre-Cycle**: dipicu oleh berakhirnya PKS atau keputusan pemindahan tenaga dari site (kepemilikan proses domain PKS/Offboarding); daftar aset/atribut kerja milik client yang perlu diserahterimakan (jika ada) disiapkan lebih dulu oleh HR Ops/Site Coordinator.

**Life Cycle**:
- **Actor**: HR Ops/Site Coordinator, PIC client (penerima serah terima), tenaga yang dioffboard (jika masih bertugas saat visit berlangsung).
- **Proses bisnis**: 1) verifikasi pengembalian aset/atribut kerja (seragam, ID card, akses site) milik client; 2) dokumentasi kondisi akhir site/serah terima tanggung jawab operasional; 3) konfirmasi tertulis/tanda tangan digital PIC client bahwa serah terima diterima.
- **Data/field**: daftar aset yang diserahterimakan beserta status pengembalian, dokumentasi kondisi akhir site, konfirmasi/tanda tangan digital PIC client.
- **Business rule & validasi**: serah terima tidak dianggap tuntas tanpa konfirmasi tertulis PIC client — status ini menjadi rujukan jika kelak timbul dispute terkait aset yang belum dikembalikan atau kondisi site pasca penarikan tenaga.

**Approval Workflow**: dokumentasi serah terima wajib direview dan disetujui Account Manager sebelum dianggap final, karena berdampak langsung pada penyelesaian administratif PKS (bagian 7 dan domain PKS).
- **Skenario Setuju** — PIC client mengonfirmasi seluruh aset dikembalikan dan tidak ada keberatan; Account Manager menyetujui penutupan administratif site tersebut.
- **Skenario Tolak** — PIC client keberatan (mis. ada aset belum dikembalikan/kondisi site bermasalah) yang dicatat sebagai temuan; penutupan administratif ditahan sampai keberatan diselesaikan, dirutekan ke domain PKS untuk penanganan lebih lanjut (potensi dispute/penalti).
- **Skenario Perlu Revisi** — kekurangan dokumentasi minor (mis. daftar aset belum lengkap dicatat) yang dapat dilengkapi tanpa mengulang proses serah terima; Account Manager menahan approval sampai dokumen dilengkapi.
- **Skenario Eksepsi (Penarikan Mendadak)** — penarikan tenaga mendadak (mis. akibat insiden K3 atau pemutusan sepihak client) dapat menyebabkan serah terima dilakukan tanpa kehadiran fisik tenaga yang dioffboard; dokumentasi tetap wajib dilengkapi PIC client dan HR Ops, dengan catatan eksplisit kondisi darurat pada laporan.

**Post-Cycle**: dokumentasi serah terima menjadi bagian arsip penutupan site pada domain PKS, dan menjadi rujukan riwayat jika site yang sama kembali menjadi client di masa depan (evaluasi riwayat penarikan tenaga).

### 6.3 Visit Investigasi

**Pre-Cycle**: dipicu oleh proses investigasi yang sudah berjalan di domain Employee Discipline atau K3; checklist persiapan (bagian 5.1) disesuaikan khusus untuk kebutuhan pembuktian investigasi (mis. daftar saksi yang perlu diwawancarai, dokumen/rekaman yang perlu diamankan) — bukan checklist standar kondisi kerja.

**Life Cycle**:
- **Actor**: penanggung jawab visit yang ditugaskan investigasi (dapat melibatkan tim Employee Discipline/K3 langsung, bukan hanya HR Ops/Site Coordinator rutin), saksi/pihak terkait di site.
- **Proses bisnis**: 1) wawancara terstruktur dengan saksi/pihak terkait (berbeda dari wawancara singkat bagian 5.2 poin 2d yang sifatnya observasional umum); 2) pengumpulan bukti tambahan (dokumen, rekaman CCTV jika tersedia, foto kondisi tempat kejadian); 3) dokumentasi berantai bukti (*chain of custody*) agar hasil dapat dipakai sebagai dasar keputusan disiplin/K3.
- **Data/field**: catatan wawancara per saksi, daftar bukti yang dikumpulkan beserta sumber & waktu pengambilan, kesimpulan sementara investigator lapangan.
- **Business rule & validasi**: hasil visit investigasi bersifat rahasia dan aksesnya dibatasi hanya untuk pihak berwenang menangani investigasi terkait (domain Employee Discipline/K3) — berbeda dari laporan visit reguler yang dapat diakses lebih luas oleh garis komando site; integritas rantai bukti wajib dijaga karena hasil investigasi berpotensi menjadi dasar sanksi/PHK yang dapat disengketakan secara hukum ketenagakerjaan.

**Approval Workflow**: hasil visit investigasi tidak ditutup oleh penanggung jawab visit sendiri — wajib diserahkan sebagai input ke proses investigasi domain Employee Discipline/K3 yang memegang kewenangan keputusan akhir.
- **Skenario Setuju (Bukti Cukup)** — domain Employee Discipline/K3 menilai bukti dari visit investigasi cukup mendukung proses investigasi yang berjalan; hasil visit dilampirkan resmi ke berkas investigasi.
- **Skenario Tolak (Bukti Tidak Memadai)** — bukti yang terkumpul dinilai tidak cukup/tidak relevan; domain terkait dapat meminta visit investigasi ulang dengan fokus tambahan.
- **Skenario Perlu Revisi (Kelengkapan Dokumentasi)** — bukti secara substansi relevan namun dokumentasinya belum memenuhi standar *chain of custody* (mis. sumber/waktu pengambilan tidak tercatat lengkap); penanggung jawab visit melengkapi dokumentasi tanpa mengulang pengumpulan bukti dari awal.
- **Skenario Eksepsi (Risiko Berkelanjutan)** — jika kondisi site mengindikasikan risiko berkelanjutan (mis. potensi hilangnya barang bukti atau intimidasi saksi), visit investigasi dapat dilaksanakan tanpa notifikasi ke site terlebih dahulu (menyimpang dari pola notifikasi standar bagian 5.1), dengan otorisasi langsung dari domain Employee Discipline/K3.

**Post-Cycle**: hasil visit investigasi menjadi bagian permanen berkas investigasi pada domain pemilik (Employee Discipline/K3), termasuk jika investigasi berlanjut ke proses hukum/perselisihan hubungan industrial (rujukan silang bagian 9 nilai bukti hukum).

**Integrasi antar tahap**: ketiga varian di atas terhubung ke domain yang memicu pelaksanaannya masing-masing — Onboarding (domain Onboarding), Offboarding/Serah Terima (domain PKS/Offboarding), dan Investigasi (domain Employee Discipline/K3); Visit Management berperan sebagai mekanisme eksekusi lapangan atas pemicu dari domain lain, bukan sebagai pemilik keputusan akhir pada ketiganya. **Jalur jika pemicu dari domain asal berubah/dibatalkan setelah visit terjadwal** (mis. penempatan tenaga baru dibatalkan setelah visit onboarding dijadwalkan, atau investigasi dihentikan sebelum visit investigasi terlaksana): visit yang sudah terjadwal dibatalkan oleh penanggung jawab visit dengan mencatat alasan pembatalan bersumber dari domain asal, agar tidak tercatat sebagai visit gagal/tidak terlaksana pada metrik compliance rate (bagian 8).

---

## 7. Sub-Siklus: Client Visit Handling (Arah Sebaliknya)

**Pre-Cycle**: PIC client mengajukan permintaan kunjungan ke kantor outsourcing, atau kunjungan dijadwalkan sebagai bagian evaluasi kontrak berkala.

**Life Cycle**:
- **Actor**: Account Manager (tuan rumah), Legal/Finance (jika agenda mencakup negosiasi ulang/komplain formal).
- **Proses bisnis**: penjadwalan disepakati kedua pihak; agenda ditetapkan di muka (review performa, negosiasi ulang PKS, komplain formal); pertemuan berlangsung dengan dokumentasi hasil (notulen/risalah).
- **Business rule**: hasil pertemuan yang mengarah pada kesepakatan perubahan term kontrak wajib diteruskan sebagai input formal ke proses Amandemen PKS (kepemilikan proses domain PKS) — kesepakatan lisan dalam pertemuan tidak berlaku efektif sampai diformalkan lewat proses amandemen resmi.

**Approval Workflow**: pertemuan Client Visit itu sendiri tidak memerlukan approval formal untuk dilaksanakan (agenda cukup disepakati kedua pihak) — approval baru relevan pada hasil pertemuan yang berdampak kontraktual/finansial, yang harus melalui jalur formal Amandemen PKS (bukan disahkan langsung dalam notulen pertemuan). Karena keputusan akhir ada pada proses Amandemen PKS, skenario berikut menjelaskan bagaimana hasil pertemuan ini diperlakukan sebelum sampai ke sana:
- **Skenario Diteruskan (Kesepakatan Substantif)** — pertemuan menghasilkan kesepakatan perubahan term kontrak; notulen diteruskan sebagai input formal ke proses Amandemen PKS, berstatus rekomendasi sampai amandemen diterbitkan.
- **Skenario Tidak Ditindaklanjuti (Tanpa Kesepakatan Substantif)** — pertemuan bersifat informatif/evaluatif tanpa kesepakatan perubahan term kontrak; notulen cukup diarsipkan sebagai riwayat komunikasi client tanpa diteruskan ke Amandemen PKS.
- **Skenario Eksepsi (Komplain Formal Berujung Eskalasi Legal)** — jika agenda pertemuan berupa komplain formal yang berpotensi menjadi dispute hukum, notulen diteruskan langsung ke Legal (bukan menunggu siklus Amandemen PKS reguler), sejalan dengan notifikasi wajib ke Legal/Finance di bawah.

**Integrasi antar tahap**: kesepakatan hasil Client Visit menjadi input formal bagi proses Amandemen PKS (kepemilikan proses domain PKS) — namun proses Amandemen PKS memegang kewenangan penuh untuk meninjau ulang kelayakan kesepakatan tersebut (mis. aspek legal/finansial yang tidak sepenuhnya terlihat saat pertemuan berlangsung). **Jalur jika kesepakatan yang tercatat di notulen ditolak atau diproses berbeda saat masuk ke Amandemen PKS** (mis. term yang disepakati verbal ternyata tidak sesuai kebijakan finansial internal, atau butuh penyesuaian substansi sebelum ditandatangani): status kesepakatan pada notulen visit diubah menjadi "Diajukan — Menunggu Finalisasi Amandemen", tidak otomatis dianggap final; Account Manager wajib menginformasikan kembali ke PIC client bahwa hasil pertemuan masih bersifat rekomendasi sampai amandemen resmi terbit, mencegah client menganggap kesepakatan lisan/notulen sudah mengikat secara hukum.

**Notifikasi/trigger**: notifikasi ke Legal/Finance jika agenda pertemuan menyentuh isu kontraktual/finansial signifikan.

**Dokumen/output**: notulen/risalah pertemuan, tersimpan sebagai bagian dokumentasi hubungan client (rujukan silang dengan Document Vault PKS jika berdampak pada kontrak).

**Post-Cycle**: hasil pertemuan yang tidak memerlukan tindak lanjut formal tetap terarsip sebagai riwayat komunikasi client, relevan untuk evaluasi hubungan saat renewal PKS.

---

## 8. Sub-Siklus: Reporting & Analytics Visit

**Pre-Cycle**: data jadwal (bagian 3) dan realisasi visit (bagian 5) tersedia sebagai sumber data.

**Life Cycle**:
- **Actor**: Manpower Planning/Account Manager (evaluator), sistem (agregasi data).
- **Proses bisnis**: dashboard menyajikan rekap kepatuhan jadwal visit (*compliance rate* — persentase site yang divisit sesuai jadwal), tren temuan per site/client/wilayah (indikasi site bermasalah yang butuh perhatian lebih), dan korelasi frekuensi visit dengan tingkat komplain/turnover di site tersebut.
- **Business rule**: site dengan compliance rate rendah dan tren temuan memburuk wajib masuk daftar prioritas evaluasi Manpower Planning, terlepas dari apakah client yang bersangkutan sudah mengeluhkan secara eksplisit atau belum.

**Approval Workflow**: penyajian dashboard compliance rate & tren temuan tidak memerlukan approval — sifatnya agregasi/pelaporan otomatis atas data yang sudah tervalidasi pada sumbernya masing-masing (jadwal bagian 3 sudah melalui approval Account Manager, temuan bagian 5.3 sudah melalui approval reviewer sesuai kategorinya). Approval baru relevan pada tindak lanjut atas hasil evaluasi, yaitu keputusan Manpower Planning memasukkan/tidak memasukkan site ke daftar prioritas evaluasi:
- **Skenario Disetujui Masuk Prioritas** — Manpower Planning mengonfirmasi site dengan compliance rate rendah/tren temuan memburuk masuk daftar prioritas evaluasi; rencana tindak lanjut korektif disusun.
- **Skenario Ditolak/Dikecualikan** — Manpower Planning menilai penurunan compliance rate disebabkan faktor yang sudah dimitigasi (mis. sudah ada rencana penambahan penanggung jawab wilayah) sehingga site tidak perlu masuk daftar prioritas tambahan; alasan pengecualian didokumentasikan.
- **Skenario Perlu Data Tambahan** — data tren dinilai belum cukup meyakinkan (mis. sampel periode terlalu pendek); keputusan ditunda sampai periode evaluasi berikutnya tanpa menolak maupun menyetujui secara definitif.

**Integrasi antar tahap**: dashboard ini mengonsumsi data dari Perencanaan & Penjadwalan (bagian 3) dan seluruh siklus pelaksanaan visit (bagian 5, 6, 10) sebagai sumber, serta menjadi salah satu input evaluasi renewal PKS pada domain PKS. **Jalur jika data realisasi visit dari salah satu varian (mis. Virtual Visit bagian 10 atau Visit Kebutuhan Khusus bagian 6) belum tercatat lengkap saat periode rekap dashboard diproses**: site/periode yang datanya belum lengkap ditandai "Data Belum Lengkap" pada dashboard, dikecualikan sementara dari perhitungan compliance rate agregat agar tidak mendistorsi angka, dan dihitung ulang otomatis begitu data susulan masuk.

**Dokumen/output**: dashboard compliance rate & tren temuan, dikonsumsi sebagai bahan evaluasi Manpower Planning dan sebagai salah satu input evaluasi renewal PKS (kepemilikan proses domain PKS).

**Post-Cycle**: hasil evaluasi periode berjalan menjadi baseline pembanding untuk periode berikutnya, memantau apakah tindak lanjut yang sudah dijalankan benar-benar memperbaiki tren.

---

## 9. Kepatuhan & Dokumentasi Legal

Laporan visit (bagian 5.2, termasuk foto, tanda tangan digital, dan data geolocation) berfungsi sebagai alat bukti jika terjadi dispute hukum dengan client atau perselisihan hubungan industrial dengan karyawan — mendokumentasikan kondisi kerja aktual di lapangan pada titik waktu tertentu. Nilai pembuktian ini menjadi pertimbangan tambahan atas kelengkapan dan konsistensi pengisian checklist (bagian 5.1-5.2): laporan yang tidak lengkap atau tanpa konfirmasi PIC client (bagian 5.2) memiliki nilai pembuktian yang lebih lemah dibanding laporan yang lengkap dan terkonfirmasi. Karena data yang dijadikan bukti mencakup data pribadi (foto individu, tanda tangan, dan jejak lokasi), penyimpanan dan penggunaannya sebagai alat bukti tetap tunduk pada UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi — akses untuk keperluan dispute/investigasi dibatasi pada pihak yang berkepentingan langsung (Legal, domain tujuan eskalasi terkait), dan penggunaannya di luar tujuan pembuktian yang sah tidak diperkenankan.

---

## 10. Variasi: Virtual/Remote Visit untuk Penempatan WFH

Untuk tenaga penempatan yang bekerja remote (klausul WFH pada domain PKS), mekanisme visit fisik berbasis geolocation (bagian 5.2) tidak berlaku — audit virtual menggunakan mekanisme pembuktian kehadiran & kepatuhan yang berbeda secara fundamental, sehingga dianalisis dengan siklusnya sendiri berikut ini.

**Pre-Cycle**: jadwal audit virtual disusun mengikuti frekuensi yang sama dengan Site Visit reguler (bagian 3) kecuali klausul WFH pada PKS menentukan lain; kesiapan akses video call/tools monitoring pada perangkat tenaga dipastikan sebelum jadwal ditetapkan.

**Life Cycle**:
- **Actor**: penanggung jawab visit (auditor virtual), tenaga WFH yang diaudit, tim IT Asset & System Access (pemilik data *activity monitoring* yang dikonsumsi di sini).
- **Proses bisnis**:
  1. Video call check-in terjadwal — memverifikasi identitas & kehadiran tenaga pada waktu kerja yang disepakati, menggantikan fungsi geolocation check-in fisik.
  2. Review *screen-sharing*/*activity monitoring* — pemeriksaan log aktivitas kerja (kepemilikan data domain IT Asset & System Access) untuk periode yang diaudit, menggantikan observasi kondisi fisik site.
  3. Wawancara singkat via panggilan — menangkap kendala/keluhan tenaga WFH, setara wawancara bagian 5.2 poin 2d namun via media jarak jauh.
  4. Video call check-out atau penandaan waktu selesai audit pada log sistem.
- **Data/field**: jadwal audit virtual (tanggal, waktu sesi), rekaman/log video call check-in, ringkasan log activity monitoring untuk periode yang diaudit, catatan hasil wawancara, waktu check-out/selesai audit.
- **Business rule & validasi**: karena data yang dikonsumsi berupa rekaman panggilan dan log aktivitas kerja individu, pengambilan dan penyimpanannya tunduk pada UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi — batasan tujuan penggunaan (rekaman hanya untuk keperluan audit kepatuhan, bukan pengawasan di luar konteks kerja), retensi data dibatasi sesuai kebijakan internal, dan akses dibatasi pada pihak yang berwenang menangani audit virtual. Dokumentasi hasil audit virtual tetap mengikuti kategori temuan & tindak lanjut yang sama seperti visit fisik (bagian 5.3) — perbedaan hanya pada metode *capture* bukti (rekaman panggilan/log aktivitas menggantikan foto & geolocation), bukan pada proses eskalasi/tindak lanjutnya.

**Approval Workflow**: pelaksanaan audit virtual itu sendiri tidak memerlukan approval untuk dicatat (sejalan dengan pelaksanaan visit fisik bagian 5.2) — approval baru relevan pada tahap temuan, mengikuti kategori & approval yang sama seperti bagian 5.3:
- **Skenario Setuju (Temuan Ditutup)** — mengikuti pola bagian 5.3, temuan minor ditutup langsung oleh auditor virtual, temuan mayor memerlukan approval Site Manager/HR Manager.
- **Skenario Tolak (Tindak Lanjut Belum Memadai)** — reviewer menilai tindak lanjut atas temuan hasil audit virtual belum menuntaskan akar masalah; dikembalikan untuk tindak lanjut tambahan.
- **Skenario Perlu Reklasifikasi** — kategori temuan yang diajukan dinilai tidak tepat (mis. minor seharusnya mayor); direklasifikasi mengikuti jalur approval kategori barunya.
- **Skenario Eksepsi (Pelanggaran Kerahasiaan Data Client)** — jika activity monitoring menemukan indikasi pelanggaran kerahasiaan data/dokumen client oleh tenaga WFH, penutupan temuan tidak dapat dilakukan Site Manager/HR Manager sendiri, wajib menunggu konfirmasi tuntas dari domain IT Asset & System Access dan/atau Legal bahwa risiko kebocoran data sudah dimitigasi.

**Notifikasi/trigger**: notifikasi ke tenaga WFH saat jadwal audit virtual diterbitkan; notifikasi ke penanggung jawab visit saat sesi video call/log activity monitoring sudah tersedia untuk direview.

**Dokumen/output**: laporan audit virtual (rekaman panggilan/ringkasan log aktivitas, hasil wawancara), mengikuti struktur laporan temuan yang sama seperti visit fisik (bagian 5.3).

**Post-Cycle**: hasil audit virtual masuk ke Reporting & Analytics (bagian 8) dengan penanda jenis visit "Virtual" agar compliance rate dapat dianalisis terpisah antara site fisik dan penempatan WFH; nilai pembuktian hukum rekaman panggilan mengikuti prinsip yang sama seperti bagian 9, dengan pertimbangan tambahan pembatasan akses sesuai UU PDP saat rekaman dipakai sebagai alat bukti.

**Integrasi antar tahap**: data *activity monitoring* dikonsumsi dari domain IT Asset & System Access sebagai bahan audit — Visit Management tidak memiliki kepemilikan atas mekanisme monitoring itu sendiri. **Jalur jika log activity monitoring dari domain IT Asset & System Access tidak tersedia/tidak lengkap saat jadwal audit virtual sudah tiba** (mis. gangguan sistem monitoring): audit virtual tetap dilaksanakan menggunakan video call check-in dan wawancara sebagai bukti minimum, dengan status laporan ditandai "Data Activity Monitoring Belum Lengkap" dan susulan review log dilakukan begitu data tersedia — bukan menunda seluruh audit sampai data monitoring pulih.

---

## 11. Siklus Terpisah: Quality Assurance (QA) Audit

Berbeda dari Visit fisik (bagian 2-10) yang levelnya kunjungan berjadwal untuk memantau **kondisi kerja & kepatuhan** (SOP/APD/kebersihan site), QA Audit levelnya sampling terhadap **kualitas output kerja** itu sendiri dan tidak selalu memerlukan kehadiran fisik.

### 11.1 Pre-Cycle

Metodologi sampling ditetapkan sebagai kebijakan baku: persentase/jumlah sampel per periode per site/kategori pekerjaan, dengan kriteria *scorecard* terstandar (bukan penilaian subjektif) — rekaman panggilan/interaksi untuk tenaga call center/customer service, audit akurasi transaksi untuk kasir/operator retail, checklist kualitas kebersihan untuk cleaning service (kepemilikan detail teknis pada dokumen operasional per lini layanan masing-masing).

### 11.2 Life Cycle

**Actor**: tim QA/Quality khusus — independen dari Garis Komando operasional site, untuk menjaga objektivitas penilaian (berbeda eksplisit dari Site Supervisor yang melakukan Visit fisik pada bagian 5).

**Proses bisnis**: sampel diambil sesuai metodologi (bagian 11.1); setiap sampel dinilai memakai *scorecard* terstandar per kategori pekerjaan; skor dikonsolidasikan per tenaga/site/periode.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Sampel | String (kode unik) | QA-2026-08-000123 | Identitas unik tiap sampel yang diambil |
| Kategori Pekerjaan | Enum | "Call Center", "Kasir Retail", "Cleaning Service" | Menentukan scorecard yang dipakai (bagian 11.1) |
| Skor Kriteria Scorecard (per kriteria) | Numerik (skala tetap, mis. 1-5) | 4 | Jumlah & jenis kriteria bervariasi per kategori pekerjaan |
| Skor Agregat | Numerik (desimal, hasil konsolidasi kriteria, skala 0-100) | 82.5 | Dikonsolidasikan per tenaga/site/periode |
| Periode Penilaian | Tanggal (rentang periode, format bulan-tahun) | Agustus 2026 | Basis pelaporan & tren skor |
| ID Tenaga Dinilai | String (ID karyawan) | EMP-00234 | Rujukan silang ke data induk karyawan |
| ID Penilai QA | String (ID auditor) | QA-AUD-007 | Untuk audit trail objektivitas penilaian |

**Business rule & validasi**: scorecard wajib terstandar per kategori pekerjaan (tidak boleh berbeda kriteria antar-penilai untuk kategori yang sama) — mencegah bias penilaian antar-site/antar-penilai QA.

**Approval Workflow**: hasil skor QA tidak memerlukan approval untuk dicatat; skor yang menjadi dasar tindak lanjut disiplin (bagian 11.3) mengikuti approval proses investigasi pada domain Employee Discipline.

### 11.3 Post-Cycle — Linkage ke Performance, Insentif & Discipline

Skor QA menjadi salah satu komponen Performance Management dan basis Insentif Performa/KPI Non-Sales (kepemilikan proses domain Payroll, Taksonomi Insentif) — sehingga QA Audit bukan sekadar mekanisme pengawasan, melainkan juga input langsung ke kompensasi.

**Business rule**: skor QA di bawah ambang batas berulang menjadi trigger investigasi pada domain Employee Discipline — dibedakan eksplisit dari temuan Visit fisik (bagian 5.3) yang levelnya kondisi/perilaku, bukan kualitas output; kedua jenis temuan (Visit dan QA Audit) dapat berjalan independen dan keduanya sah menjadi dasar investigasi terpisah tanpa saling menggantikan.

**Integrasi antar tahap**: hasil QA Audit dikonsumsi dua arah — ke Performance Management/Payroll (sisi positif, basis insentif) dan ke Employee Discipline (sisi korektif, saat skor konsisten di bawah ambang). **Jalur jika terjadi perbedaan/sengketa antara skor QA dan hasil investigasi Employee Discipline** (mis. skor QA tenaga tertentu konsisten baik namun investigasi Discipline berjalan atas dasar temuan Visit fisik yang berbeda substansi, atau sebaliknya skor QA rendah namun investigasi Discipline tidak menemukan pelanggaran serupa): kedua catatan tetap dipertahankan sebagai dua sumber temuan independen tanpa saling membatalkan satu sama lain — QA Audit menilai kualitas output kerja, sedangkan Discipline menilai pelanggaran perilaku/aturan, sehingga hasil yang tampak bertentangan pada dasarnya mengukur dimensi berbeda. Jika perbedaan tersebut mengindikasikan inkonsistensi metodologi (mis. scorecard QA tidak menangkap pelanggaran yang seharusnya relevan), hal ini dieskalasi bersama ke tim QA/Quality dan domain Employee Discipline untuk peninjauan kriteria scorecard — bukan diselesaikan dengan mengubah salah satu skor/temuan agar selaras dengan yang lain.

---

## 12. Ringkasan Ketentuan Kunci

- Lima kategori visit (Site, Client, Compliance/Audit, Emergency/Ad-hoc, Customer) dibedakan eksplisit karena masing-masing punya trigger, actor, dan tujuan berbeda — Emergency/Ad-hoc melewati sebagian tahap perencanaan terjadwal karena sifatnya reaktif.
- Frekuensi visit rutin tidak boleh lebih jarang dari ambang minimum yang dijanjikan klausul PKS — pelanggaran frekuensi ini sendiri berpotensi menjadi temuan breach SLA.
- Data capture lapangan (geolocation, form terstruktur, foto, tanda tangan digital PIC client) adalah komponen bukti hukum; kunjungan tanpa konfirmasi PIC client tetap tersimpan tapi berstatus "belum dikonfirmasi client" dengan nilai pembuktian lebih lemah.
- Temuan mayor wajib direview pihak selain pelapor (Site Manager/HR Manager) sebelum ditutup — pemisahan pelapor dan penutup untuk menjaga akuntabilitas; temuan terkait keselamatan kritikal tidak dapat ditutup sampai domain K3 mengonfirmasi risiko sudah dimitigasi, terlepas SLA administratif standar.
- Visit Management tetap memegang kepemilikan *originating record* atas temuan yang dirutekan ke domain lain — memantau sampai domain tujuan mengonfirmasi penyelesaian, bukan melepaskan begitu dirutekan.
- QA Audit (kualitas output kerja) berjalan independen dari Visit fisik (kondisi kerja) — keduanya sah menjadi dasar investigasi terpisah tanpa saling menggantikan, dan skor QA yang konsisten di bawah ambang menjadi trigger investigasi disiplin tersendiri.

---

## 13. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Perencanaan Visit | Penjadwalan | Penyusunan kalender per risk level & cluster site | HR Ops/Site Coordinator, Account Manager | Validasi Account Manager | Klausul frekuensi visit | TBD |
| Pelaksanaan Visit | Persiapan | Penyusunan checklist, notifikasi ke site | Penanggung jawab visit | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Pelaksanaan Visit | Data capture lapangan | Check-in geolocation, form terstruktur, foto, tanda tangan digital | Penanggung jawab visit, PIC client | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Pelaksanaan Visit | Temuan & tindak lanjut | Kategorisasi temuan, routing ke domain tujuan, review temuan mayor | Penanggung jawab visit, Site Manager/HR Manager | Approval Site Manager/HR Manager untuk temuan mayor | Klausul SLA/pelanggaran PKS (jika relevan) | TBD |
| Visit Kebutuhan Khusus | Onboarding/Offboarding/Investigasi | Verifikasi kesiapan penempatan, serah terima aset/site, investigasi lapangan | Penanggung jawab visit, PIC client, tim Employee Discipline/K3 (investigasi) | Approval Site Coordinator/Account Manager (onboarding); Approval Account Manager (offboarding); Approval domain Employee Discipline/K3 (investigasi) | Klausul terkait (jika ada, mis. klausul offboarding PKS) | TBD |
| Client Visit | Penjadwalan & pertemuan | Penjadwalan, penetapan agenda, dokumentasi hasil | Account Manager, Legal/Finance | Tidak ada approval terpisah untuk pelaksanaan pertemuan; hasil kesepakatan diteruskan ke Amandemen PKS | Klausul terkait agenda pertemuan | TBD |
| Reporting & Analytics | Evaluasi compliance & tren | Dashboard compliance rate, tren temuan per site | Manpower Planning, Account Manager | Tidak ada approval untuk penyajian dashboard; approval pada keputusan masuk daftar prioritas evaluasi | Tidak berlaku langsung | TBD |
| Virtual Visit (WFH) | Audit virtual | Video call check-in, review activity monitoring | Penanggung jawab visit | Tidak ada approval untuk pelaksanaan audit; Approval Site Manager/HR Manager untuk temuan mayor (mengikuti bagian 5.3) | Klausul WFH/remote | TBD |
| QA Audit | Sampling & scoring | Pengambilan sampel, penilaian scorecard, konsolidasi skor | Tim QA independen | Tidak ada approval terpisah; skor rendah berulang memicu investigasi Discipline | Tidak berlaku langsung | TBD |
