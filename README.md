# BPMN Studio (Vue 3 + TypeScript)

Port bertahap dari BPMN Studio versi single-file HTML (bpmn-js + vanilla JS)
ke aplikasi Vue 3 + TypeScript + Vuetify, untuk diintegrasikan ke project
yang sudah berjalan.

**Status: semua fitur dari versi HTML lama sudah selesai di-port** —
canvas & toolbar dasar, Deploy ke Flowable, Start Instance (dengan deteksi
variabel otomatis dari diagram), Task (cari/klaim/selesaikan, reassign/
delegasikan/resolve, due date & prioritas, filter & sort), Lacak Proses
(sorot node aktif di kanvas secara real-time, dengan refresh otomatis dan
fallback ke riwayat), Kontrol Proses (suspend/aktifkan/hentikan instance,
suspend/aktifkan definisi proses), Riwayat/Audit Trail (timeline lengkap
tiap tahap satu process instance), Dashboard Ringkasan (statistik lintas
semua proses yang pernah dideploy), Catatan Approval (lihat/tambah catatan
pada sebuah task, tetap tersimpan walau task-nya sudah selesai), Lampiran
Dokumen (agregasi semua dokumen yang dilampirkan di sepanjang alur satu
process instance — termasuk di tahap yang sudah selesai — dengan tambah/
hapus lampiran hanya di task yang masih aktif), Grup & User (cari/buat
candidate group, lihat & tambah anggota, buat user baru), Notifikasi Task
Baru (polling berkala candidate group, badge lonceng di toolbar,
notifikasi peramban kalau diizinkan, terus berjalan walau dialognya
ditutup), dan Bandingkan Versi Diagram (bandingkan dua versi Process
Definition yang sudah dideploy — elemen ditambahkan/dihapus/diubah,
dianalisis lewat XML masing-masing versi langsung di peramban). Rencana
lanjutan berupa fitur baru di luar cakupan tool aslinya ada di "Rencana
Fase Berikutnya" di bawah.

## Menjalankan

```bash
cp .env.example .env   # isi URL/username/password Flowable Anda (lihat "Konfigurasi Flowable" di bawah)
npm install
npm run dev      # dev server, default http://localhost:5173
npm run build    # type-check (vue-tsc) + build produksi ke dist/
npm run preview  # jalankan hasil build produksi secara lokal
```

Sudah diverifikasi: `npm run build` berjalan bersih tanpa error TypeScript,
dan sudah dites end-to-end dengan Playwright: editor (Baru/Buka/Undo/Redo/
Export/Zoom, termasuk drag shape dari palette dan cek reaktivitas tombol
Undo/Redo), Deploy ke Flowable (deploy langsung ke server tiruan dengan
Basic Auth dari `.env`, tombol "Salin Perintah curl", dan penanganan error
koneksi), Start Instance (Process Definition Key terisi otomatis dari
diagram yang dibuka, "Deteksi Variabel dari Diagram" menemukan variabel dari
kondisi gateway & candidate group berisi `${...}`, baris variabel manual
tetap ada saat dialog ditutup-buka lagi, kirim ke server tiruan dengan Basic
Auth, "Salin Perintah curl", validasi Process Definition Key kosong, dan
penanganan error koneksi), Task (cari task ke server tiruan, klaim,
reassign, delegasikan lalu resolve, ubah due date & prioritas lalu hapus due
date, selesaikan task hingga hilang dari daftar, indikator "TERLAMBAT" untuk
task yang jatuh temponya sudah lewat, dan dialog yang mengosongkan hasil
pencarian tiap kali dibuka ulang), dan Lacak Proses (cari instance yang
sedang berjalan lalu verifikasi marker highlight benar-benar muncul di SVG
kanvas, tombol Bersihkan Sorotan menghapusnya lagi, Lihat Variabel memuat
variabel proses, refresh otomatis mendeteksi saat proses selesai lalu
membersihkan sorotan & berhenti sendiri, fallback ke riwayat saat instance
tidak lagi berjalan, dan pesan "tidak ditemukan" saat tidak ada di kedua
tempat), dan Kontrol Proses (suspend/aktifkan/hentikan instance ke server
tiruan, validasi ID kosong, field ID instance yang otomatis dikosongkan
setelah Hentikan/Cancel berhasil, serta suspend/aktifkan definisi proses
dengan opsi "sertakan instance yang berjalan"), dan Riwayat/Audit Trail
(cari instance selesai maupun yang masih berjalan, timeline aktivitas
terurut lengkap dengan siapa mengerjakan & durasi tiap tahap, pesan "tidak
ditemukan" yang mengarahkan ke Lacak Proses, dan dialog yang mengosongkan
hasil tiap kali dibuka ulang), dan Dashboard Ringkasan (tiga proses tiruan
dengan satu endpoint statistik sengaja dibuat gagal untuk memastikan "?"
muncul di kolomnya tanpa mengosongkan kolom lain, urutan baris terverifikasi
task-terlambat-dulu lalu alfabetis, baris total terhitung benar, dan dialog
yang mengosongkan hasil tiap kali dibuka ulang), dan Catatan Approval
(memuat catatan task yang sudah ada lengkap dengan penulis & waktu,
penulis kosong tampil sebagai "(tanpa nama)", pesan "Belum ada catatan
untuk task ini." untuk task tanpa catatan, tambah catatan dengan maupun
tanpa nama penulis lalu muncul di daftar setelah reload otomatis, validasi
Task ID kosong dan catatan kosong, penanganan error HTTP/koneksi, dan
dialog yang mengosongkan hasil tiap kali dibuka ulang), dan Lampiran
Dokumen (lampiran dari dua tahap berbeda satu instance — satu di task yang
sudah selesai, satu di task yang masih aktif — sama-sama muncul dengan
info tahap/pengunggah/waktu, hanya lampiran di task aktif yang punya
tombol Hapus, unduh isi lampiran dari server tiruan, hapus lampiran dengan
konfirmasi lalu hilang dari daftar, resolusi Business Key lewat instance
berjalan, pesan "tidak ditemukan" untuk business key yang tidak ada di
mana pun, pesan instance-tanpa-task-tercatat dan instance-tanpa-lampiran,
bagian Tambah Lampiran Baru yang otomatis tersembunyi kalau tidak ada task
aktif digantikan catatan "Proses sudah selesai", unggah lampiran via
tautan URL lalu muncul di daftar setelah reload otomatis, validasi nama
lampiran kosong dan tidak ada file/tautan, penanganan error HTTP/koneksi,
dan dialog yang mengosongkan hasil tiap kali dibuka ulang), dan Grup &
User (cari grup tanpa filter maupun dengan filter nama yang menyaring
hasil, pesan "tidak ada grup ditemukan" untuk filter yang tidak cocok,
Lihat Anggota memuat daftar anggota grup yang punya anggota maupun yang
kosong, Tambah Anggota berhasil lalu muncul di daftar anggota setelah
reload otomatis, kegagalan Tambah Anggota karena user belum terdaftar
(foreign key) ditampilkan sebagai error, validasi User ID kosong, Buat
Grup dan Buat User berhasil dengan field yang mengosongkan diri lalu
tetap menampilkan error yang jelas untuk ID yang sudah dipakai, validasi
Group ID/User ID kosong, penanganan error HTTP/koneksi, dan dialog yang
mengosongkan hasil tiap kali dibuka ulang), dan Notifikasi Task Baru
(validasi Candidate Group kosong, kegagalan HTTP saat baseline tidak
menyalakan interval, mulai memantau dengan interval minimum 10 detik
lalu field & tombol Mulai Pantau terkunci, **dialog ditutup lalu polling
tetap berjalan di latar** — task baru yang muncul saat dialog tertutup
tercermin di badge lonceng toolbar, dibuka lagi menampilkan entri log &
status terbaru, Berhentikan menghentikan interval sungguhan (dicek tidak
ada tick lagi setelahnya), dan tombol Izinkan Notifikasi Peramban
menampilkan status granted/denied/tidak tersedia tanpa error), dan
Bandingkan Versi Diagram (cari versi untuk key yang tidak ditemukan/error
HTTP, dua versi sungguhan yang beda elemen — added/removed/modified
lengkap dengan pesan perubahan nama & tujuan alur — terhitung benar,
versi tunggal dibandingkan ke dirinya sendiri menampilkan pesan "tidak
ada perbedaan", kegagalan HTTP saat mengambil XML salah satu versi, dan
dialog yang mengosongkan hasil tiap kali dibuka ulang).

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

## Struktur Project

```
src/
  moddle/
    flowable-moddle.ts    Ekstensi moddle BPMN untuk atribut flowable:
                          (assignee, candidateGroups, dll.) — port 1:1 dari
                          FLOWABLE_MODDLE di build_modeler.py versi lama.
                          WAJIB ada supaya diagram yang dibuat/edit di sini
                          tetap bisa di-deploy & dieksekusi di Flowable.
    blank-diagram.ts      XML diagram kosong untuk "Baru".
  stores/
    diagram.ts            Pinia store: filename & status "dirty" diagram
                          yang sedang dibuka (dipakai lintas komponen).
    flowable.ts            baseUrl/username/password Flowable — dibaca dari
                          `import.meta.env.VITE_FLOWABLE_*` (lihat
                          "Konfigurasi Flowable" di atas), dipakai bersama
                          oleh Deploy dan semua fitur Flowable berikutnya.
  types/
    flowable.ts            Tipe TypeScript untuk request/response Flowable
                          REST, ditambah satu per fitur seiring di-port
                          (DeploymentResponse untuk Deploy;
                          StartProcessInstanceRequest/ProcessInstanceResponse/
                          ProcessVariablePayload untuk Start Instance;
                          FlowableTask/TaskQueryResponse/TaskSearchParams
                          untuk Task; RuntimeProcessInstance/
                          HistoricProcessInstance/ProcessInstanceVariable/
                          RuntimeExecution untuk Lacak Proses;
                          HistoricActivityInstance untuk Riwayat/Audit
                          Trail — reuse HistoricProcessInstance untuk
                          ringkasannya; ProcessDefinitionSummary/
                          DashboardRow untuk Dashboard Ringkasan).
  composables/
    useBpmnModeler.ts     Bungkus lifecycle bpmn-js (init/destroy/import/
                          export/undo-redo/zoom) jadi API reaktif Vue, plus
                          getRootProcessId() & detectProcessVariableNames()
                          (dipakai Start Instance untuk auto-fill & deteksi
                          variabel dari kondisi gateway/candidate group).
    useDeployFlowable.ts   Logika Deploy: POST multipart ke
                          /repository/deployments, plus generator perintah
                          curl untuk fallback kalau fetch() diblokir CORS.
    useStartProcessInstance.ts  Logika Start Instance: POST JSON ke
                          /runtime/process-instances, plus generator
                          perintah curl.
    useFlowableTasks.ts    Logika Task: GET /runtime/tasks (cari & filter)
                          plus claim/complete/reassign/delegate/resolve/
                          due-date-&-prioritas lewat POST atau PUT ke
                          /runtime/tasks/{id}. Satu instance dibuat di
                          TaskDialog.vue dan action-nya dibagi ke tiap
                          TaskRow.vue supaya semua status tampil di satu
                          area yang sama (seperti #task-status di versi
                          HTML lama).
    useProcessTracking.ts  Logika Lacak Proses: cari instance via
                          /runtime/process-instances (fallback ke
                          /history/historic-process-instances kalau tidak
                          sedang berjalan), sorot node aktif di kanvas lewat
                          canvas.addMarker()/removeMarker() berdasarkan
                          /runtime/executions, refresh otomatis via
                          setInterval, plus fetchVariables() untuk tombol
                          "Lihat Variabel". Menerima getModeler() (bukan
                          instance modeler langsung) supaya selalu memakai
                          diagram yang sedang aktif di editor.
    useProcessControl.ts   Logika Kontrol Proses: suspend/activate instance
                          via PUT /runtime/process-instances/{id}, hentikan
                          permanen via DELETE (dengan deleteReason opsional),
                          suspend/activate definisi proses via PUT
                          /repository/process-definitions/{id}.
    useAuditTrail.ts       Logika Riwayat/Audit Trail: cari ringkasan via
                          /history/historic-process-instances, lalu timeline
                          lengkap via /history/historic-activity-instances
                          (terurut startTime asc). Juga meng-export
                          activityTypeLabel()/formatDuration(), dipakai
                          langsung oleh AuditTrailDialog.vue untuk merender
                          tiap baris timeline.
    useDashboardSummary.ts  Logika Dashboard Ringkasan: daftar process
                          definition via /repository/process-definitions,
                          lalu 4 hitungan independen per proses (instance
                          berjalan/selesai, task terbuka/terlambat) —
                          kegagalan satu hitungan jadi null ("?" di UI)
                          tanpa mengosongkan hitungan lain di baris yang
                          sama. Diurutkan task-terlambat-dulu, lalu nama.
    useTaskComments.ts     Logika Catatan Approval: GET/POST
                          /runtime/tasks/{taskId}/comments (POST dengan
                          saveProcessInstanceId: true agar catatan tetap ada
                          setelah task selesai). loadComments punya opsi
                          showStatus agar pesan "Catatan berhasil
                          ditambahkan." tidak langsung tertimpa saat
                          addComment me-reload daftar.
    useTaskAttachments.ts  Logika Lampiran Dokumen: resolve Process
                          Instance ID (runtime, fallback riwayat — sama
                          seperti Lacak Proses/Riwayat), lalu agregasi
                          lampiran dari SETIAP task historis (bukan cuma
                          yang aktif) via
                          /history/historic-task-instances +
                          /runtime/tasks/{id}/attachments per tahap.
                          Hapus/tambah lampiran hanya diizinkan ke task
                          yang masih ada di /runtime/tasks (isTaskActive).
    useIdentity.ts         Logika Grup & User: cari/buat candidate group
                          (/identity/groups), lihat & tambah anggota
                          (/identity/users?memberOfGroup=.../
                          /identity/groups/{id}/members), buat user
                          (/identity/users). Semua aksi — termasuk
                          Tambah Anggota yang dipanggil dari GroupRow.vue —
                          melapor ke satu status area yang sama, seperti
                          modal aslinya.
    useNotifyTasks.ts      Logika Notifikasi Task Baru: polling
                          /runtime/tasks?candidateGroup=... tiap interval
                          (minimum 10 detik), diff terhadap ID task yang
                          sudah pernah terlihat, catat entri log +
                          notifikasi peramban (kalau diizinkan) untuk yang
                          baru. HARUS di-instantiate satu kali saja, di
                          BpmnEditor.vue (bukan di dalam NotifyDialog.vue),
                          supaya polling & badge lonceng toolbar tetap
                          jalan walau dialognya ditutup — lihat komentar
                          di file ini untuk alasannya.
    useCompareVersions.ts  Logika Bandingkan Versi Diagram: cari semua versi
                          Process Definition dengan key yang sama
                          (/repository/process-definitions?key=...&sort=
                          version&order=desc), ambil XML mentah tiap versi
                          (/repository/process-definitions/{id}/
                          resourcedata), lalu diff sepenuhnya di sisi
                          peramban — tidak ada endpoint diff bawaan di
                          Flowable REST API. Parsing pakai DOMParser bawaan
                          browser, elemen dikumpulkan per id (tag, name,
                          sourceRef, targetRef, attachedToRef), lalu
                          dibandingkan jadi ditambahkan/dihapus/diubah/tidak
                          berubah. Pakai ulang `activityTypeLabel` dari
                          useAuditTrail.ts untuk label tipe elemen.
  components/
    BpmnEditor.vue         Komponen utama: canvas + toolbar + file input
                          tersembunyi untuk "Buka" + dialog export/deploy/
                          start instance.
    EditorToolbar.vue      Toolbar (Vuetify v-app-bar): Baru/Buka/Undo/Redo/
                          Export/menu Flowable/lonceng Notifikasi Task Baru
                          (dengan badge unseen-count)/Zoom/nama file/status.
    ExportDialog.vue       Dialog menampilkan XML hasil export + tombol
                          salin ke clipboard / unduh .bpmn.
    DeployDialog.vue       Dialog Deploy ke Flowable: URL (read-only, dari
                          .env), nama file, Deploy Langsung / Salin
                          Perintah curl, area status.
    StartInstanceDialog.vue  Dialog Start Instance: URL (read-only), Process
                          Definition Key (auto-fill dari diagram), Business
                          Key opsional, baris Variabel Proses dinamis
                          (nama/nilai/tipe), "Deteksi Variabel dari Diagram",
                          Start Instance / Salin Perintah curl, area status.
    TaskDialog.vue         Dialog Task: URL (read-only), form filter
                          (candidate group, process instance ID, rentang
                          jatuh tempo, rentang prioritas, urutan), tombol
                          Cari Task, area status, daftar TaskRow hasil
                          pencarian.
    TaskRow.vue            Satu baris hasil pencarian task: ringkasan
                          (nama/ID/assignee/owner/prioritas/jatuh tempo,
                          indikator TERLAMBAT), Klaim & Selesaikan,
                          Reassign & Delegasikan/Resolve, edit due date &
                          prioritas.
    TrackProcessDialog.vue  Dialog Lacak Proses: URL (read-only),
                          Process Instance ID / Business Key, Cari Status,
                          Bersihkan Sorotan di Diagram, kontrol Refresh
                          Otomatis (muncul hanya saat ada instance yang
                          sedang dilacak), area status, daftar
                          ProcessInstanceCard hasil pencarian.
    ProcessInstanceCard.vue  Satu kartu hasil: instance berjalan (dengan
                          tombol Lihat Variabel) atau instance dari riwayat
                          (ditampilkan tanpa tombol variabel/highlight).
    ProcessControlDialog.vue  Dialog Kontrol Proses: URL (read-only), bagian
                          Instance Proses (Suspend/Aktifkan/Hentikan +
                          alasan penghentian) dan bagian Definisi Proses
                          (Suspend/Aktifkan + checkbox "sertakan instance
                          yang berjalan"), area status.
    AuditTrailDialog.vue   Dialog Riwayat/Audit Trail: URL (read-only),
                          Process Instance ID / Business Key, tombol Lihat
                          Riwayat, kartu ringkasan instance, daftar kartu
                          timeline aktivitas terurut.
    DashboardDialog.vue    Dialog Dashboard Ringkasan: URL (read-only),
                          tombol Muat Dashboard, tabel per-proses (nama/key/
                          versi/berjalan/selesai/task terbuka/task
                          terlambat) dengan baris total dan catatan "?"
                          kalau ada statistik yang gagal diambil.
    CommentsDialog.vue      Dialog Catatan Approval: URL (read-only), Task
                          ID + tombol Muat Catatan, daftar kartu catatan
                          (penulis/waktu/isi), form Tambah Catatan Baru
                          (isi + penulis opsional), area status.
    AttachmentsDialog.vue   Dialog Lampiran Dokumen: URL (read-only),
                          Process Instance ID / Business Key, tombol Cari &
                          Muat Lampiran, daftar AttachmentRow hasil
                          pencarian, bagian Tambah Lampiran Baru (pilih
                          task aktif tujuan, nama/deskripsi, file atau
                          tautan URL) yang tersembunyi kalau tidak ada task
                          aktif, catatan "Proses sudah selesai" sebagai
                          gantinya, area status.
    AttachmentRow.vue       Satu baris hasil lampiran: nama, tahap asal,
                          deskripsi, tipe/pengunggah/waktu, tombol Unduh
                          (atau Buka Tautan untuk lampiran bertipe URL),
                          tombol Hapus (hanya muncul kalau tahap asalnya
                          masih task aktif).
    IdentityDialog.vue      Dialog Grup & User: URL (read-only), filter
                          nama grup + tombol Cari Grup, daftar GroupRow
                          hasil pencarian, bagian Buat Grup Baru (ID/nama/
                          tipe), bagian Buat User Baru (ID/nama depan/
                          nama belakang/email/password), area status.
    GroupRow.vue            Satu baris hasil grup: nama/ID/tipe, tombol
                          Lihat Anggota yang lazy-load daftar anggota saat
                          pertama diklik, form inline "+ Tambah Anggota".
    NotifyDialog.vue        Dialog Notifikasi Task Baru: URL (read-only),
                          Candidate Group + Interval Cek (dikunci saat
                          sedang memantau), Mulai Pantau/Berhentikan/
                          Izinkan Notifikasi Peramban, area status, daftar
                          entri log task baru. Menerima instance
                          useNotifyTasks() dari BpmnEditor.vue lewat prop
                          `notify`, bukan membuatnya sendiri.
    CompareVersionsDialog.vue  Dialog Bandingkan Versi Diagram: URL
                          (read-only), Process Definition Key + tombol Cari
                          Versi, pilihan Versi A/Versi B (select, otomatis
                          terisi versi terbaru vs sebelumnya), tombol
                          Bandingkan, ringkasan jumlah perbedaan, daftar
                          kartu berwarna untuk elemen Ditambahkan (hijau)/
                          Dihapus (merah)/Diubah (kuning, dengan rincian
                          perubahan), area status.
    StatusBox.vue          Kotak status/output monospace yang dipakai
                          bersama oleh semua dialog fitur Flowable.
  plugins/vuetify.ts        Setup Vuetify + tema warna (dipetakan dari
                          palet warna versi HTML lama).
  vite-env.d.ts             Deklarasi tipe TypeScript untuk
                          `import.meta.env.VITE_FLOWABLE_*`.
```

## Cara Pakai di Project Lain

Komponen `<BpmnEditor>` menerima dua prop opsional:

```vue
<BpmnEditor
  :initial-xml="xmlBpmnAnda"
  initial-filename="proses-saya.bpmn"
/>
```

Kalau `initial-xml` tidak diisi, editor akan mulai dengan diagram kosong
(satu Start Event) — sama seperti tombol "Baru" di versi lama.

Untuk memakainya di project Vue+TS yang sudah berjalan: salin folder `src/`
ke project Anda (atau jadikan package terpisah kalau mau dipakai di lebih
dari satu project), lalu install dependency yang sama: `bpmn-js`, `vuetify`,
`@mdi/font`, `pinia` (kalau project Anda belum pakai Pinia, state di
`stores/diagram.ts` & `stores/flowable.ts` bisa diubah jadi `ref()` biasa
tanpa mengubah cara komponen memakainya), lalu tambahkan tiga variabel
`VITE_FLOWABLE_*` (lihat "Konfigurasi Flowable" di atas) ke `.env` project
Anda.

## Rencana Fase Berikutnya

Semua fitur yang ada di versi HTML lama sudah selesai di-port ke Vue +
TypeScript — daftar di bawah ini murni catatan riwayat portingnya:

- ~~Deploy ke Flowable~~ (selesai — lihat `useDeployFlowable.ts` +
  `DeployDialog.vue`)
- ~~Start Instance (dengan variabel proses)~~ (selesai — lihat
  `useStartProcessInstance.ts` + `StartInstanceDialog.vue`, plus
  `getRootProcessId()`/`detectProcessVariableNames()` di `useBpmnModeler.ts`)
- ~~Task (cari/klaim/selesaikan, reassign/delegasikan/resolve, due date &
  prioritas, filter & sort)~~ (selesai — lihat `useFlowableTasks.ts` +
  `TaskDialog.vue` + `TaskRow.vue`)
- ~~Lacak Proses (+ visualisasi posisi real-time di kanvas)~~ (selesai —
  lihat `useProcessTracking.ts` + `TrackProcessDialog.vue` +
  `ProcessInstanceCard.vue`, plus CSS global `.active-node-highlight` di
  `BpmnEditor.vue`)
- ~~Kontrol Proses (suspend/activate/cancel instance)~~ (selesai — lihat
  `useProcessControl.ts` + `ProcessControlDialog.vue`)
- ~~Riwayat / Audit Trail~~ (selesai — lihat `useAuditTrail.ts` +
  `AuditTrailDialog.vue`)
- ~~Dashboard Ringkasan~~ (selesai — lihat `useDashboardSummary.ts` +
  `DashboardDialog.vue`)
- ~~Catatan Approval (comments)~~ (selesai — lihat `useTaskComments.ts` +
  `CommentsDialog.vue`)
- ~~Lampiran Dokumen (agregasi lintas seluruh task historis satu proses)~~
  (selesai — lihat `useTaskAttachments.ts` + `AttachmentsDialog.vue` +
  `AttachmentRow.vue`)
- ~~Grup & User (identity management)~~ (selesai — lihat `useIdentity.ts` +
  `IdentityDialog.vue` + `GroupRow.vue`)
- ~~Notifikasi Task Baru (polling)~~ (selesai — lihat `useNotifyTasks.ts` +
  `NotifyDialog.vue` + lonceng badge di `EditorToolbar.vue`)
- ~~Bandingkan Versi Diagram~~ (selesai — lihat `useCompareVersions.ts` +
  `CompareVersionsDialog.vue`)

Fitur baru di luar cakupan tool aslinya bisa ditambahkan mengikuti pola
yang sudah dipakai konsisten di atas: satu composable `useXxx.ts` untuk
logika (fetch ke Flowable, state reaktif: `statusMessage`/`statusIsError`/
`isLoading`/hasil) + satu komponen `XxxDialog.vue` untuk tampilannya
(`v-dialog` + field-field Vuetify). Baca `baseUrl`/`authHeader` dari
`useFlowableStore()` — jangan tambahkan field URL/username/password baru
per fitur seperti kelemahan versi HTML lama, dan jangan tampilkan
username/password di UI manapun (konsisten dengan "Konfigurasi Flowable"
di atas). Tambahkan dropdown-item baru di `EditorToolbar.vue`'s menu
"Flowable" untuk membuka dialog fitur tersebut.

## Catatan Teknis

- Versi `bpmn-js` di sini (18.x) lebih baru dari yang dipakai versi HTML
  lama (17.11.1 via CDN). Ada satu perubahan API yang relevan: opsi
  `keyboard: { bindTo: window }` sudah dihapus di bpmn-js 18 — binding
  keyboard sekarang implisit ke container. Composable `useBpmnModeler.ts`
  sudah disesuaikan untuk ini.
- Kredensial Flowable sekarang datang dari `.env` (lihat "Konfigurasi
  Flowable" di atas), bukan dari form — ini beda dari versi HTML lama yang
  punya field URL/username/password yang bisa diedit di tiap modal. Kalau
  suatu saat butuh ganti server Flowable tanpa rebuild (mis. staging vs
  produksi), pertimbangkan generate `.env` per environment saat proses
  deploy/CI, bukan menambahkan kembali field edit di UI.
- Build produksi saat ini menghasilkan satu JS bundle besar (~1.3MB,
  sebagian besar dari bpmn-js + Vuetify + font MDI). Untuk project produksi,
  pertimbangkan code-splitting (`import()` dinamis untuk `<BpmnEditor>`,
  atau plugin tree-shaking Vuetify) kalau ukuran bundle jadi masalah — belum
  dilakukan di Fase 1 ini supaya fokus ke fungsionalitas dulu.
