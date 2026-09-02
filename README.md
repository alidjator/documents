# BPMN Studio (Vue 3 + TypeScript)

| Dibuat oleh | Tanggal |
| - | - |
| alidjator@gmail.com | 2 September 2026 |

Editor diagram BPMN dan DMN berbasis Vue 3 + TypeScript + Vuetify yang
terintegrasi dengan Flowable REST API. Didesain untuk disematkan (embed)
sebagai komponen `<BpmnEditor>` dan/atau `<DmnEditor>` — independen satu
sama lain — ke dalam project Vue yang sudah berjalan — lihat [Integrasi ke
Project Lain](#integrasi-ke-project-lain).

Untuk referensi lengkap endpoint Flowable + parameter yang dikirim tiap
fitur, lihat [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md).

## Fitur

| Fitur | Deskripsi |
| - | - |
| Editor Diagram | Canvas BPMN (bpmn-js) dengan toolbar Baru/Buka/Undo/Redo/Export, palette drag-and-drop elemen BPMN, dan kontrol zoom. |
| Panel Properti | Panel di sisi kanan kanvas `<BpmnEditor>` — klik elemen apa pun untuk mengedit propertinya langsung (ID/nama proses, Candidate Groups/Users/Assignee & multi-instance untuk User Task, Decision Table Reference Key untuk Business Rule Task, Condition Expression untuk Sequence Flow). Murni client-side lewat API bpmn-js, tidak memanggil Flowable REST sama sekali — lihat [Catatan Teknis](#catatan-teknis). Khusus `<BpmnEditor>`; `<DmnEditor>` tidak punya panel ini karena decision table sudah bisa diedit langsung di grid bawaan dmn-js. |
| Deploy ke Flowable | Kirim diagram sebagai deployment baru — key proses yang sama otomatis menjadi versi baru tanpa menimpa versi lama. |
| Muat dari Flowable | Kebalikan dari Deploy — cari Process Definition yang sudah dideploy by key, pilih versinya, lalu muat XML-nya langsung ke editor. |
| Start Instance | Jalankan process instance baru dari diagram yang terbuka, dengan deteksi otomatis variabel proses dari kondisi gateway/candidate group. |
| Task | Cari, klaim, selesaikan, reassign, delegasikan/resolve, serta atur due date & prioritas task. |
| Lacak Proses | Visualisasi real-time posisi instance yang sedang berjalan langsung di kanvas, dengan refresh otomatis dan fallback ke riwayat. |
| Kontrol Proses | Suspend/aktifkan/hentikan instance, serta suspend/aktifkan definisi proses. |
| Riwayat / Audit Trail | Timeline lengkap tiap tahap yang dilalui satu process instance (siapa mengerjakan, kapan, berapa lama). |
| Dashboard Ringkasan | Statistik (instance berjalan/selesai, task terbuka/terlambat) lintas semua proses yang pernah dideploy. |
| Catatan Approval | Lihat & tambah catatan pada sebuah task; tetap tersimpan walau task-nya sudah selesai. |
| Lampiran Dokumen | Kelola dokumen yang dilampirkan sepanjang alur satu process instance, termasuk di tahap yang sudah selesai. |
| Grup & User | Kelola candidate group dan user Flowable (cari/buat grup, lihat & tambah anggota, buat user). |
| Notifikasi Task Baru | Pemantauan berkala (polling) sebuah candidate group, dengan badge di toolbar dan notifikasi peramban; tetap berjalan di latar walau dialognya ditutup. |
| Bandingkan Versi Diagram | Bandingkan dua versi Process Definition yang sudah dideploy — elemen ditambahkan/dihapus/diubah, dianalisis dari XML masing-masing versi langsung di peramban. |
| Editor DMN (Decision Table) | Komponen terpisah `<DmnEditor>` — canvas dmn-js dengan toolbar Baru/Buka/Undo/Redo/Export, fokus pada decision table (DRD & literal expression bawaan dmn-js tetap bisa diakses lewat tombol "View DRD" tapi bukan target utama). |
| Deploy DMN ke Flowable | Kirim file `.dmn` sebagai deployment baru ke Flowable DMN REST API. **Endpoint belum diverifikasi ke server Flowable sungguhan** — lihat catatan di [Catatan Teknis](#catatan-teknis). |
| Uji Coba Decision | Jalankan decision table yang sudah dideploy dengan nilai variabel tertentu, lihat hasil aturan yang cocok, tanpa perlu menjalankan process instance. Sama seperti Deploy DMN, endpoint-nya belum diverifikasi. |
| Muat DMN dari Flowable | Kebalikan dari Deploy DMN — cari decision yang sudah dideploy by key, pilih versinya, lalu muat XML-nya ke editor. Endpoint-nya belum diverifikasi, sama seperti fitur DMN lain. |
| Grup & User (di editor DMN) | Menu "Flowable" di `<DmnEditor>` juga punya "Grup & User…" — memakai dialog yang sama persis dengan editor BPMN (identity management bukan konsep khusus proses/decision, jadi tidak ada yang perlu dibedakan). |
| Bandingkan Versi Decision | Versi DMN dari "Bandingkan Versi Diagram" — bandingkan dua versi Decision yang sudah dideploy, elemen ditambahkan/dihapus/diubah (rule, kolom input/output, hit policy) dianalisis dari XML masing-masing versi langsung di peramban. Pakai endpoint DMN yang sama seperti fitur DMN lain di atas — **belum diverifikasi ke server Flowable sungguhan**. |
| Riwayat Eksekusi Decision | Versi DMN dari "Riwayat / Audit Trail" — lihat setiap kali sebuah decision dievaluasi Flowable (kapan, versi mana, berhasil/gagal), bisa dipersempit ke satu Process Instance ID atau ke yang gagal saja (untuk memantau kasus no-hit/error dari Business Rule Task). **Endpoint belum diverifikasi ke server Flowable sungguhan.** |
| Pantau Decision Gagal (No-Hit) | Versi DMN dari "Notifikasi Task Baru" — polling berkala untuk evaluasi decision yang gagal (badge lonceng + notifikasi peramban), berguna kalau Business Rule Task pemanggilnya diset `decisionTaskThrowErrorOnNoHits="true"`. **Endpoint belum diverifikasi ke server Flowable sungguhan**, sama seperti Riwayat Eksekusi Decision (satu endpoint yang sama). |
| Dipakai oleh Proses Mana Saja? | Cek proses mana saja yang memanggil sebuah decision lewat Business Rule Task, sebelum deploy ulang decision tersebut — dianalisis dari XML versi terbaru tiap Process Definition langsung di peramban. Tidak seperti fitur DMN lain, **kedua endpoint yang dipakai di sini sudah terverifikasi** (endpoint proses yang sama dengan Dashboard Ringkasan/Bandingkan Versi Diagram). |

## Arsitektur

**Stack:** Vue 3 (`<script setup>` + TypeScript), Vuetify 3, Pinia, bpmn-js,
dmn-js, Vite.

Setiap fitur Flowable mengikuti pola yang sama:

- Satu composable `useXxx.ts` per fitur — membungkus pemanggilan Flowable
  REST API dan state reaktif (`isLoading`, `statusMessage`,
  `statusIsError`, hasil).
- Satu komponen dialog `XxxDialog.vue` per fitur — `v-dialog` Vuetify yang
  mengonsumsi composable-nya dan merender form/hasil.
- `EditorToolbar.vue` memancarkan satu event per fitur (mis. `@deploy`,
  `@tasks`), didengarkan oleh `BpmnEditor.vue` untuk membuka dialog
  terkait.
- `useFlowableStore()` (Pinia) adalah satu-satunya sumber
  `baseUrl`/`username`/`password`/`authHeader`, dibaca dari `.env` — lihat
  [Konfigurasi Flowable](#konfigurasi-flowable-env).
- `StatusBox.vue` dipakai bersama semua dialog untuk menampilkan status
  dan hasil operasi.

`<DmnEditor>` mengikuti pola yang sama persis, komponen & file terpisah dari
`<BpmnEditor>` (lihat daftar di [Struktur direktori](#struktur-direktori)) —
keduanya tidak saling bergantung dan bisa dipakai sendiri-sendiri.

### Struktur direktori

```
src/
  moddle/             Ekstensi moddle BPMN untuk atribut flowable: (assignee,
                       candidateGroups, dll — wajib ada supaya diagram bisa
                       di-deploy & dieksekusi di Flowable), plus XML diagram
                       kosong default untuk "Baru" (blank-bpmn-diagram.ts /
                       blank-dmn-diagram.ts).
  types/dmn-js.d.ts   Deklarasi tipe TypeScript manual untuk dmn-js (library
                       ini tidak menyediakan tipe sama sekali) — hanya
                       mencakup API yang dipakai useDmnModeler.ts.
  stores/             Pinia store: diagram.ts (filename & status "dirty"
                       diagram BPMN yang sedang dibuka), flowable.ts (baseUrl/
                       username/password/authHeader, dari .env — dipakai
                       bersama oleh fitur BPMN maupun DMN).
  types/flowable.ts   Tipe TypeScript untuk request/response Flowable REST
                       (proses maupun DMN).
  composables/        Satu use*.ts per fitur (logika + state reaktif) —
                       lihat DEVELOPER_GUIDE.md untuk detail endpoint tiap
                       fitur. useDmnModeler.ts membungkus siklus hidup
                       instance dmn-js (mirip useBpmnModeler.ts untuk
                       bpmn-js); useDeployDmn.ts & useExecuteDecision.ts
                       untuk integrasi Flowable DMN REST API;
                       useLoadBpmnFromFlowable.ts & useLoadDmnFromFlowable.ts
                       untuk "Muat dari Flowable" (kebalikan dari Deploy);
                       useCompareVersions.ts & useCompareDmnVersions.ts untuk
                       "Bandingkan Versi" (diff elemen antar versi, client-side);
                       useDecisionExecutionHistory.ts untuk "Riwayat Eksekusi
                       Decision" (padanan useAuditTrail.ts di sisi DMN);
                       useNotifyDecisionFailures.ts untuk "Pantau Decision
                       Gagal (No-Hit)" (padanan useNotifyTasks.ts);
                       useDecisionUsage.ts untuk "Dipakai oleh Proses Mana
                       Saja?" (scan client-side, endpoint proses yang sudah
                       terverifikasi);
                       useDraftAutosave.ts untuk simpan/pulihkan draft ke
                       localStorage, dipakai BpmnEditor.vue & DmnEditor.vue
                       — lihat [Draft Otomatis](#draft-otomatis-reload-tidak-hilang).
  components/         BpmnEditor.vue (komponen utama BPMN) + EditorToolbar.vue,
                       dan satu XxxDialog.vue (plus komponen baris pendukung
                       bila ada, mis. TaskRow.vue) per fitur BPMN, termasuk
                       LoadBpmnFromFlowableDialog.vue & CompareVersionsDialog.vue.
                       DmnEditor.vue (komponen utama DMN, independen) +
                       DmnEditorToolbar.vue, ExportDmnDialog.vue,
                       DeployDmnDialog.vue, TestDecisionDialog.vue,
                       LoadDmnFromFlowableDialog.vue, CompareDmnVersionsDialog.vue,
                       DecisionExecutionHistoryDialog.vue,
                       NotifyDecisionFailuresDialog.vue, DecisionUsageDialog.vue.
                       IdentityDialog.vue ("Grup & User") dipakai bersama oleh
                       BpmnEditor.vue maupun DmnEditor.vue — tidak ada versi
                       DMN terpisah karena identity management bukan konsep
                       yang berbeda antara proses dan decision.
  plugins/vuetify.ts  Setup Vuetify + tema warna.
  vite-env.d.ts       Deklarasi tipe untuk `import.meta.env.VITE_FLOWABLE_*`.
```

## Instalasi & Menjalankan

```bash
cp .env.example .env   # isi URL/username/password Flowable Anda
npm install
npm run dev      # dev server, default http://localhost:5173
npm run build    # type-check (vue-tsc) + build produksi ke dist/
npm run preview  # jalankan hasil build produksi secara lokal
```

## Contoh Diagram

Folder `examples/` berisi satu pasang diagram contoh, dibuat dari matrix
approval "MATRIX APPROVAL BERITA ACARA" (routing approval berdasarkan
jenis klien/tender dan nilai project):

| File | Isi |
| - | - |
| `examples/keputusan-approval-bod.dmn` | Decision table "Tentukan Approval BOD & Checker Berita Acara" — 2 kolom input (jenis klien, nilai project) → 4 kolom output (label approval, daftar approver group, level checker OPS/Legal, level checker Finance). |
| `examples/approval-berita-acara.bpmn` | Proses yang memanggil decision di atas lewat Business Rule Task, lalu menjalankan 3 checker berurutan dan tahap Approval BOD (multi-instance sequential — jumlah & urutan approver-nya otomatis mengikuti hasil decision, bukan hardcode). |

Buka salah satunya lewat tombol "Buka…" di `<BpmnEditor>`/`<DmnEditor>`
untuk melihat/mengedit, atau deploy langsung ke Flowable lewat menu
"Flowable" di masing-masing toolbar. **Kedua file punya beberapa asumsi
eksplisit** di komentar XML paling atas (satuan nilai project, batas
antar-tier, level checker untuk tier yang tidak disebutkan gambar
sumbernya, dll.) — baca komentarnya dan sesuaikan rule di file DMN kalau
ada yang meleset dari proses approval yang sebenarnya di perusahaan Anda;
BPMN-nya tidak perlu diubah karena semua kombinasi approver/checker
datang dari hasil decision.

Diagram ini juga jadi pemicu dua penambahan kecil di
`src/moddle/flowable-moddle.ts`: `BusinessRuleTaskProps`
(`flowable:decisionTableReferenceKey`) dan `MultiInstanceProps`
(`flowable:collection`/`flowable:elementVariable`) — sebelumnya moddle
extension ini hanya mencakup atribut User Task & Service Task, jadi kedua
atribut ini akan gugur diam-diam kalau task Business Rule/multi-instance
dibuka lalu di-export ulang lewat editor ini.

## Konfigurasi Flowable (.env)

URL/username/password Flowable **tidak** diinput lewat form manapun di
aplikasi ini — semuanya datang dari environment variable saat build/dev,
lewat file `.env` (isi `.env.example`, lalu `cp .env.example .env` dan isi
nilai Anda sendiri):

```
VITE_FLOWABLE_BASE_URL=https://host-anda/flowable-rest/service
VITE_FLOWABLE_USERNAME=rest-admin
VITE_FLOWABLE_PASSWORD=rahasia
```

Di setiap dialog fitur (mis. Deploy), field "URL REST Flowable" tetap
ditampilkan tapi **read-only** (tidak bisa diedit) supaya jelas nilainya
datang dari konfigurasi, bukan dari form; username/password tidak
ditampilkan sama sekali.

**Peringatan keamanan — baca sebelum mengandalkan ini untuk kerahasiaan:**
Vite menyisipkan semua variabel berawalan `VITE_` ke dalam bundle JS hasil
build secara harfiah (bisa dicek: string URL/username akan langsung terlihat
kalau `dist/assets/*.js` dibuka di editor teks, atau lewat tab Network
browser). Ini artinya `VITE_FLOWABLE_USERNAME`/`PASSWORD` bisa dibaca siapa
pun yang bisa mengakses aplikasi ini — **bukan** cara yang aman untuk
menyimpan password, cuma cara praktis supaya tidak perlu diketik ulang di
form. Pakai user Flowable dengan privilege paling minim yang masih nyaman
kalau sampai terekspos publik, dan untuk deployment produksi sungguhan,
sebaiknya taruh backend/proxy di depan Flowable alih-alih memanggilnya
langsung dari browser dengan kredensial tertanam seperti ini.

`.env` sudah masuk `.gitignore` (jangan commit kredensial asli); `.env.example`
tetap ikut ke-commit sebagai template.

## Integrasi ke Project Lain

`<BpmnEditor>` dan `<DmnEditor>` adalah dua komponen independen — pakai
salah satu saja, atau keduanya, sesuai kebutuhan project Anda.

Komponen `<BpmnEditor>` menerima empat prop opsional:

```vue
<BpmnEditor
  :initial-xml="xmlBpmnAnda"
  initial-filename="proses-saya.bpmn"
  autosave-key="bpmnStudioVue.draft.bpmn.v1"
  :enable-autosave="true"
/>
```

Kalau `initial-xml` tidak diisi, editor mulai dengan diagram kosong (satu
Start Event) — sama seperti tombol "Baru" di toolbar — **kecuali** ada
draft tersimpan otomatis dari sesi sebelumnya, lihat [Draft
Otomatis](#draft-otomatis-reload-tidak-hilang) di bawah.

Komponen `<DmnEditor>` menerima prop yang sejajar:

```vue
<DmnEditor
  :initial-xml="xmlDmnAnda"
  initial-filename="keputusan-saya.dmn"
  autosave-key="bpmnStudioVue.draft.dmn.v1"
  :enable-autosave="true"
/>
```

Kalau `initial-xml` tidak diisi, editor mulai dengan satu decision table
kosong (satu kolom input, satu kolom output, satu rule) — sama seperti
tombol "Baru" di toolbar DMN — **kecuali** ada draft tersimpan otomatis
dari sesi sebelumnya, lihat [Draft Otomatis](#draft-otomatis-reload-tidak-hilang)
di bawah.

### Draft Otomatis (reload tidak hilang)

Diagram yang sedang dibuka **tidak pernah tersimpan ke mana pun** kecuali
lewat Export/Deploy manual — sebelumnya, ini artinya reload halaman (atau
menutup tab tanpa sengaja) langsung menghilangkan seluruh pekerjaan yang
belum di-export, karena diagramnya cuma hidup di memori instance
bpmn-js/dmn-js saat itu. `useDraftAutosave.ts` menutup celah ini: setiap
ada perubahan (`commandStack.changed`), XML diagram saat itu disimpan ke
`localStorage` browser (debounce 800ms supaya tidak menulis di setiap
keystroke), lalu dibaca kembali saat komponen di-mount. Kalau ditemukan,
editor memulihkan draft tersebut alih-alih memulai dari diagram kosong,
dan menampilkan pesan singkat "Draft dipulihkan (2 Sep 16.41)." di area
status (nama file & pesan status di toolbar terpotong dengan "…" kalau
terlalu panjang untuk lebar layar — arahkan kursor untuk melihat teks
lengkapnya).

Beberapa hal penting soal fitur ini:

- **Hanya aktif kalau editor "memiliki" dokumennya sendiri** — begitu prop
  `initial-xml` diisi (host aplikasi lain yang menyuplai kontennya), draft
  otomatis dinonaktifkan total (tidak menyimpan, tidak memulihkan). Ini
  disengaja: kalau host punya penyimpanan sendiri untuk konten yang dia
  suplai, draft lokal yang basi bisa menimpa versi yang lebih baru dari
  host tanpa sepengetahuannya. Matikan juga secara eksplisit lewat
  `:enable-autosave="false"` kalau perlu (mis. kebijakan perusahaan yang
  melarang penyimpanan apa pun di browser).
- Tombol **"Baru"** menghapus draft yang tersimpan (setelah dikonfirmasi
  lewat dialog konfirmasi browser) — logikanya: "Baru" berarti membuang
  pekerjaan saat ini, jadi reload setelahnya seharusnya tidak
  mengembalikan yang baru saja dibuang.
- Draft cuma **satu snapshot yang terus ditimpa** (bukan riwayat/versi) —
  membuka file lain lewat "Buka…" langsung menimpa draft lama dengan isi
  file yang baru dibuka.
- `autosave-key` hanya perlu diubah kalau satu halaman host menyematkan
  lebih dari satu `<BpmnEditor>`/`<DmnEditor>` sekaligus (mis. beberapa
  tab diagram terbuka bersamaan) — tiap instance butuh key sendiri supaya
  tidak saling menimpa draft.
- Best-effort: kalau `localStorage` penuh, dinonaktifkan browser, atau
  sedang mode incognito yang membatasinya, autosave diam-diam tidak
  melakukan apa-apa (tidak ada error yang mengganggu pekerjaan) — hanya
  berarti Anda kembali ke kondisi lama (harus Export/Deploy manual).

Langkah integrasi ke project Vue + TypeScript yang sudah berjalan:

1. Salin folder `src/` ke project Anda (atau jadikan package terpisah
   kalau mau dipakai di lebih dari satu project). Kalau hanya butuh satu
   dari dua editor, file-file milik editor yang tidak dipakai (lihat
   [Struktur direktori](#struktur-direktori)) boleh dihapus.
2. Install dependency yang sama: `bpmn-js`, `vuetify`, `@mdi/font`,
   `pinia`. Kalau memakai `<DmnEditor>`, tambahkan juga `dmn-js`. Kalau
   project Anda belum pakai Pinia, state di `stores/diagram.ts` &
   `stores/flowable.ts` bisa diubah jadi `ref()` biasa tanpa mengubah cara
   komponen memakainya.
3. Tambahkan tiga variabel `VITE_FLOWABLE_*` (lihat [Konfigurasi
   Flowable](#konfigurasi-flowable-env)) ke `.env` project Anda —
   dipakai bersama oleh `<BpmnEditor>` dan `<DmnEditor>` (base URL DMN
   REST API Flowable diasumsikan sama dengan base URL proses; lihat
   catatan di [Catatan Teknis](#catatan-teknis) kalau server Anda
   mengekspos keduanya di base URL yang berbeda).

### Menambahkan fitur baru

Fitur Flowable baru mengikuti pola yang sama seperti fitur yang sudah ada
(lihat [Arsitektur](#arsitektur)):

1. Buat composable `useXxx.ts` — baca `baseUrl`/`authHeader` dari
   `useFlowableStore()`, jangan tambahkan field URL/username/password
   baru per fitur, dan jangan tampilkan username/password di UI manapun.
2. Buat komponen dialog `XxxDialog.vue` yang mengonsumsi composable
   tersebut, dengan field "URL REST Flowable" read-only dan `StatusBox`
   untuk menampilkan status.
3. Tambahkan `v-list-item` baru di menu "Flowable" pada
   `EditorToolbar.vue`, dan wiring event + state dialog di
   `BpmnEditor.vue`.

## Catatan Teknis

- bpmn-js di-inisialisasi tanpa opsi `keyboard.bindTo` eksplisit —
  keyboard shortcut (Undo/Redo, dsb.) otomatis ter-bind ke container
  diagram lewat konfigurasi bawaan di `useBpmnModeler.ts`.
- Panel Properti (`usePropertiesPanel.ts` + `PropertiesPanel.vue`) tidak
  memanggil Flowable REST API sama sekali — berbeda dari hampir semua
  fitur lain di project ini, panel ini murni membaca/menulis model
  diagram lewat API bawaan bpmn-js (`modeling`, `bpmnFactory`, event bus),
  jadi tidak butuh koneksi ke server Flowable untuk berfungsi. Field
  Candidate Groups/Users/Assignee, Condition Expression, dan
  multi-instance (mode cardinality tetap) adalah porting 1:1 dari panel
  properti versi HTML single-file yang jadi cikal-bakal project ini.
  Section Business Rule Task (Decision Table Reference Key,
  throw-error-on-no-hit) dan mode multi-instance berbasis collection
  adalah tambahan baru — tidak ada di versi HTML aslinya — karena
  dibutuhkan untuk mengedit atribut `flowable:decisionTableReferenceKey`
  /`flowable:decisionTaskThrowErrorOnNoHits`/`flowable:collection`
  /`flowable:elementVariable` yang dipakai `examples/approval-berita-acara.bpmn`.
- Build produksi saat ini menghasilkan satu JS bundle besar (~2.3MB,
  sebagian besar dari bpmn-js + dmn-js + Vuetify + font MDI). Kalau
  ukuran bundle jadi masalah di project Anda, pertimbangkan
  code-splitting (`import()` dinamis untuk `<BpmnEditor>`/`<DmnEditor>`)
  atau plugin tree-shaking Vuetify.
- dmn-js tidak menyediakan tipe TypeScript sama sekali (berbeda dari
  bpmn-js) — `src/types/dmn-js.d.ts` adalah deklarasi tipe manual yang
  hanya mencakup API yang benar-benar dipakai `useDmnModeler.ts`, bukan
  tipe lengkap library-nya.
- **Endpoint DMN REST API Flowable (`/dmn-repository/deployments` dan
  `/dmn-runtime/decision-executions`, dipakai oleh Deploy DMN & Uji Coba
  Decision) mengikuti konvensi umum modul `flowable-dmn-rest` tapi belum
  diverifikasi terhadap server Flowable sungguhan** — berbeda dari semua
  endpoint proses lain di project ini yang sudah dikonfirmasi. Server
  Anda mungkin juga mengekspos DMN REST API di base URL/context path
  yang berbeda dari proses (`VITE_FLOWABLE_BASE_URL` dipakai apa adanya
  untuk keduanya di project ini). Kalau permintaan Deploy DMN/Uji Coba
  Decision gagal dengan HTTP 404, cek dulu path & base URL DMN REST API
  di server Flowable Anda, lalu sesuaikan `useDeployDmn.ts` /
  `useExecuteDecision.ts` kalau perlu.
