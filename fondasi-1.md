# Garis Komando — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Garis Komando mendefinisikan hierarki approval yang menjadi basis seluruh alur persetujuan lintas domain lain dalam siklus employee — pengajuan cuti, mutasi, surat peringatan, amandemen kontrak kerja sama, penerbitan dokumen penempatan, dan setiap titik keputusan lain yang memerlukan otorisasi berjenjang. Berbeda dari domain lain yang punya trigger dan output transaksionalnya sendiri, domain ini bersifat **kerangka rujukan** yang dikonsumsi oleh hampir seluruh proses bisnis lain; elaborasi di sini berfokus pada bagaimana kerangka itu sendiri dibentuk, dikonfigurasi, dan dieksekusi sebagai layanan bersama (*shared service*) bagi proses-proses tersebut.

Ruang lingkup meliputi: struktur hierarki baku dan dualitas jalur struktural vs fungsional, matrix approval berdasarkan jenis keputusan dan nilai/dampak, mekanisme transaksi approval generik (termasuk pola sekuensial vs paralel), penanganan pengecualian (delegasi saat approver berhalangan, eskalasi saat approver tidak merespons), perbedaan garis komando antar-legal-entity, governance atas konfigurasi matrix approval itu sendiri, dan audit trail atas seluruh keputusan yang diambil.

---

## 2. Struktur Hierarki Baku & Dualitas Jalur Struktural vs Fungsional

### 2.1 Struktur Hierarki Umum

Jalur eskalasi baku: **Employee → Direct Supervisor/Site Supervisor (khusus tenaga lapangan) → Department Head/Site Manager → HR (sesuai fungsi: HR Ops, HRBP, HR Manager) → Finance/Payroll (untuk keputusan berdampak finansial) → Direksi (untuk keputusan strategis/berdampak besar)**. Jalur ini adalah rujukan default; setiap domain yang memanggil approval dari kerangka ini memetakan jenis keputusannya ke tingkat mana dalam hierarki ini yang relevan (lihat matrix pada bagian 3).

**Pre-Cycle**: struktur hierarki baku ini ditetapkan sebagai bagian desain organisasi awal perusahaan outsourcing (struktur jabatan, garis pelaporan formal) sebelum sistem approval apa pun beroperasi; perubahannya mengikuti proses formal pada bagian 5 (Governance Konfigurasi), bukan diubah ad-hoc oleh domain pemanggil mana pun.

**Life Cycle**: dipakai sehari-hari sebagai rujukan pemetaan otomatis oleh sistem setiap kali transaksi approval dari domain manapun dibuka (bagian 3.2) — pemohon maupun approver tidak menentukan sendiri jalurnya, sistem yang menentukan berdasarkan hierarki baku ini dikombinasikan dengan matrix jenis keputusan (bagian 3.1) dan legal entity pemohon (bagian 4).

**Post-Cycle**: kesesuaian hierarki dengan struktur organisasi aktual dievaluasi berkala (mis. setiap kali ada perubahan struktur organisasi atau pergantian pejabat) sebagai bagian Governance Konfigurasi (bagian 5); hierarki yang sudah usang (mis. merujuk posisi yang telah dihapus) wajib direvisi lewat proses tersebut, bukan dibiarkan sebagai rujukan yang tidak lagi akurat bagi transaksi approval yang berjalan.

### 2.2 Dualitas Garis Komando Struktural vs Fungsional

Untuk karyawan penempatan yang bertugas di site client, berlaku dua jalur pelaporan yang berjalan bersamaan namun untuk ranah keputusan yang berbeda:

- **Garis Struktural** — jalur organisasi internal perusahaan outsourcing (Site Supervisor → Site Manager → HR → Direksi), berwenang atas keputusan **administratif-HR** (cuti, mutasi, disiplin, kontrak kerja) dan **finansial** (payroll, kompensasi).
- **Garis Fungsional** — site supervisor secara operasional harian berada di bawah arahan PIC client (client mengarahkan penugasan harian, target kerja, dan supervisi teknis lapangan), namun tetap under HR perusahaan outsourcing secara administratif.

**Business rule**: keputusan yang berdampak hubungan kerja (cuti, disiplin, mutasi, PHK) **wajib** melalui Garis Struktural meski secara operasional sehari-hari karyawan diarahkan client — PIC client dapat mengajukan permintaan/observasi (misal permintaan replacement, laporan pelanggaran) tetapi keputusan final dan approval formal tetap melalui Garis Struktural sesuai matrix pada bagian 3, sejalan dengan penegasan bahwa hubungan kerja tetap antara perusahaan outsourcing dengan karyawan dan bukan client.

**Pre-Cycle**: pembagian dua jalur ini ditetapkan sejak PKS dengan client disepakati — ruang lingkup arahan operasional harian yang boleh diberikan PIC client (Garis Fungsional) digariskan eksplisit dalam klausul PKS, terpisah dari kewenangan administratif-HR yang tetap berada pada Garis Struktural, bukan ditentukan belakangan secara kasus per kasus.

**Life Cycle**: site supervisor menjalankan kedua jalur ini bersamaan setiap hari kerja — menerima arahan teknis/operasional dari PIC client sekaligus tetap melapor administratif-HR ke Garis Struktural; setiap permintaan/observasi dari PIC client yang berpotensi berdampak hubungan kerja diteruskan sebagai pengajuan formal ke Garis Struktural, bukan dieksekusi langsung oleh site supervisor semata atas dasar arahan client.

**Post-Cycle**: kejelasan batas dua jalur ini dievaluasi berkala berdasarkan pola dispute/keluhan yang tercatat pada Audit Trail (bagian 6) — pola berulang PIC client mengarahkan keputusan yang seharusnya administratif-HR menjadi bahan revisi klausul PKS atau penegasan ulang batas kewenangan lewat Governance Konfigurasi (bagian 5).

**Integrasi antar tahap**: pembagian dua jalur ini menjadi rujukan bagi seluruh domain yang melibatkan client sebagai pihak yang memberi observasi/permintaan namun bukan approver formal — termasuk permintaan replacement tenaga kerja, endorsement data lembur untuk keperluan billing tambahan, dan pengajuan lain melalui Client Portal yang tetap memerlukan approval internal Garis Struktural sebelum dieksekusi. **Jalur jika PIC client mengeksekusi/mengarahkan tindakan operasional sebelum approval formal Garis Struktural selesai** (mis. PIC client langsung memberhentikan atau menarik tenaga kerja dari site, atau mengubah penugasan secara signifikan tanpa menunggu approval): tindakan tersebut dianggap di luar kewenangan client dan tidak mengubah status hubungan kerja karyawan secara sistem — status kepegawaian tetap berjalan sampai approval formal Garis Struktural diselesaikan (setuju/tolak), sementara insiden ini wajib dieskalasi ke HR Manager/Legal sebagai potensi pelanggaran ketentuan PKS untuk ditindaklanjuti dengan client, dan dicatat sebagai kejadian eksepsi pada Audit Trail (bagian 6). **Jalur jika dua domain/jalur mengklaim otoritas keputusan yang sama secara bersamaan** (mis. Garis Fungsional/client dan Garis Struktural sama-sama mengklaim berhak memutuskan mutasi atau penghentian penugasan seorang tenaga kerja): keputusan yang berlaku sah secara sistem adalah keputusan Garis Struktural sesuai matrix (bagian 3.1) — klaim dari jalur lain diperlakukan sebagai observasi/permintaan yang tetap harus melalui pengajuan approval formal, bukan diakui setara sebagai keputusan final.

---

## 3. Matrix Approval & Transaksi Approval Generik

### 3.1 Matrix Berdasarkan Jenis Keputusan & Nilai/Dampak

Setiap jenis keputusan dipetakan ke jumlah level approval dan approver yang berwenang berdasarkan dua dimensi: **jenis keputusan** (operasional harian, administratif HR, finansial) dan **nilai/dampak** (rendah–tinggi). Sebagai baku:

| Jenis Keputusan | Contoh | Level Approval Minimal | Approver Tertinggi |
|---|---|---|---|
| Operasional harian, dampak rendah | Cuti biasa, izin harian | 1 level | Direct Supervisor/Site Supervisor |
| Administratif HR, dampak menengah | Mutasi antar-site, perubahan data organisasi | 2 level | Site Manager/Department Head, HR Ops |
| Administratif HR, dampak tinggi | Surat Peringatan, PHK | Multi-level | HR Manager, Legal, Direksi |
| Finansial, dampak menengah | Pengajuan Cash Advance dalam ambang normal | 2 level | Site Supervisor, Finance/Payroll |
| Finansial/strategis, dampak tinggi | Amandemen PKS berdampak budget, persetujuan client bernilai tinggi | Multi-level dengan maker-checker | Direksi/BOD |

Matrix rinci per jenis transaksi didefinisikan di masing-masing domain pemilik proses (misal ambang nominal Cash Advance oleh domain Payroll, ambang nilai amandemen PKS oleh domain PKS); domain ini menyediakan **kerangka tingkatan & urutan approver**-nya, bukan menetapkan ambang nominal spesifik per transaksi.

### 3.2 Life-Cycle Umum: Satu Transaksi Approval

**Pre-Cycle**:
- Pemohon (karyawan/HR Ops/fungsi lain sesuai domain pemicu) memastikan data pendukung pengajuan sudah lengkap sesuai syarat domain asal (misal dokumen pendukung cuti, dokumen amandemen kontrak) sebelum transaksi approval dibuka.
- Sistem menentukan jalur approval yang berlaku berdasarkan matrix (bagian 3.1) dan legal entity pemohon (bagian 4).

**Life Cycle**:

**Actor**: Pemohon, approver di tiap level sesuai matrix, sistem (routing & tracking).

**Proses bisnis**:
1. Pengajuan masuk ke approver level pertama sesuai matrix.
2. Approver meninjau pengajuan melalui sub-langkah pemeriksaan berikut, sebelum keputusannya jatuh ke salah satu skenario bernama yang dijabarkan pada Approval Workflow di bawah:
   - **2a. Pemeriksaan kelengkapan** — approver memastikan data/dokumen pendukung sesuai syarat domain asal sudah lengkap sebelum menilai substansi; pengajuan dengan kelengkapan tidak memenuhi syarat langsung diarahkan ke **Skenario Perlu Revisi** tanpa perlu masuk penilaian substansi.
   - **2b. Penilaian substansi** — approver menilai kelayakan pengajuan sesuai kriteria domain asal (mis. kecukupan saldo cuti, kesesuaian anggaran mutasi); hasil penilaian ini menentukan apakah keputusan jatuh ke **Skenario Setuju** atau **Skenario Tolak**.
3. Untuk approval **sekuensial** (default): level berikutnya baru menerima pengajuan setelah level sebelumnya menyetujui; jika level manapun menolak, transaksi berakhir seketika tanpa diteruskan ke level berikutnya.
4. Untuk approval **paralel** (dikonfigurasi khusus untuk transaksi yang memerlukan verifikasi lintas-fungsi tanpa saling menunggu, misal maker-checker Finance/Legal pada persetujuan client bernilai tinggi): seluruh approver paralel menerima pengajuan bersamaan; transaksi baru dianggap disetujui penuh setelah **seluruh** approver paralel menyetujui — **jika salah satu approver paralel menolak**, transaksi berakhir dengan status ditolak meski approver paralel lainnya sudah menyetujui (penolakan satu pihak membatalkan keseluruhan transaksi, bukan hanya bagiannya); **jika salah satu approver paralel meminta revisi** sementara yang lain sudah menyetujui, transaksi ditahan berstatus "Perlu Revisi" dan approver yang sudah menyetujui tidak perlu menyetujui ulang setelah revisi selama revisi tidak mengubah substansi yang sudah mereka nilai.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Transaksi Approval | String, prefix tetap + tahun + running number | APR-2026-000451 | ID unik transaksi approval; dirujuk balik oleh domain asal |
| Domain/Jenis Keputusan Asal | Enum | "Cuti", "Mutasi Antar-Site", "Amandemen PKS" | Menentukan domain pemilik proses & kriteria substansi 2b |
| Pemohon | String, kombinasi Employee ID + Nama | EMP-00123 — Budi Santoso | Karyawan/fungsi yang mengajukan transaksi |
| Daftar Approver per Level | Array of object {level, approver ID, tipe} | [{level:1, approverID:"EMP-00080", tipe:"sekuensial"}] | Ditentukan sistem berdasar matrix (3.1) & legal entity (bagian 4) |
| Status Tiap Level | Enum | "Pending", "Setuju", "Tolak", "Perlu Revisi" | Berubah sesuai skenario Approval Workflow di bawah |
| Catatan/Alasan Keputusan | Teks bebas maks. 1000 karakter, atau kode kategori alasan baku | "REJ-03 — Anggaran mutasi tidak mencukupi" | Wajib diisi untuk Skenario Tolak |
| Waktu Submit | Timestamp (ISO 8601, dengan zona waktu) | 2026-08-17T09:12:00+07:00 | Dasar perhitungan SLA (bagian 3.4) |
| Waktu Keputusan Tiap Level | Timestamp (ISO 8601, dengan zona waktu) atau null | 2026-08-17T14:30:00+07:00 | Null selama status level masih pending |

**Business rule & validasi**: keputusan **reject** wajib disertai alasan tertulis; keputusan yang melibatkan dampak terhadap hubungan kerja (SP, PHK) wajib merujuk pasal yang relevan pada peraturan ketenagakerjaan yang berlaku (Undang-Undang Ketenagakerjaan berikut perubahannya melalui Undang-Undang Cipta Kerja, dan Peraturan Pemerintah Nomor 35 Tahun 2021 untuk kategori PHK dan perhitungan kompensasi) sebagai dasar keputusan, bukan sekadar keputusan administratif tanpa rujukan.

**Approval Workflow**: mengikuti matrix pada bagian 3.1; SLA per level mengikuti Sub-Siklus SLA Respons Default (bagian 3.4) kecuali domain pemanggil sudah menetapkan SLA spesifiknya sendiri. Keputusan approver di tiap level jatuh pada salah satu skenario berikut:
  - **Skenario Setuju** — pengajuan lolos pemeriksaan kelengkapan (2a) dan dinilai layak pada penilaian substansi (2b); transaksi lanjut ke approver level berikutnya sesuai matrix, atau berstatus selesai/disetujui jika ini level terakhir.
  - **Skenario Tolak** — pengajuan dinilai tidak layak pada penilaian substansi (2b); transaksi berakhir seketika dengan status ditolak, alasan wajib diisi dari kategori baku sesuai domain asal — untuk approval sekuensial, level berikutnya tidak menerima pengajuan ini.
  - **Skenario Perlu Revisi** — kelengkapan data/dokumen tidak memenuhi syarat pada pemeriksaan 2a, atau substansi pada dasarnya dapat diterima namun perlu penyesuaian minor; pengajuan dikembalikan ke pemohon untuk perbaikan, siklus mengulang dari Pre-Cycle — bukan dihitung sebagai penolakan; pemohon dapat mengajukan ulang tanpa membuka transaksi baru selama masih dalam batas waktu yang wajar.
  - **Skenario Eksepsi/Force Majeure** — berlaku hanya untuk kategori keputusan kritikal/darurat yang secara eksplisit ditandai oleh domain pemanggil (misal approval K3 saat insiden, atau lockdown akses sistem darurat); kewenangan override/emergency approval memungkinkan keputusan diambil di luar urutan level normal matrix (mis. langsung oleh pejabat tertinggi yang tersedia tanpa menunggu level di bawahnya), dengan syarat keputusan tersebut wajib dikonfirmasi ulang secara formal oleh approver level yang seharusnya dalam waktu wajar setelah kondisi darurat berakhir, dan seluruh penggunaan jalur ini dicatat eksplisit sebagai keputusan eksepsi (bukan disamakan dengan keputusan jalur normal) pada Audit Trail (bagian 6). Di luar kategori yang ditandai eksplisit ini, tidak ada jalur pintas yang melewati level approval yang sudah ditetapkan matrix.

**Notifikasi/trigger**: notifikasi ke approver level berjalan saat pengajuan masuk ke levelnya; notifikasi ke pemohon di setiap perubahan status (disetujui/ditolak/perlu revisi); notifikasi ke approver level berikutnya begitu level sebelumnya menyetujui (pola sekuensial).

**Dokumen/output**: catatan keputusan approval lengkap per level, status akhir transaksi (disetujui/ditolak), rujukan balik ke transaksi domain asal.

**Integrasi antar tahap**: kerangka ini dipanggil sebagai layanan bersama oleh domain manapun yang memerlukan approval berjenjang — bukan proses yang berjalan sendiri tanpa pemicu dari domain lain. **Jalur jika domain pemanggil salah memetakan jenis keputusannya ke matrix** (mis. keputusan yang seharusnya kategori finansial/strategis dipetakan sebagai operasional harian, sehingga levelnya jadi terlalu rendah): dianggap kesalahan konfigurasi integrasi, bukan kesalahan transaksi individual — wajib dieskalasi ke administrator konfigurasi (bagian 5) untuk mengoreksi pemetaan domain tersebut, bukan cukup dikoreksi manual pada satu transaksi saja karena kesalahan pemetaan akan berulang pada transaksi berikutnya dari domain yang sama.

**Pertimbangan Non-Fungsional**: kerangka ini adalah titik tunggal yang dilalui hampir seluruh proses approval lintas domain — ketersediaannya kritikal (downtime di sini memblokir seluruh transaksi hilir yang menunggu approval, bukan hanya satu domain) dan wajib mampu menangani volume transaksi tinggi secara bersamaan dari banyak domain pemanggil tanpa antrian yang menumpuk melebihi SLA yang dijanjikan.

**Post-Cycle**: hasil akhir transaksi approval (disetujui/ditolak) dikembalikan ke domain asal untuk melanjutkan proses bisnisnya (misal cuti yang disetujui memicu pemotongan saldo cuti pada domain Time Management); seluruh riwayat keputusan tersimpan permanen sebagai bagian Audit Trail Persetujuan (bagian 6).

### 3.3 Sub-Siklus: Delegasi Wewenang (Delegation of Authority/DOA) saat Approver Berhalangan

**Pre-Cycle**: approver mendaftarkan periode ketidakhadiran terjadwal (cuti, dinas luar) beserta penunjukan pengganti (delegate) sebelum periode tersebut dimulai; untuk ketidakhadiran mendadak (sakit tanpa pemberitahuan), sistem memakai aturan default delegasi ke atasan berikutnya dalam hierarki tanpa perlu pendaftaran manual.

**Life Cycle**:
- **Actor**: approver asli, delegate yang ditunjuk, sistem (routing otomatis).
- **Proses bisnis**: begitu ada pengajuan approval yang seharusnya masuk ke approver yang sedang berhalangan (sesuai periode terdaftar), sistem otomatis mengalihkan ke delegate yang ditunjuk atau ke atasan berikutnya jika tidak ada delegate terdaftar.
- **Data/field**:

  | Nama Field | Tipe Data | Contoh Nilai | Keterangan |
  |---|---|---|---|
  | ID Pendaftaran Delegasi | String, prefix tetap + running number | DOA-2026-000078 | ID unik pendaftaran delegasi |
  | Approver Asli | String, Employee ID + Nama | EMP-00080 — Siti Aminah | Approver yang mendaftarkan periode berhalangan |
  | Delegate yang Ditunjuk | String, Employee ID + Nama | EMP-00095 — Rudi Hartono | Kosong/null jika memakai aturan default eskalasi ke atasan |
  | Periode Delegasi | Pasangan tanggal (ISO 8601) mulai–selesai | 2026-08-20 s.d. 2026-08-27 | Dipakai sistem untuk menentukan jendela pengalihan aktif |
  | Jenis Pendaftaran | Enum | "Terjadwal", "Mendadak/Default" | Menentukan apakah lewat pendaftaran manual atau aturan default |
  | Status Approval Delegasi | Enum | "Otomatis Berlaku", "Menunggu HR Manager", "Disetujui", "Ditolak", "Perlu Revisi" | Hanya relevan untuk delegasi jangka panjang (>30 hari) |
  | Cakupan Kewenangan | Enum/multi-select | "Seluruh Jenis Keputusan", "Operasional Harian Saja" | Batas jenis keputusan yang boleh diputuskan delegate |

- **Business rule**: delegate hanya dapat mengambil keputusan dalam batas kewenangan yang sama seperti approver asli untuk periode delegasi berjalan; delegasi tidak dapat dialihkan berjenjang lebih dari satu tingkat tanpa approval tambahan dari HR Manager/System Admin.
- **Approval Workflow**: pendaftaran delegate oleh approver tidak memerlukan approval tambahan untuk delegasi rutin (cuti terjadwal); untuk delegasi jangka panjang (di atas ambang waktu tertentu, misal 30 hari) memerlukan approval HR Manager.
  - **Skenario Setuju** — HR Manager menilai delegate yang ditunjuk memiliki kewenangan/level yang sesuai (tidak menunjuk bawahan sendiri sebagai delegate untuk keputusan yang seharusnya di atas levelnya); delegasi jangka panjang berlaku efektif sesuai periode yang diajukan.
  - **Skenario Tolak** — delegate yang ditunjuk dinilai tidak memenuhi syarat kewenangan (mis. levelnya di bawah ambang minimal untuk jenis keputusan yang didelegasikan); approver asli diminta menunjuk delegate lain, sementara pengajuan yang masuk selama periode tersebut mengikuti aturan default (eskalasi ke atasan berikutnya, bagian 3.4) sampai delegate pengganti disetujui.
  - **Skenario Perlu Revisi** — delegate yang ditunjuk pada dasarnya memenuhi syarat, namun cakupan kewenangan yang diajukan perlu dipersempit (mis. delegate hanya diberi kewenangan untuk jenis keputusan operasional harian, bukan seluruh jenis keputusan approver asli); HR Manager mengembalikan pengajuan dengan catatan cakupan yang perlu disesuaikan.

**Notifikasi/trigger**: notifikasi ke delegate saat menerima pengajuan yang dialihkan; notifikasi ke approver asli saat kembali aktif mengenai transaksi yang sudah diputuskan delegate selama periode berhalangan.

**Dokumen/output**: log delegasi (periode, approver asli, delegate, transaksi yang diputuskan atas nama delegasi).

**Integrasi antar tahap**: status delegasi aktif dikonsumsi oleh mesin routing Transaksi Approval Generik (bagian 3.2) setiap kali pengajuan baru dibuka, dan berinteraksi dengan Sub-Siklus SLA (bagian 3.4) — pengajuan yang dialihkan ke delegate tetap tunduk pada SLA yang sama seperti approver asli. **Jalur jika pendaftaran delegasi tidak ter-update tepat waktu** (mis. approver sudah mulai cuti namun pendaftaran delegate baru diinput setelah pengajuan pertama masuk): pengajuan yang terlanjur masuk ke approver yang berhalangan sebelum pendaftaran delegasi aktif tetap mengikuti aturan default SLA (bagian 3.4) — dieskalasi otomatis ke atasan berikutnya jika tidak direspons dalam tenggat, tidak menunggu pendaftaran delegasi menyusul; begitu pendaftaran delegasi aktif, hanya pengajuan berikutnya yang mengikuti jalur delegasi tersebut. **Jalur jika periode delegasi tumpang tindih dengan delegasi lain yang masih aktif untuk approver yang sama**: sistem menolak pendaftaran delegasi baru yang tumpang tindih dan meminta approver asli menyesuaikan periode terlebih dahulu, mencegah ambiguitas siapa delegate yang sah pada satu titik waktu.

**Post-Cycle**: log delegasi menjadi bagian audit trail (bagian 6), ditandai eksplisit sebagai keputusan yang diambil lewat jalur delegasi, bukan approver asli — relevan saat terjadi dispute mengenai keabsahan keputusan yang diambil selama periode delegasi.

### 3.4 Sub-Siklus: SLA Respons Approval Default & Auto-Escalate

Berbeda dari Delegasi Wewenang (dipicu approver **berhalangan**), sub-siklus ini menangani kondisi approver **ada tetapi tidak kunjung merespons** dalam waktu wajar.

**Pre-Cycle**: target waktu default ditetapkan per jenis keputusan mengikuti matrix (bagian 3.1) — operasional harian: 1×24 jam, administratif HR: 2×24 jam, finansial/strategis: 3×24 jam (dapat dikonfigurasi ulang sesuai kebijakan internal); domain pemanggil yang sudah memiliki SLA spesifik sendiri (misal SLA Approval MPR pada domain rekrutmen, SLA Visit Management, SLA Provisioning akses sistem) memakai SLA spesifiknya sebagai pengecualian dari default ini.

**Life Cycle**:
- **Actor**: sistem (monitoring otomatis), approver level berjalan, approver level berikutnya (penerima eskalasi).
- **Proses bisnis**: sistem memantau durasi sejak pengajuan masuk ke suatu level approval; jika durasi melampaui SLA target tanpa keputusan, sistem otomatis mengeskalasi pengajuan ke atasan berikutnya dalam hierarki, dengan pengajuan awal tetap tercatat sebagai *pending* di level asal.
- **Data/field**:

  | Nama Field | Tipe Data | Contoh Nilai | Keterangan |
  |---|---|---|---|
  | ID Transaksi Approval Terkait | String, rujukan ke ID pada bagian 3.2 | APR-2026-000451 | Menghubungkan monitoring SLA ke transaksi approval asal |
  | Target SLA | Numerik (jumlah hari kerja) | 2 | Ditentukan jenis keputusan (3.1) atau SLA spesifik domain pemanggil |
  | Waktu Masuk ke Level | Timestamp (ISO 8601) | 2026-08-17T09:12:00+07:00 | Dasar hitung mundur SLA level berjalan |
  | Tenggat SLA | Timestamp (ISO 8601), disesuaikan Kalender Hari Libur | 2026-08-19T17:00:00+07:00 | Mundur otomatis ke hari kerja berikutnya jika jatuh di hari libur |
  | Status SLA | Enum | "Dalam SLA", "Mendekati Tenggat", "Terlambat", "Dieskalasi" | Diperbarui otomatis oleh sistem monitoring |
  | Riwayat Eskalasi | Array of object {tingkat, approver, waktu eskalasi} | [{tingkat:1, approver:"EMP-00080", waktu:"2026-08-19T17:01:00+07:00"}] | Mencatat seluruh eskalasi berjenjang, bukan hanya yang terakhir |

- **Business rule**: perhitungan hari mengikuti Kalender Hari Libur yang berlaku — SLA dihitung hari kerja, bukan kalender penuh; jika tenggat jatuh tepat di hari libur, otomatis mundur ke hari kerja berikutnya — **kecuali** untuk approval kategori kritikal/darurat (K3, lockdown akses sistem) yang tetap dihitung kalender penuh tanpa jeda hari libur.
- **Approval Workflow**: eskalasi bersifat otomatis tanpa perlu permintaan manual dari pemohon; approver yang menerima eskalasi dapat tetap memutuskan meski approver asli belum merespons, dan keputusan eskalasi berlaku sah sebagai keputusan final level tersebut.
  - **Skenario Eskalasi Berhasil** — approver pengganti (atasan berikutnya) merespons dalam SLA-nya sendiri; keputusan yang diambil berlaku sah dan dicatat sebagai keputusan jalur eskalasi (bukan jalur normal) pada Audit Trail (bagian 6).
  - **Skenario Eskalasi Berulang** — approver pengganti juga tidak merespons dalam SLA-nya; sistem mengeskalasi kembali ke tingkat berikutnya dalam hierarki, dengan seluruh riwayat eskalasi (bukan hanya eskalasi terakhir) tetap tercatat sebagai jejak lengkap.
  - **Skenario Puncak Hierarki Tidak Merespons** — eskalasi mencapai Direksi/BOD (tidak ada tingkat lebih tinggi untuk dieskalasi) dan tetap tidak ada respons dalam SLA; pengajuan ditandai status "Eskalasi Maksimal — Memerlukan Intervensi Manual", dan notifikasi khusus dikirim ke HR Manager/System Admin untuk tindak lanjut manual di luar mekanisme eskalasi otomatis — bukan dianggap disetujui/ditolak otomatis karena tidak ada respons.

**Notifikasi/trigger**: reminder ke approver mendekati tenggat SLA (misal H-1 sebelum SLA terlampaui); notifikasi eskalasi ke approver berikutnya beserta pemberitahuan ke approver asli bahwa pengajuan sudah dieskalasi.

**Dokumen/output**: log SLA per transaksi (waktu submit, tenggat, waktu keputusan aktual, status tepat waktu/terlambat/dieskalasi).

**Integrasi antar tahap**: sub-siklus ini berjalan berdampingan dengan Delegasi Wewenang (bagian 3.3) — pengajuan yang sudah dialihkan ke delegate tetap dipantau SLA-nya seperti approver asli — dan hasil eskalasinya menjadi bagian riwayat transaksi pada Transaksi Approval Generik (bagian 3.2). **Jalur jika SLA breach tidak terdeteksi sistem** (mis. gangguan pada mesin monitoring/scheduler sehingga tenggat terlampaui tanpa eskalasi otomatis terpicu): begitu gangguan terdeteksi (baik lewat pemulihan sistem maupun laporan manual pemohon yang belum menerima keputusan), sistem wajib menjalankan rekonsiliasi retroaktif — menghitung ulang seluruh pengajuan yang seharusnya sudah dieskalasi selama periode gangguan dan mengeskalasinya segera, dengan keterlambatan deteksi itu sendiri dicatat sebagai insiden pada Audit Trail (bagian 6), bukan dibiarkan sebagai celah yang membuat pengajuan tertahan tanpa batas waktu di level asal.

**Post-Cycle**: data keterlambatan/eskalasi per approver terakumulasi sebagai metrik kepatuhan SLA internal, dapat dipakai sebagai bahan evaluasi kinerja approver dalam proses manajemen kinerja.

---

## 4. Perbedaan Garis Komando per Legal Entity

Tiap legal entity (PT) dalam holding memiliki struktur direksi dan fungsi HR-nya sendiri; matrix approval pada bagian 3.1 diterapkan **per legal entity** — approver level tertinggi (Direksi) berbeda personelnya antar-PT meski jenis keputusan dan levelnya sama. Untuk keputusan yang melibatkan kebijakan grup terpusat (misal kebijakan kompensasi/benefit lintas-PT), jalur approval memerlukan **konfirmasi tambahan dari HR Corporate/Direksi Holding** di luar approval level PT masing-masing — kombinasi ini ditetapkan eksplisit di governance konfigurasi (bagian 5), bukan diasumsikan berlaku otomatis.

**Pre-Cycle**: struktur direksi dan fungsi HR per legal entity ditetapkan pada saat pendirian/registrasi PT yang bersangkutan, kemudian didaftarkan sebagai data referensi legal entity pada Governance Konfigurasi (bagian 5) — pendaftaran ini menjadi prasyarat sebelum matrix approval dapat dipetakan secara spesifik untuk entitas tersebut.

**Life Cycle**: setiap transaksi approval yang dibuka (bagian 3.2) memakai struktur direksi/HR legal entity pemohon sebagai rujukan approver tertinggi sehari-hari; untuk keputusan kebijakan grup terpusat, jalur otomatis ditambah konfirmasi HR Corporate/Direksi Holding sesuai kombinasi yang sudah ditetapkan pada governance konfigurasi, tanpa perlu ditentukan ulang per transaksi.

**Post-Cycle**: perbedaan struktur antar-legal-entity ditinjau ulang secara berkala saat terjadi perubahan struktur direksi/HR di salah satu entitas, atau saat transaksi lintas-entitas berulang kali menemui perbedaan level approval yang signifikan antar-entitas — hasil tinjauan menjadi masukan revisi matrix per entitas lewat Governance Konfigurasi (bagian 5).

**Integrasi antar tahap**: pemetaan legal entity per transaksi mengikuti Data Organisasi karyawan yang bersangkutan (rujukan pada domain Employee Data Governance); untuk transaksi lintas-entitas (misal sub-rogasi/pengalihan karyawan antar-legal-entity), approval memerlukan konfirmasi dari kedua legal entity yang terlibat, bukan hanya salah satunya. **Jalur jika kedua legal entity punya level approval berbeda untuk jenis keputusan yang sama** (mis. entitas A mensyaratkan 2 level, entitas B mensyaratkan 3 level untuk keputusan setara): transaksi lintas-entitas wajib mengikuti level approval **tertinggi** dari kedua entitas yang terlibat, bukan level terendah — mencegah salah satu entitas "melewati" kontrol internal entitas lain lewat transaksi lintas-entitas.

---

## 5. Sub-Siklus: Governance Konfigurasi Approval Workflow

Matrix approval pada bagian 3.1 bukan nilai statis — ia adalah konfigurasi yang dapat berubah mengikuti perubahan struktur organisasi, dan perubahan tersebut sendiri memerlukan governance agar tidak menimbulkan celah atau kebuntuan approval.

**Pre-Cycle**: perubahan struktur organisasi (penambahan/penghapusan posisi approver, perubahan hierarki pelaporan) teridentifikasi sebagai pemicu kebutuhan revisi matrix.

**Life Cycle**:
- **Actor**: administrator konfigurasi (HR Manager/System Admin), Direksi (approval perubahan signifikan).
- **Proses bisnis**:
  1. Administrator konfigurasi mengajukan perubahan matrix (menambah/mengubah level approval, mengganti approver untuk suatu jenis keputusan).
  2. Sistem menjalankan validasi otomatis terhadap perubahan yang diajukan, melalui sub-langkah:
     - **2a. Pemeriksaan approval gap** — sistem memeriksa apakah perubahan menciptakan tahapan dalam suatu jenis keputusan yang tidak punya penanggung jawab approver (mis. level dihapus tanpa penggantinya ditetapkan).
     - **2b. Pemeriksaan approval loop** — sistem memeriksa apakah perubahan menciptakan kondisi approver A menunggu approver B yang pada gilirannya menunggu approver A, sehingga transaksi tidak pernah dapat diselesaikan.
  3. Jika validasi (2a-2b) lolos dan perubahan tergolong signifikan (mengubah level approval tertinggi atau menambah/mengurangi jumlah level), perubahan memerlukan approval Direksi sebelum diterapkan; perubahan minor (mis. penggantian personel approver tanpa mengubah struktur level) dapat diterapkan langsung oleh administrator konfigurasi.
  4. Perubahan yang disetujui diterapkan dengan versi baru; versi lama tetap tersimpan sebagai riwayat.

**Data/field**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Versi Matrix | String/numerik dengan skema versi | v2026.3 | Identitas unik versi matrix approval yang berlaku |
| Tanggal Berlaku | Tanggal (ISO 8601) | 2026-09-01 | Tanggal efektif versi matrix mulai berlaku |
| Jenis Keputusan Terdampak | Enum/multi-select | "Mutasi Antar-Site", "PHK" | Jenis keputusan yang levelnya berubah pada versi ini |
| Level & Approver Sebelum/Sesudah | Object pasangan {before, after} | before: 2 level (Site Manager, HR Ops); after: 3 level (+Legal) | Menunjukkan delta perubahan untuk keperluan audit |
| Hasil Validasi Approval Gap (2a) | Enum | "Lolos", "Gagal — Gap Terdeteksi pada Level 2" | Hasil pemeriksaan otomatis sub-langkah 2a |
| Hasil Validasi Approval Loop (2b) | Enum | "Lolos", "Gagal — Loop Approver A/B Terdeteksi" | Hasil pemeriksaan otomatis sub-langkah 2b |
| Status Approval Perubahan | Enum | "Diterapkan Langsung (Minor)", "Menunggu Direksi", "Disetujui", "Ditolak", "Perlu Revisi" | Hanya relevan untuk perubahan signifikan |

**Business rule & validasi**: perubahan matrix yang gagal validasi approval gap/loop wajib ditolak sistem sebelum sampai ke tahap approval Direksi — mencegah konfigurasi cacat diterapkan meski sudah disetujui manusia yang mungkin tidak menyadari implikasi teknisnya.

**Approval Workflow**: perubahan signifikan memerlukan approval Direksi; perubahan minor cukup oleh administrator konfigurasi tanpa approval berjenjang tambahan — pembedaan signifikan/minor sendiri ditetapkan sebagai kebijakan baku yang hanya dapat diubah lewat proses ini juga (self-referential governance).
- **Skenario Setuju** — Direksi menyetujui perubahan signifikan yang diajukan; versi matrix baru diterapkan efektif sesuai tanggal yang ditetapkan.
- **Skenario Tolak** — Direksi menilai perubahan berisiko (mis. mengurangi level approval untuk keputusan berdampak tinggi tanpa mitigasi yang memadai); perubahan ditolak dan matrix versi berjalan tetap berlaku, dengan alasan penolakan dicatat sebagai bagian log validasi.
- **Skenario Perlu Revisi** — perubahan disetujui secara prinsip tapi implementasinya perlu disesuaikan (mis. approver pengganti yang diusulkan perlu dilengkapi dengan delegate cadangan agar tidak menimbulkan celah tunggal/*single point of failure*); administrator konfigurasi melengkapi sebelum diajukan ulang ke Direksi tanpa mengulang seluruh proses validasi otomatis dari awal.

**Notifikasi/trigger**: notifikasi ke seluruh approver yang terdampak perubahan matrix begitu versi baru berlaku efektif.

**Dokumen/output**: riwayat versi matrix approval, log validasi approval gap/loop per perubahan yang diajukan (termasuk yang ditolak).

**Integrasi antar tahap**: konfigurasi matrix ini berdampak lintas seluruh domain pemanggil (bagian 3.2) — begitu versi baru berlaku efektif, sistem wajib menyebarkan/menyinkronkan rujukan matrix ke seluruh domain pemanggil secara serentak (bukan bertahap per domain), sehingga tidak ada dua domain yang secara bersamaan memakai versi matrix berbeda untuk jenis keputusan yang sama. **Jalur jika penyebaran versi baru belum tersinkron di sebagian domain pemanggil** (mis. akibat gangguan teknis saat propagasi konfigurasi): transaksi approval baru yang terlanjur dibuka pada domain yang belum tersinkron tetap memakai versi matrix lama sampai domain tersebut terkonfirmasi sinkron — bukan dibiarkan memakai kombinasi versi campuran yang tidak konsisten — dan ketidaksinkronan ini wajib terdeteksi serta dilaporkan ke administrator konfigurasi sebagai insiden yang perlu ditindaklanjuti, bukan dianggap selesai hanya karena versi baru sudah "diterapkan" di sumbernya.

**Post-Cycle**: versi matrix yang berlaku menjadi rujukan tunggal bagi seluruh transaksi approval baru (bagian 3.2); transaksi yang sudah berjalan sebelum perubahan tetap mengikuti versi matrix yang berlaku saat transaksi tersebut dimulai, untuk menghindari perubahan aturan di tengah proses yang sedang berjalan.

---

## 6. Sub-Siklus: Audit Trail Persetujuan

**Pre-Cycle**: setiap transaksi approval (bagian 3.2), delegasi (bagian 3.3), dan eskalasi SLA (bagian 3.4) menghasilkan event yang wajib dicatat sebagai bagian audit trail — tidak ada event approval yang dikecualikan dari pencatatan.

**Life Cycle**:
- **Actor**: sistem (pencatatan otomatis), HR Ops/pihak berwenang (query saat dibutuhkan untuk dispute).
- **Proses bisnis**: setiap keputusan approval (setuju/tolak/revisi), delegasi, dan eskalasi dicatat dengan format konsisten — siapa memutuskan, kapan, catatan/alasan, apakah lewat jalur normal/delegasi/eskalasi otomatis — ke satu rujukan tunggal, bukan tersebar per modul yang memanggilnya dengan format masing-masing.
- **Data/field**:

  | Nama Field | Tipe Data | Contoh Nilai | Keterangan |
  |---|---|---|---|
  | ID Log Audit | String, prefix tetap + running number | AUD-2026-0098231 | ID unik tiap entri audit trail |
  | ID Transaksi Terkait | String, rujukan ke transaksi asal (3.2/3.3/3.4) | APR-2026-000451 | Menghubungkan entri ke transaksi approval/delegasi/eskalasi asal |
  | Domain Asal | Enum | "Cuti", "Mutasi", "Delegasi", "Eskalasi SLA" | Domain pemilik proses yang memicu entri ini |
  | Aktor Pemutus | String, Employee ID + Nama | EMP-00080 — Siti Aminah | Pihak yang benar-benar mengambil keputusan (bisa delegate/approver eskalasi) |
  | Jenis Jalur | Enum | "Normal", "Delegasi", "Eskalasi Otomatis", "Eksepsi/Force Majeure" | Wajib diisi agar dapat dibedakan saat dispute |
  | Keputusan | Enum | "Setuju", "Tolak", "Perlu Revisi" | Hasil keputusan yang dicatat |
  | Catatan/Alasan | Teks bebas atau kode kategori alasan baku | "REJ-03 — Anggaran tidak mencukupi" | Sesuai alasan yang diinput pada transaksi asal |
  | Waktu Pencatatan | Timestamp (ISO 8601, immutable) | 2026-08-17T14:30:05+07:00 | Dicatat otomatis oleh sistem, tidak dapat diubah setelah tersimpan |

- **Business rule & validasi**: format pencatatan wajib konsisten lintas seluruh domain yang memanggil kerangka approval ini, agar penelusuran dispute (misal karyawan mempertanyakan siapa yang menyetujui mutasinya) dapat dilakukan dari satu sumber tanpa perlu mengecek format berbeda-beda per domain asal.
- **Approval Workflow**: tidak memerlukan approval — bersifat pencatatan otomatis wajib, tidak dapat dinonaktifkan oleh domain pemanggil manapun.

**Notifikasi/trigger**: tidak ada notifikasi rutin; akses ke audit trail dipicu oleh kebutuhan investigasi/dispute.

**Dokumen/output**: log audit trail permanen per transaksi approval, dapat difilter per karyawan/approver/jenis keputusan/rentang waktu.

**Integrasi antar tahap**: sub-siklus ini menjadi rujukan tunggal yang dikonsumsi lintas domain — Transaksi Approval Generik (bagian 3.2), Delegasi Wewenang (bagian 3.3), SLA Respons Default (bagian 3.4), dan Governance Konfigurasi (bagian 5) seluruhnya menulis eventnya ke sini, bukan menyimpan log terpisah masing-masing. **Jalur jika domain pemanggil gagal mengirim event ke audit trail** (mis. keputusan sudah dieksekusi di domain asal — misal saldo cuti sudah terpotong — namun event pencatatannya gagal terkirim akibat gangguan integrasi): domain asal wajib menahan status transaksi sebagai "Belum Terkonfirmasi Audit" dan melakukan pengiriman ulang otomatis sampai berhasil dicatat, bukan menganggap transaksi selesai hanya karena eksekusi bisnisnya sudah berjalan — mencegah keputusan hubungan kerja yang tidak pernah tercatat sebagai bukti resmi.

**Post-Cycle**: audit trail menjadi bukti resmi saat terjadi dispute hubungan kerja maupun audit kepatuhan internal/eksternal; data ini juga menjadi input bagi evaluasi kepatuhan SLA approver (bagian 3.4) dan evaluasi kualitas konfigurasi matrix (bagian 5, misal jika suatu jenis keputusan berulang kali telat karena approver yang sama).

**Pertimbangan Non-Fungsional**: log audit trail wajib bersifat *immutable* (tidak dapat diubah/dihapus setelah tercatat, termasuk oleh administrator sistem) mengingat perannya sebagai bukti resmi dispute — integritas data lebih diutamakan daripada kemudahan koreksi; retensi log mengikuti kebijakan retensi data yang ditetapkan domain Employee Data Governance (bagian Kebijakan Retensi & Pemusnahan Data), bukan aturan retensi terpisah.

---

## 7. Ringkasan Ketentuan Kunci

- Garis Struktural (internal outsourcing) selalu menjadi jalur approval formal untuk keputusan berdampak hubungan kerja, meski secara operasional harian karyawan penempatan diarahkan PIC client lewat Garis Fungsional — client hanya dapat mengajukan permintaan/observasi, bukan memutuskan.
- Setiap transaksi approval generik wajib menghasilkan satu dari tiga keputusan eksplisit (Setuju/Tolak/Perlu Revisi) di tiap level, dengan pola sekuensial sebagai default dan pola paralel (maker-checker) khusus untuk transaksi lintas-fungsi bernilai tinggi.
- Eskalasi otomatis berjalan saat approver tidak merespons dalam SLA (dibedakan dari delegasi yang dipicu approver berhalangan) — dapat berulang hingga ke puncak hierarki, dengan status "Eskalasi Maksimal" jika Direksi/BOD pun tidak merespons.
- Perubahan matrix approval tunduk pada governance sendiri: validasi otomatis wajib menolak konfigurasi yang menciptakan *approval gap* atau *approval loop* sebelum sampai ke approval Direksi untuk perubahan signifikan.
- Matrix approval diterapkan per legal entity; transaksi lintas-entitas mengikuti level approval tertinggi dari entitas yang terlibat, dan memerlukan konfirmasi dari kedua entitas — bukan salah satu saja.
- Seluruh keputusan approval, delegasi, dan eskalasi dicatat ke satu audit trail tunggal dengan format konsisten lintas domain pemanggil, bersifat *immutable* sebagai bukti resmi dispute.

---

## 8. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Transaksi Approval Generik | Pengajuan & keputusan | Submit pengajuan, review berjenjang, keputusan setuju/tolak/revisi | Pemohon, approver berjenjang | Sesuai matrix jenis keputusan & nilai/dampak | Tidak berlaku langsung | TBD |
| Transaksi Approval Generik | Approval paralel | Maker-checker/verifikasi lintas-fungsi bersamaan | Account Manager, Finance/Legal | Paralel, seluruh approver wajib setuju | Klausul persetujuan client bernilai tinggi (jika relevan) | TBD |
| Delegasi Wewenang | Pendaftaran & eksekusi delegasi | Registrasi delegate, routing otomatis saat approver berhalangan | Approver asli, delegate, sistem | Approval HR Manager untuk delegasi jangka panjang | Tidak berlaku langsung | TBD |
| SLA Respons Default | Monitoring & eskalasi | Pemantauan tenggat, reminder, auto-escalate | Sistem, approver berjenjang | Eskalasi otomatis ke atasan berikutnya | Tidak berlaku langsung | TBD |
| Governance Konfigurasi | Perubahan matrix approval | Pengajuan perubahan, validasi gap/loop, approval Direksi (jika signifikan) | Administrator konfigurasi, Direksi | Approval Direksi untuk perubahan signifikan | Tidak berlaku langsung | TBD |
| Audit Trail | Pencatatan & penelusuran | Pencatatan tiap keputusan, query saat dispute | Sistem, HR Ops | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
