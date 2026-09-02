# Developer Guide — Referensi Endpoint Flowable

Dokumen ini berisi referensi endpoint Flowable REST API yang dipakai tiap
fitur di BPMN Studio (Vue + TypeScript), beserta parameter yang dikirim —
untuk developer yang perlu tahu persis apa yang terjadi di balik tiap
tombol tanpa harus baca kode composable-nya langsung. Untuk panduan
instalasi, konfigurasi `.env`, dan struktur project secara umum, lihat
[README.md](./README.md).

Semua endpoint di bawah ini memakai Basic Auth (header `Authorization`,
hanya dikirim kalau `VITE_FLOWABLE_USERNAME` atau `VITE_FLOWABLE_PASSWORD`
diisi — lihat "Konfigurasi Flowable (.env)" di README.md) dan base URL dari
`VITE_FLOWABLE_BASE_URL` (semua path di bawah ini relatif terhadap base URL
tersebut). Dokumen ini akan ditambah bertahap per fitur.

## Deploy ke Flowable

Sumber: `src/composables/useDeployFlowable.ts` + `src/components/DeployDialog.vue`.

```
POST /repository/deployments
Content-Type: multipart/form-data  (di-set otomatis oleh browser, JANGAN
                                     di-set manual — akan merusak boundary)
```

Body (`FormData`), satu part:

- `file` — `Blob` (`application/octet-stream`) berisi XML diagram saat ini,
  dengan nama file sesuai field "Nama file deployment" di dialog. Flowable
  menolak (HTTP 400) kalau ekstensinya bukan salah satu dari `.bpmn`,
  `.bpmn20.xml`, `.bar`, atau `.zip` — aplikasi ini tidak memvalidasi
  ekstensi di sisi client, pesan error dari Flowable langsung ditampilkan
  apa adanya di area status.

Kalau key proses di dalam XML sama dengan deployment yang sudah ada
sebelumnya, Flowable otomatis membuat versi baru (menaikkan nomor versi) —
tidak menimpa deployment lama. Ini yang dimanfaatkan fitur "Bandingkan Versi
Diagram" untuk membandingkan antar versi.

Respons sukses (HTTP 200/201), field yang dipakai aplikasi ini:

- `id` — ID deployment, ditampilkan ke user.
- `name` — nama deployment (biasanya = nama file yang dikirim).
- `deploymentTime` — timestamp, ditampilkan ke user.
- (`category`, `parentDeploymentId`, `url`, `tenantId` juga ada di respons
  tapi tidak dipakai di UI.)

Kalau respons bukan 2xx, body respons (teks mentah dari Flowable, biasanya
JSON berisi pesan error) ditampilkan langsung di area status tanpa
diparsing lebih lanjut. Kalau `fetch()` sendiri gagal (network error/CORS),
aplikasi menampilkan pesan generik yang menyarankan kemungkinan penyebab
(CORS belum diizinkan, server tidak terjangkau, dsb.) dan menawarkan tombol
"Salin Perintah curl" sebagai alternatif — perintah curl yang dihasilkan
persis meniru request di atas:

```
curl -u "user:pass" -X POST ".../repository/deployments" \
  -F "file=@nama.bpmn;type=application/octet-stream"
```

## Start Instance

Sumber: `src/composables/useStartProcessInstance.ts` + `src/components/StartInstanceDialog.vue`.

```
POST /runtime/process-instances
Content-Type: application/json
```

Body (JSON):

- `processDefinitionKey` (wajib) — key process definition, bukan
  deployment/definition ID. Dialog otomatis mengisi field ini dari atribut
  `id` elemen `<bpmn:process>` root diagram yang sedang dibuka
  (`getRootProcessId()`), tapi boleh diubah manual. Kalau kosong, request
  tidak dikirim sama sekali — langsung ditolak di sisi client dengan pesan
  "Isi Process Definition Key dulu."
- `businessKey` (opsional) — hanya disertakan di body kalau field-nya diisi
  (di-trim dulu; string kosong tidak dikirim sebagai key).
- `variables` (opsional) — array objek `{ name, value }`, hanya disertakan
  kalau ada minimal satu baris variabel dengan nama terisi. Baris dengan
  nama kosong diabaikan (tidak ikut dikirim). Value di-cast di sisi client
  sesuai dropdown tipe per baris sebelum dikirim:
  - `string` — dikirim apa adanya (string).
  - `number` — dikirim lewat `Number(raw)`; kalau hasilnya `NaN` (input
    bukan angka valid), fallback ke string mentahnya alih-alih mengirim
    `NaN`.
  - `boolean` — `true` hanya kalau nilai input persis `"true"` atau `"1"`;
    selain itu dikirim `false`.

  Tombol "Deteksi Variabel dari Diagram" tidak memanggil endpoint apa pun —
  murni scan XML diagram di sisi client untuk pola `${...}` (dipakai di
  kondisi gateway/candidate group) dan menambahkan baris variabel kosong
  untuk tiap nama unik yang belum ada di daftar, supaya user tinggal isi
  nilainya sebelum submit.

Contoh body lengkap:

```json
{
  "processDefinitionKey": "beritaAcaraApproval",
  "businessKey": "BA-2026-001",
  "variables": [
    { "name": "requesterName", "value": "Budi" },
    { "name": "amount", "value": 1500000 },
    { "name": "isUrgent", "value": true }
  ]
}
```

Respons sukses (HTTP 201), field yang dipakai aplikasi ini:

- `id` — Process Instance ID, ditampilkan ke user (jadi acuan untuk fitur
  Lacak Proses/Kontrol Proses/Riwayat/Catatan Approval/Lampiran Dokumen
  lainnya).
- `businessKey` — ditampilkan kalau ada.
- `url` — ditampilkan kalau ada.

  Kalau body respons ternyata bukan JSON valid, atau JSON-nya tidak
  mengandung `id`, aplikasi tetap menganggap request berhasil (status HTTP
  2xx) dan menampilkan teks mentah responsnya apa adanya alih-alih field di
  atas.

Kalau respons bukan 2xx, body respons mentah ditampilkan di area status.
Kalau `fetch()` gagal (network error/CORS), tampil pesan generik + saran
pakai perintah curl. Perintah curl yang dihasilkan (`buildCurlCommand`)
sengaja ditulis dengan quoting gaya Command Prompt/PowerShell (Windows) —
dialog menampilkan catatan terpisah cara menyesuaikannya untuk
bash/macOS/Linux (ganti kutip dua terluar jadi kutip tunggal, hapus `\`
di depan kutip dua di dalamnya).

## Task (cari, klaim, selesaikan, reassign, delegasi, due date & prioritas)

Sumber: `src/composables/useFlowableTasks.ts` + `src/components/TaskDialog.vue` +
`src/components/TaskRow.vue`. Satu instance composable ini dibuat di
`TaskDialog.vue` dan dipakai bersama oleh setiap `TaskRow.vue` yang
dirender, jadi semua aksi di bawah melapor ke satu area status yang sama.

### Cari task

```
GET /runtime/tasks?<query string>
```

Semua parameter query bersifat opsional, hanya disertakan kalau field
form-nya diisi:

- `candidateGroup` — filter by candidate group.
- `processInstanceId` — filter by Process Instance ID.
- `dueAfter` — dari field tanggal (`yyyy-mm-dd`), dikirim sebagai
  `<tanggal>T00:00:00.000Z`.
- `dueBefore` — dari field tanggal, dikirim sebagai
  `<tanggal>T23:59:59.999Z`.
- `minimumPriority` / `maximumPriority` — angka.
- `sort` — salah satu dari `priority` | `dueDate` | `createTime`; kalau
  diisi, `order` (`asc` | `desc`) ikut disertakan.

Respons: `{ data: FlowableTask[] }` — field per task yang dipakai aplikasi
ini: `id`, `name`, `processInstanceId`, `assignee`, `owner`,
`delegationState`, `priority`, `dueDate`. Hasil ditampilkan apa adanya
(tidak ada sorting/filtering tambahan di sisi client di luar yang sudah
diminta lewat query).

### Klaim task

```
POST /runtime/tasks/{taskId}
Content-Type: application/json

{ "action": "claim", "assignee": "<username>" }
```

Username wajib diisi di form sebelum tombol "Klaim" mengirim request
(divalidasi di client, tidak ada request kalau kosong). Sukses = HTTP 2xx
tanpa body yang perlu diparsing — assignee di baris hasil langsung
diperbarui secara optimistik ke nilai yang baru saja dikirim.

### Selesaikan task

```
POST /runtime/tasks/{taskId}
Content-Type: application/json

{ "action": "complete" }
```

Dikonfirmasi lewat dialog `confirm()` browser sebelum request dikirim.
Sukses = HTTP 2xx **atau** 204 (Flowable biasa merespons 204 tanpa body
untuk complete). Task yang berhasil diselesaikan dihapus dari daftar hasil
di UI (event `completed` ke parent).

### Reassign / Delegasikan / Resolve / edit Due Date & Prioritas

Keempatnya memakai helper yang sama (`performAction`): method-nya `PUT`
kalau body TIDAK punya field `action`, atau `POST` kalau body punya field
`action` (mengikuti konvensi verb Flowable REST). Endpoint path selalu
sama:

```
{PUT|POST} /runtime/tasks/{taskId}
Content-Type: application/json
```

- **Reassign** (`PUT`, tanpa `action`) — alih task secara permanen, tanpa
  jejak delegasi:
  ```json
  { "assignee": "<username tujuan>" }
  ```
- **Delegasikan** (`POST`) — serahkan sementara; owner asli tercatat oleh
  Flowable dan bisa menerima kembali lewat Resolve:
  ```json
  { "action": "delegate", "assignee": "<username tujuan>" }
  ```
- **Resolve** (`POST`) — kembalikan task ke owner setelah delegasi selesai
  dikerjakan (tombol ini hanya muncul kalau `delegationState` task ==
  `"pending"`); owner masih perlu klik Selesaikan setelah ini:
  ```json
  { "action": "resolve" }
  ```
- **Simpan Due Date & Prioritas** (`PUT`, tanpa `action`) — body dibangun
  dinamis, hanya field yang benar-benar diisi/berubah yang disertakan:
  - `priority` — disertakan kalau input prioritas adalah angka valid
    (`Number.parseInt`).
  - `dueDate` — disertakan (format `<yyyy-mm-dd>T00:00:00.000Z`) **hanya**
    kalau field tanggal diisi; mengosongkan field tanggal lalu klik Simpan
    TIDAK menghapus jatuh tempo yang sudah ada (pakai tombol Hapus untuk
    itu) — kalau body akhirnya kosong (tidak ada perubahan valid sama
    sekali), request tidak dikirim.
  ```json
  { "priority": 30, "dueDate": "2026-09-15T00:00:00.000Z" }
  ```
- **Hapus Jatuh Tempo** (`PUT`, tanpa `action`) — mengirim `dueDate: null`
  secara eksplisit untuk menghapusnya:
  ```json
  { "dueDate": null }
  ```

Respons semua aksi di atas: body JSON task yang sudah diperbarui (kalau
ada isinya — beberapa aksi Flowable merespons tanpa body/204), field yang
dibaca aplikasi ini per aksi: `assignee`, `owner`, `delegationState`,
`priority`, `dueDate`. Baris task di UI diperbarui dari field-field ini
(fallback ke nilai yang baru saja dikirim kalau field terkait tidak ada di
respons), supaya ringkasan baris langsung ter-update tanpa perlu
re-search.

Kalau respons bukan 2xx pada aksi manapun di atas, body respons mentah
ditampilkan di area status bersama method+status HTTP-nya. Kalau
`fetch()` gagal (network error/CORS), pesan generiknya sama dengan fitur
lain (kemungkinan CORS/lingkungan terbatas/server tidak terjangkau).

## Lacak Proses

Sumber: `src/composables/useProcessTracking.ts` + `src/components/TrackProcessDialog.vue` +
`src/components/ProcessInstanceCard.vue`. Fitur ini memvisualisasikan
posisi instance yang sedang berjalan langsung di kanvas diagram (marker
CSS `.active-node-highlight` yang berdenyut oranye), bukan cuma menampilkan
data.

### Cari instance (running, dengan fallback riwayat)

Kalau Process Instance ID diisi:

```
GET /runtime/process-instances/{pid}
```

HTTP 404 pada endpoint ini berarti "tidak sedang berjalan" (bukan error) —
langsung lanjut ke fallback riwayat di bawah, bukan ditampilkan sebagai
kegagalan.

Kalau Business Key yang diisi (bukan PID):

```
GET /runtime/process-instances?businessKey=<key>
```

Kalau ditemukan instance yang sedang berjalan (list tidak kosong): untuk
setiap PID hasil, aplikasi memanggil `GET
/runtime/executions?processInstanceId={pid}` secara paralel (`Promise.all`)
untuk mengumpulkan `activityId` dari tiap execution aktifnya, lalu setiap
ID elemen BPMN yang cocok dengan `elementRegistry` bpmn-js pada diagram
yang sedang terbuka di-highlight lewat `canvas.addMarker(id,
'active-node-highlight')`. Elemen yang tidak ditemukan di diagram (mis.
sedang membuka diagram versi lain) dilewati diam-diam.

Kalau tidak ditemukan yang sedang berjalan, sorotan dibersihkan
(`canvas.removeMarker`) dan aplikasi fallback ke riwayat:

```
GET /history/historic-process-instances?processInstanceId=<pid>
GET /history/historic-process-instances?businessKey=<key>
```

Instance dari riwayat ditampilkan tanpa tombol Lihat Variabel dan tanpa
highlight (proses sudah selesai, tidak ada node aktif).

### Lihat Variabel (instance yang sedang berjalan)

```
GET /runtime/process-instances/{instanceId}/variables
```

Menerima dua bentuk respons dari Flowable (keduanya ditangani): array
`[{ name, value }, ...]` langsung, atau envelope `{ data: [{ name, value
}, ...] }` — dipakai bentuk mana pun yang cocok.

### Refresh Otomatis

Tidak ada endpoint baru — hanya mengulang query pencarian instance +
`/runtime/executions` di atas pada interval yang ditentukan user (minimum
3 detik, sisi client memaksa `Math.max(3, intervalSeconds)`), dan berhenti
otomatis (membersihkan sorotan) begitu instance yang dipantau terdeteksi
sudah tidak berjalan lagi. Kontrol ini hanya muncul di UI selagi ada
instance yang sedang berjalan hasil pencarian terakhir.

## Kontrol Proses

Sumber: `src/composables/useProcessControl.ts` + `src/components/ProcessControlDialog.vue`.
Lima aksi berbeda, semuanya melapor ke satu area status yang sama.

### Suspend / Aktifkan instance

```
PUT /runtime/process-instances/{pid}
Content-Type: application/json

{ "action": "suspend" }   atau   { "action": "activate" }
```

### Hentikan (cancel) instance

```
DELETE /runtime/process-instances/{pid}[?deleteReason=<alasan>]
```

Parameter `deleteReason` hanya disertakan di query string kalau field
alasan diisi. Sukses = HTTP 204 **atau** 2xx lainnya. Kalau berhasil,
dialog membersihkan field Process Instance ID (sama seperti versi HTML
lama) — instance yang sudah dihentikan tidak bisa dicari lagi lewat
`/runtime/`.

### Suspend / Aktifkan Process Definition

```
PUT /repository/process-definitions/{processDefinitionId}
Content-Type: application/json

{ "action": "suspend", "includeProcessInstances": true }
```

atau `"action": "activate"`. `includeProcessInstances` berasal dari
checkbox "sertakan instance yang berjalan" di dialog — kalau `true`,
seluruh instance yang sedang berjalan dari definisi tersebut ikut
di-suspend/diaktifkan bersamaan; kalau `false`, instance yang sedang
berjalan tidak terpengaruh (tetap berjalan meski definisinya di-suspend,
sehingga instance baru saja yang tidak bisa dibuat).

Semua lima aksi Kontrol Proses: kalau respons bukan 2xx, body respons
mentah ditampilkan di area status. Tidak ada perintah curl alternatif
untuk fitur ini (beda dengan Deploy/Start Instance) — kalau `fetch()`
gagal, hanya pesan error generik yang ditampilkan.

## Riwayat / Audit Trail

Sumber: `src/composables/useAuditTrail.ts` + `src/components/AuditTrailDialog.vue`.
Dua panggilan berurutan (yang kedua baru dijalankan kalau yang pertama
menemukan sesuatu):

```
GET /history/historic-process-instances?processInstanceId=<pid>
GET /history/historic-process-instances?businessKey=<key>
```

Kalau list hasil kosong, pencarian berhenti di sini dengan pesan "tidak
ditemukan di riwayat" (menyarankan coba "Lacak Proses…" kalau prosesnya
baru saja dimulai dan belum masuk riwayat). Kalau ditemukan, item pertama
dari list dipakai sebagai ringkasan instance (`summary` — field yang
dibaca: `id`, `businessKey`, `processDefinitionId`, `startTime`,
`endTime`, `durationInMillis`), lalu:

```
GET /history/historic-activity-instances?processInstanceId=<id dari langkah sebelumnya>&sort=startTime&order=asc
```

Hasilnya (`data: HistoricActivityInstance[]`) dirender sebagai timeline
terurut kronologis, field per aktivitas: `activityId`, `activityName`,
`activityType` (dipetakan ke label Indonesia lewat `activityTypeLabel()` —
lihat daftar pemetaan lengkapnya di `useAuditTrail.ts`, tipe yang tidak ada
di daftar fallback ke versi ber-spasi & kapital dari nama tipe mentahnya),
`assignee`, `startTime`, `endTime`, `durationInMillis` (diformat ke
"X hari Y jam Z menit" lewat `formatDuration()` — hari/jam/menit hanya
ditampilkan kalau bukan nol, detik hanya ditampilkan kalau tidak ada
hari/jam yang ditampilkan).

Kalau salah satu dari dua request di atas gagal (HTTP non-2xx atau network
error), pesan error yang sama ditampilkan untuk keduanya (mengandung
status HTTP + body respons kalau ada, atau hint CORS/network kalau
`fetch()`-nya sendiri gagal).

## Dashboard Ringkasan

Sumber: `src/composables/useDashboardSummary.ts` + `src/components/DashboardDialog.vue`.
Satu request untuk daftar proses, lalu empat request statistik **per**
proses (dijalankan paralel untuk semua proses sekaligus lewat
`Promise.all`):

```
GET /repository/process-definitions?latest=true&size=100
```

Hanya versi terbaru tiap process definition key yang diambil
(`latest=true`) — kalau kosong, ditampilkan "Tidak ada Process Definition
yang ditemukan di server ini." dan berhenti di situ. Untuk setiap
definisi (`key`, `name`, `version`), empat panggilan berikut dijalankan
paralel:

```
GET /runtime/process-instances?processDefinitionKey=<key>&size=0            → "running"
GET /history/historic-process-instances?processDefinitionKey=<key>&finished=true&size=0  → "completed"
GET /runtime/tasks?processDefinitionKey=<key>&size=0                         → "openTasks"
GET /runtime/tasks?processDefinitionKey=<key>&size=0&dueBefore=<sekarang, ISO>  → "overdue"
```

`size=0` dipakai di semua empat endpoint di atas karena aplikasi ini
**hanya butuh field `total` dari respons** (jumlah, bukan isi list-nya) —
Flowable tetap menyertakan `total` di response envelope walau `size=0`
membuat array `data`-nya kosong, jadi ini menghindari transfer data yang
tidak terpakai. `dueBefore` pada query "overdue" memakai timestamp
`new Date().toISOString()` yang diambil sekali di awal `load()` dan dipakai
sama untuk semua baris (bukan per-baris saat itu juga), supaya semua baris
dashboard konsisten dibandingkan terhadap titik waktu yang sama.

Setiap dari empat panggilan di atas ditangani **independen** — HTTP
non-2xx atau network error pada salah satu endpoint menghasilkan `null`
untuk kolom itu saja (ditampilkan sebagai "?" di tabel), tanpa
menggagalkan tiga kolom lainnya di baris yang sama atau baris-baris lain.
Tidak ada pesan error yang ditampilkan untuk kegagalan per-kolom ini
(silent fallback ke `null`) — hanya kegagalan pada request daftar proses
definition di awal yang menampilkan pesan error di area status.

Baris hasil diurutkan overdue-terbanyak-dulu, lalu nama proses
(alfabetis) sebagai tie-breaker.

## Catatan Approval (comments)

Sumber: `src/composables/useTaskComments.ts` + `src/components/CommentsDialog.vue`.

### Muat Catatan

```
GET /runtime/tasks/{taskId}/comments
```

Menerima dua bentuk respons: array langsung, atau envelope `{ data: [...]
}`. Field per catatan (`TaskComment`) yang dipakai: `id`, `message`,
`author`, `time`, `taskId`.

### Tambah Catatan

```
POST /runtime/tasks/{taskId}/comments
Content-Type: application/json

{ "message": "<isi catatan>", "saveProcessInstanceId": true, "author": "<opsional>" }
```

`author` hanya disertakan kalau field-nya diisi. `saveProcessInstanceId:
true` **wajib** ada di body — inilah yang membuat catatan tetap ada dan
bisa dibaca lewat endpoint yang sama meski task-nya sudah selesai
(kalau field ini tidak dikirim, Flowable menghapus komentarnya begitu task
selesai). Setelah berhasil menambah, aplikasi otomatis memanggil ulang GET
di atas (`loadComments(taskId, showStatus=false)`) untuk me-refresh daftar
— parameter kedua bernilai `false` supaya pesan "Catatan berhasil
ditambahkan." tidak langsung tertimpa pesan "Ditemukan N catatan." dari
refresh tersebut.

## Lampiran Dokumen

Sumber: `src/composables/useTaskAttachments.ts` + `src/components/AttachmentsDialog.vue` +
`src/components/AttachmentRow.vue`. Fitur ini mengagregasi lampiran dari
**SETIAP** task historis satu process instance (bukan cuma yang aktif),
karena tujuannya melihat dokumen yang di-attach di tahap-tahap yang sudah
selesai sekalipun.

### Resolve Process Instance ID

Kalau user mengisi Business Key (bukan PID langsung), PID di-resolve dulu
lewat pola runtime-lalu-fallback-riwayat yang sama seperti fitur Lacak
Proses/Riwayat:

```
GET /runtime/process-instances?businessKey=<key>
GET /history/historic-process-instances?businessKey=<key>   (fallback kalau runtime kosong)
```

### Cari task & lampiran

Setelah PID didapat:

```
GET /history/historic-task-instances?processInstanceId=<pid>&size=200
GET /runtime/tasks?processInstanceId=<pid>
```

Panggilan pertama mengambil SEMUA task (selesai maupun belum) untuk
dijadikan lookup nama tahap (`taskNameById`, dipakai untuk label "tahap
asal" di tiap baris lampiran) dan sumber daftar task yang akan dicek
lampirannya. Panggilan kedua mengambil task yang MASIH aktif — dipakai
`isTaskActive(taskId)` untuk menentukan tahap mana yang boleh
ditambah/dihapus lampirannya (Flowable menolak mutasi lampiran pada task
yang sudah selesai); kegagalan pada panggilan kedua ditangani lunak
(fallback ke list kosong, tidak menggagalkan seluruh pencarian).

Kalau task historis kosong (proses baru saja dimulai, belum ada task
tercatat), pencarian berhenti di situ tanpa memanggil endpoint lampiran.
Kalau tidak, untuk **setiap** ID task historis (paralel via `Promise.all`,
kegagalan per-task ditangani lunak → array kosong untuk task itu saja):

```
GET /runtime/tasks/{taskId}/attachments
```

Field per lampiran (`TaskAttachment`, digabung dengan `taskId` +
`taskName` dari lookup di atas sebelum ditambahkan ke daftar gabungan):
`id`, `name`, `description`, `type`, `userId`, `time`, `externalUrl`.
Hasil gabungan dari semua task diurutkan waktu-terbaru-dulu
(`time.localeCompare` terbalik).

### Unduh / Buka lampiran

- Kalau lampiran bertipe URL (`externalUrl` terisi) — tidak ada request,
  langsung `window.open(externalUrl, '_blank')`.
- Kalau lampiran berupa file: `GET
  /runtime/tasks/{taskId}/attachments/{attachmentId}/content` — isinya
  diambil sebagai `Blob`, dibungkus jadi Object URL sementara, lalu
  di-trigger sebagai unduhan lewat elemen `<a download>` yang dibuat dan
  langsung dihapus dari DOM (Object URL di-revoke setelah 4 detik).

### Hapus lampiran

```
DELETE /runtime/tasks/{taskId}/attachments/{attachmentId}
```

Hanya bisa dipanggil dari baris yang tahap asalnya `isTaskActive(taskId)`
bernilai true (tombol Hapus tersembunyi di baris lain). Sukses = HTTP 2xx
atau 204.

### Tambah lampiran baru

Endpoint yang sama untuk dua mode berbeda, dibedakan oleh Content-Type dan
bentuk body:

**Mode upload file** (ada file dipilih):

```
POST /runtime/tasks/{taskId}/attachments
Content-Type: multipart/form-data  (di-set otomatis oleh browser)
```

Body (`FormData`): `name`, `description` (opsional, hanya disertakan kalau
diisi), `type` (diisi dari `file.type` milik browser, fallback ke string
literal `"file"` kalau browser tidak mendeteksi MIME type-nya), `file`
(File asli).

**Mode tautan URL** (field Tautan URL diisi, tidak ada file dipilih):

```
POST /runtime/tasks/{taskId}/attachments
Content-Type: application/json

{ "name": "<nama>", "type": "url", "externalUrl": "<url>", "description": "<opsional>" }
```

Kedua mode: target `taskId` wajib berasal dari task yang masih aktif
(dropdown tujuan di dialog hanya menampilkan `activeTasks`); minimal satu
dari file atau Tautan URL harus diisi, dan `name` wajib diisi — divalidasi
di sisi client sebelum request dikirim. Setelah berhasil, daftar lampiran
otomatis di-refresh lewat pemanggilan ulang alur "Cari task & lampiran" di
atas.
