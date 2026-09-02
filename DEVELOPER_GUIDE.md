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
tersebut).

## Daftar Fitur

| # | Fitur | Sumber |
| - | - | - |
| 1 | [Deploy ke Flowable](#1-deploy-ke-flowable) | `useDeployFlowable.ts` |
| 2 | [Start Instance](#2-start-instance) | `useStartProcessInstance.ts` |
| 3 | [Task](#3-task-cari-klaim-selesaikan-reassign-delegasi-due-date--prioritas) | `useFlowableTasks.ts` |
| 4 | [Lacak Proses](#4-lacak-proses) | `useProcessTracking.ts` |
| 5 | [Kontrol Proses](#5-kontrol-proses) | `useProcessControl.ts` |
| 6 | [Riwayat / Audit Trail](#6-riwayat--audit-trail) | `useAuditTrail.ts` |
| 7 | [Dashboard Ringkasan](#7-dashboard-ringkasan) | `useDashboardSummary.ts` |
| 8 | [Catatan Approval](#8-catatan-approval-comments) | `useTaskComments.ts` |
| 9 | [Lampiran Dokumen](#9-lampiran-dokumen) | `useTaskAttachments.ts` |
| 10 | [Grup & User](#10-grup--user-identity-management) | `useIdentity.ts` |
| 11 | [Notifikasi Task Baru](#11-notifikasi-task-baru-polling) | `useNotifyTasks.ts` |
| 12 | [Bandingkan Versi Diagram](#12-bandingkan-versi-diagram) | `useCompareVersions.ts` |

## 1. Deploy ke Flowable

Sumber: `src/composables/useDeployFlowable.ts` + `src/components/DeployDialog.vue`.

```
POST /repository/deployments
Content-Type: multipart/form-data  (di-set otomatis oleh browser, JANGAN
                                     di-set manual — akan merusak boundary)
```

Body (`FormData`), satu part:

| Field | Tipe | Keterangan |
| - | - | - |
| `file` | `Blob` (`application/octet-stream`) | XML diagram saat ini, dengan nama file sesuai field "Nama file deployment" di dialog. Flowable menolak (HTTP 400) kalau ekstensinya bukan salah satu dari `.bpmn`, `.bpmn20.xml`, `.bar`, atau `.zip` — aplikasi ini tidak memvalidasi ekstensi di sisi client, pesan error dari Flowable langsung ditampilkan apa adanya di area status. |

Kalau key proses di dalam XML sama dengan deployment yang sudah ada
sebelumnya, Flowable otomatis membuat versi baru (menaikkan nomor versi) —
tidak menimpa deployment lama. Ini yang dimanfaatkan fitur "Bandingkan Versi
Diagram" (bagian 12) untuk membandingkan antar versi.

Respons sukses (HTTP 200/201), field yang dipakai aplikasi ini:

| Field | Keterangan |
| - | - |
| `id` | ID deployment, ditampilkan ke user. |
| `name` | Nama deployment (biasanya = nama file yang dikirim). |
| `deploymentTime` | Timestamp, ditampilkan ke user. |
| `category`, `parentDeploymentId`, `url`, `tenantId` | Ada di respons tapi tidak dipakai di UI. |

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

## 2. Start Instance

Sumber: `src/composables/useStartProcessInstance.ts` + `src/components/StartInstanceDialog.vue`.

```
POST /runtime/process-instances
Content-Type: application/json
```

Body (JSON):

| Field | Wajib | Keterangan |
| - | - | - |
| `processDefinitionKey` | Ya | Key process definition, bukan deployment/definition ID. Dialog otomatis mengisi field ini dari atribut `id` elemen `<bpmn:process>` root diagram yang sedang dibuka (`getRootProcessId()`), tapi boleh diubah manual. Kalau kosong, request tidak dikirim sama sekali — langsung ditolak di sisi client dengan pesan "Isi Process Definition Key dulu." |
| `businessKey` | Tidak | Hanya disertakan di body kalau field-nya diisi (di-trim dulu; string kosong tidak dikirim sebagai key). |
| `variables` | Tidak | Array objek `{ name, value }`, hanya disertakan kalau ada minimal satu baris variabel dengan nama terisi. Baris dengan nama kosong diabaikan (tidak ikut dikirim). |

Setiap baris variabel di-cast di sisi client sesuai dropdown tipe sebelum dikirim:

| Tipe dropdown | Cara cast | Fallback |
| - | - | - |
| `string` | Dikirim apa adanya. | — |
| `number` | `Number(raw)`. | Kalau hasilnya `NaN` (input bukan angka valid), fallback ke string mentahnya alih-alih mengirim `NaN`. |
| `boolean` | `true` hanya kalau nilai input persis `"true"` atau `"1"`. | Selain itu dikirim `false`. |

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

| Field | Keterangan |
| - | - |
| `id` | Process Instance ID, ditampilkan ke user (jadi acuan untuk fitur Lacak Proses/Kontrol Proses/Riwayat/Catatan Approval/Lampiran Dokumen lainnya). |
| `businessKey` | Ditampilkan kalau ada. |
| `url` | Ditampilkan kalau ada. |

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

## 3. Task (cari, klaim, selesaikan, reassign, delegasi, due date & prioritas)

Sumber: `src/composables/useFlowableTasks.ts` + `src/components/TaskDialog.vue` +
`src/components/TaskRow.vue`. Satu instance composable ini dibuat di
`TaskDialog.vue` dan dipakai bersama oleh setiap `TaskRow.vue` yang
dirender, jadi semua aksi di bawah melapor ke satu area status yang sama.

### 3.1 Cari task

```
GET /runtime/tasks?<query string>
```

Semua parameter query bersifat opsional, hanya disertakan kalau field
form-nya diisi:

| Parameter | Keterangan |
| - | - |
| `candidateGroup` | Filter by candidate group. |
| `processInstanceId` | Filter by Process Instance ID. |
| `dueAfter` | Dari field tanggal (`yyyy-mm-dd`), dikirim sebagai `<tanggal>T00:00:00.000Z`. |
| `dueBefore` | Dari field tanggal, dikirim sebagai `<tanggal>T23:59:59.999Z`. |
| `minimumPriority` / `maximumPriority` | Angka. |
| `sort` | Salah satu dari `priority` \| `dueDate` \| `createTime`; kalau diisi, `order` (`asc` \| `desc`) ikut disertakan. |

Respons: `{ data: FlowableTask[] }` — field per task yang dipakai aplikasi
ini: `id`, `name`, `processInstanceId`, `assignee`, `owner`,
`delegationState`, `priority`, `dueDate`. Hasil ditampilkan apa adanya
(tidak ada sorting/filtering tambahan di sisi client di luar yang sudah
diminta lewat query).

### 3.2 Klaim task

```
POST /runtime/tasks/{taskId}
Content-Type: application/json

{ "action": "claim", "assignee": "<username>" }
```

Username wajib diisi di form sebelum tombol "Klaim" mengirim request
(divalidasi di client, tidak ada request kalau kosong). Sukses = HTTP 2xx
tanpa body yang perlu diparsing — assignee di baris hasil langsung
diperbarui secara optimistik ke nilai yang baru saja dikirim.

### 3.3 Selesaikan task

```
POST /runtime/tasks/{taskId}
Content-Type: application/json

{ "action": "complete" }
```

Dikonfirmasi lewat dialog `confirm()` browser sebelum request dikirim.
Sukses = HTTP 2xx **atau** 204 (Flowable biasa merespons 204 tanpa body
untuk complete). Task yang berhasil diselesaikan dihapus dari daftar hasil
di UI (event `completed` ke parent).

### 3.4 Reassign / Delegasikan / Resolve / edit Due Date & Prioritas

Kelimanya memakai helper yang sama (`performAction`): method-nya `PUT`
kalau body TIDAK punya field `action`, atau `POST` kalau body punya field
`action` (mengikuti konvensi verb Flowable REST). Endpoint path selalu
sama: `{PUT|POST} /runtime/tasks/{taskId}` dengan `Content-Type:
application/json`.

| Aksi | Method | Body | Keterangan |
| - | - | - | - |
| Reassign | `PUT` | `{ "assignee": "<username tujuan>" }` | Alih task secara permanen, tanpa jejak delegasi. |
| Delegasikan | `POST` | `{ "action": "delegate", "assignee": "<username tujuan>" }` | Serahkan sementara; owner asli tercatat oleh Flowable dan bisa menerima kembali lewat Resolve. |
| Resolve | `POST` | `{ "action": "resolve" }` | Kembalikan task ke owner setelah delegasi selesai dikerjakan (tombol ini hanya muncul kalau `delegationState` task == `"pending"`); owner masih perlu klik Selesaikan setelah ini. |
| Simpan Due Date & Prioritas | `PUT` | `{ "priority": 30, "dueDate": "2026-09-15T00:00:00.000Z" }` | Body dibangun dinamis — `priority` disertakan kalau input adalah angka valid (`Number.parseInt`); `dueDate` disertakan **hanya** kalau field tanggal diisi (mengosongkan field lalu Simpan TIDAK menghapus jatuh tempo yang sudah ada — pakai Hapus untuk itu). Kalau body akhirnya kosong, request tidak dikirim. |
| Hapus Jatuh Tempo | `PUT` | `{ "dueDate": null }` | Mengirim `null` secara eksplisit untuk menghapus jatuh tempo. |

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

## 4. Lacak Proses

Sumber: `src/composables/useProcessTracking.ts` + `src/components/TrackProcessDialog.vue` +
`src/components/ProcessInstanceCard.vue`. Fitur ini memvisualisasikan
posisi instance yang sedang berjalan langsung di kanvas diagram (marker
CSS `.active-node-highlight` yang berdenyut oranye), bukan cuma menampilkan
data.

### 4.1 Cari instance (running, dengan fallback riwayat)

| Kondisi | Endpoint |
| - | - |
| Process Instance ID diisi | `GET /runtime/process-instances/{pid}` — HTTP 404 di sini berarti "tidak sedang berjalan" (bukan error), langsung lanjut ke fallback riwayat. |
| Business Key diisi (bukan PID) | `GET /runtime/process-instances?businessKey=<key>` |
| Fallback: tidak ditemukan yang sedang berjalan | `GET /history/historic-process-instances?processInstanceId=<pid>` atau `?businessKey=<key>` |

Kalau ditemukan instance yang sedang berjalan (list tidak kosong): untuk
setiap PID hasil, aplikasi memanggil `GET
/runtime/executions?processInstanceId={pid}` secara paralel (`Promise.all`)
untuk mengumpulkan `activityId` dari tiap execution aktifnya, lalu setiap
ID elemen BPMN yang cocok dengan `elementRegistry` bpmn-js pada diagram
yang sedang terbuka di-highlight lewat `canvas.addMarker(id,
'active-node-highlight')`. Elemen yang tidak ditemukan di diagram (mis.
sedang membuka diagram versi lain) dilewati diam-diam.

Kalau tidak ditemukan yang sedang berjalan, sorotan dibersihkan
(`canvas.removeMarker`) sebelum fallback riwayat dijalankan. Instance dari
riwayat ditampilkan tanpa tombol Lihat Variabel dan tanpa highlight
(proses sudah selesai, tidak ada node aktif).

### 4.2 Lihat Variabel (instance yang sedang berjalan)

```
GET /runtime/process-instances/{instanceId}/variables
```

Menerima dua bentuk respons dari Flowable (keduanya ditangani): array
`[{ name, value }, ...]` langsung, atau envelope `{ data: [{ name, value
}, ...] }` — dipakai bentuk mana pun yang cocok.

### 4.3 Refresh Otomatis

Tidak ada endpoint baru — hanya mengulang query pencarian instance +
`/runtime/executions` di atas pada interval yang ditentukan user (minimum
3 detik, sisi client memaksa `Math.max(3, intervalSeconds)`), dan berhenti
otomatis (membersihkan sorotan) begitu instance yang dipantau terdeteksi
sudah tidak berjalan lagi. Kontrol ini hanya muncul di UI selagi ada
instance yang sedang berjalan hasil pencarian terakhir.

## 5. Kontrol Proses

Sumber: `src/composables/useProcessControl.ts` + `src/components/ProcessControlDialog.vue`.
Lima aksi berbeda, semuanya melapor ke satu area status yang sama.

| Aksi | Method | Path | Body |
| - | - | - | - |
| Suspend instance | `PUT` | `/runtime/process-instances/{pid}` | `{ "action": "suspend" }` |
| Aktifkan instance | `PUT` | `/runtime/process-instances/{pid}` | `{ "action": "activate" }` |
| Hentikan (cancel) instance | `DELETE` | `/runtime/process-instances/{pid}[?deleteReason=<alasan>]` | — |
| Suspend Process Definition | `PUT` | `/repository/process-definitions/{processDefinitionId}` | `{ "action": "suspend", "includeProcessInstances": <bool> }` |
| Aktifkan Process Definition | `PUT` | `/repository/process-definitions/{processDefinitionId}` | `{ "action": "activate", "includeProcessInstances": <bool> }` |

Catatan per aksi:

- **Hentikan instance** — parameter `deleteReason` hanya disertakan di
  query string kalau field alasan diisi. Sukses = HTTP 204 **atau** 2xx
  lainnya. Kalau berhasil, dialog membersihkan field Process Instance ID
  (sama seperti versi HTML lama) — instance yang sudah dihentikan tidak
  bisa dicari lagi lewat `/runtime/`.
- **Suspend/Aktifkan Process Definition** — `includeProcessInstances`
  berasal dari checkbox "sertakan instance yang berjalan" di dialog: kalau
  `true`, seluruh instance yang sedang berjalan dari definisi tersebut
  ikut di-suspend/diaktifkan bersamaan; kalau `false`, instance yang
  sedang berjalan tidak terpengaruh (tetap berjalan meski definisinya
  di-suspend, sehingga hanya instance baru yang tidak bisa dibuat).

Semua lima aksi di atas: kalau respons bukan 2xx, body respons mentah
ditampilkan di area status. Tidak ada perintah curl alternatif untuk
fitur ini (beda dengan Deploy/Start Instance) — kalau `fetch()` gagal,
hanya pesan error generik yang ditampilkan.

## 6. Riwayat / Audit Trail

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
dari list dipakai sebagai ringkasan instance:

| Field ringkasan | Keterangan |
| - | - |
| `id`, `businessKey`, `processDefinitionId` | Identitas instance. |
| `startTime`, `endTime` | Waktu mulai/selesai. |
| `durationInMillis` | Durasi total (ms). |

Lalu:

```
GET /history/historic-activity-instances?processInstanceId=<id dari langkah sebelumnya>&sort=startTime&order=asc
```

Hasilnya (`data: HistoricActivityInstance[]`) dirender sebagai timeline
terurut kronologis, field per aktivitas:

| Field | Keterangan |
| - | - |
| `activityId`, `activityName` | Identitas & nama langkah. |
| `activityType` | Dipetakan ke label Indonesia lewat `activityTypeLabel()` — lihat daftar pemetaan lengkapnya di `useAuditTrail.ts`; tipe yang tidak ada di daftar fallback ke versi ber-spasi & kapital dari nama tipe mentahnya. |
| `assignee` | Siapa yang mengerjakan. |
| `startTime`, `endTime` | Waktu mulai/selesai langkah. |
| `durationInMillis` | Diformat ke "X hari Y jam Z menit" lewat `formatDuration()` — hari/jam/menit hanya ditampilkan kalau bukan nol, detik hanya ditampilkan kalau tidak ada hari/jam yang ditampilkan. |

Kalau salah satu dari dua request di atas gagal (HTTP non-2xx atau network
error), pesan error yang sama ditampilkan untuk keduanya (mengandung
status HTTP + body respons kalau ada, atau hint CORS/network kalau
`fetch()`-nya sendiri gagal).

## 7. Dashboard Ringkasan

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

| Kolom tabel | Endpoint |
| - | - |
| `running` | `GET /runtime/process-instances?processDefinitionKey=<key>&size=0` |
| `completed` | `GET /history/historic-process-instances?processDefinitionKey=<key>&finished=true&size=0` |
| `openTasks` | `GET /runtime/tasks?processDefinitionKey=<key>&size=0` |
| `overdue` | `GET /runtime/tasks?processDefinitionKey=<key>&size=0&dueBefore=<sekarang, ISO>` |

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

## 8. Catatan Approval (comments)

Sumber: `src/composables/useTaskComments.ts` + `src/components/CommentsDialog.vue`.

### 8.1 Muat Catatan

```
GET /runtime/tasks/{taskId}/comments
```

Menerima dua bentuk respons: array langsung, atau envelope `{ data: [...]
}`. Field per catatan (`TaskComment`) yang dipakai:

| Field | Keterangan |
| - | - |
| `id` | ID catatan. |
| `message` | Isi catatan. |
| `author` | Penulis (opsional). |
| `time` | Waktu dibuat. |
| `taskId` | Task asal catatan. |

### 8.2 Tambah Catatan

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

## 9. Lampiran Dokumen

Sumber: `src/composables/useTaskAttachments.ts` + `src/components/AttachmentsDialog.vue` +
`src/components/AttachmentRow.vue`. Fitur ini mengagregasi lampiran dari
**SETIAP** task historis satu process instance (bukan cuma yang aktif),
karena tujuannya melihat dokumen yang di-attach di tahap-tahap yang sudah
selesai sekalipun.

### 9.1 Resolve Process Instance ID

Kalau user mengisi Business Key (bukan PID langsung), PID di-resolve dulu
lewat pola runtime-lalu-fallback-riwayat yang sama seperti fitur Lacak
Proses/Riwayat:

| Urutan | Endpoint |
| - | - |
| 1 | `GET /runtime/process-instances?businessKey=<key>` |
| 2 (fallback kalau #1 kosong) | `GET /history/historic-process-instances?businessKey=<key>` |

### 9.2 Cari task & lampiran

Setelah PID didapat:

| Panggilan | Endpoint | Kegunaan |
| - | - | - |
| 1 | `GET /history/historic-task-instances?processInstanceId=<pid>&size=200` | Mengambil SEMUA task (selesai maupun belum) untuk lookup nama tahap (`taskNameById`, dipakai untuk label "tahap asal" di tiap baris lampiran) dan sumber daftar task yang akan dicek lampirannya. |
| 2 | `GET /runtime/tasks?processInstanceId=<pid>` | Mengambil task yang MASIH aktif — dipakai `isTaskActive(taskId)` untuk menentukan tahap mana yang boleh ditambah/dihapus lampirannya. Kegagalan di sini ditangani lunak (fallback ke list kosong, tidak menggagalkan seluruh pencarian). |

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

### 9.3 Unduh / Buka lampiran

| Tipe lampiran | Perilaku |
| - | - |
| URL (`externalUrl` terisi) | Tidak ada request — langsung `window.open(externalUrl, '_blank')`. |
| File | `GET /runtime/tasks/{taskId}/attachments/{attachmentId}/content` — isinya diambil sebagai `Blob`, dibungkus jadi Object URL sementara, lalu di-trigger sebagai unduhan lewat elemen `<a download>` yang dibuat dan langsung dihapus dari DOM (Object URL di-revoke setelah 4 detik). |

### 9.4 Hapus lampiran

```
DELETE /runtime/tasks/{taskId}/attachments/{attachmentId}
```

Hanya bisa dipanggil dari baris yang tahap asalnya `isTaskActive(taskId)`
bernilai true (tombol Hapus tersembunyi di baris lain). Sukses = HTTP 2xx
atau 204.

### 9.5 Tambah lampiran baru

Endpoint yang sama (`POST /runtime/tasks/{taskId}/attachments`) untuk dua
mode berbeda, dibedakan oleh Content-Type dan bentuk body:

| Mode | Content-Type | Body |
| - | - | - |
| Upload file (ada file dipilih) | `multipart/form-data` (di-set otomatis oleh browser) | `FormData`: `name`, `description` (opsional), `type` (dari `file.type` browser, fallback `"file"`), `file` (File asli). |
| Tautan URL (field Tautan URL diisi, tidak ada file dipilih) | `application/json` | `{ "name": "<nama>", "type": "url", "externalUrl": "<url>", "description": "<opsional>" }` |

Kedua mode: target `taskId` wajib berasal dari task yang masih aktif
(dropdown tujuan di dialog hanya menampilkan `activeTasks`); minimal satu
dari file atau Tautan URL harus diisi, dan `name` wajib diisi — divalidasi
di sisi client sebelum request dikirim. Setelah berhasil, daftar lampiran
otomatis di-refresh lewat pemanggilan ulang alur "Cari task & lampiran" di
atas (9.2).

## 10. Grup & User (identity management)

Sumber: `src/composables/useIdentity.ts` + `src/components/IdentityDialog.vue` +
`src/components/GroupRow.vue`. Semua aksi di bawah (termasuk Tambah
Anggota yang dipanggil dari baris grup) melapor ke satu area status yang
sama.

### 10.1 Cari Grup

```
GET /identity/groups[?nameLike=%<filter>%]
```

`nameLike` hanya disertakan kalau field filter nama diisi, dibungkus
tanda `%` di kedua sisi (substring match, sesuai konvensi `nameLike`
Flowable). Kalau kosong, endpoint dipanggil tanpa query sama sekali
(mengembalikan semua grup). Respons: `{ data: FlowableGroup[] }`, field
per grup: `id`, `name`, `type`.

### 10.2 Lihat Anggota Grup

```
GET /identity/users?memberOfGroup=<groupId>
```

Dipanggil lazy — baru pertama kali tombol "Lihat Anggota" di baris grup
diklik, hasilnya di-cache di baris itu sendiri (tidak dipanggil ulang
kalau di-toggle tutup/buka lagi). Respons: `{ data: FlowableUser[] }` —
hanya field `id` yang dipakai (username).

### 10.3 Tambah Anggota ke Grup

```
POST /identity/groups/{groupId}/members
Content-Type: application/json

{ "userId": "<username>" }
```

`userId` wajib diisi (divalidasi client). Tidak ada validasi bahwa user
tersebut memang ada — kalau usernya tidak ada, error dari Flowable
ditampilkan apa adanya di area status.

### 10.4 Buat Grup Baru

```
POST /identity/groups
Content-Type: application/json
```

| Field | Wajib | Fallback |
| - | - | - |
| `id` | Ya | — |
| `name` | Tidak | Nilai `id`, kalau field nama dikosongi. |
| `type` | Tidak | `"assignment"` (tipe candidate-group standar Flowable), kalau field tipe dikosongi. |

### 10.5 Buat User Baru

```
POST /identity/users
Content-Type: application/json
```

| Field | Wajib | Keterangan |
| - | - | - |
| `id` | Ya | Username. |
| `firstName`, `lastName`, `email` | Tidak | Hanya disertakan di body kalau field-nya diisi. |
| `password` | Tidak | Password BARU untuk user Flowable yang sedang dibuat — **bukan** kredensial koneksi aplikasi ini sendiri (yang tetap selalu dari `.env`, tidak pernah dari form manapun). Hanya disertakan kalau diisi. |

## 11. Notifikasi Task Baru (polling)

Sumber: `src/composables/useNotifyTasks.ts` + `src/components/NotifyDialog.vue` +
lonceng badge di `EditorToolbar.vue`. Composable ini **harus** dibuat satu
kali saja di `BpmnEditor.vue` (bukan di dalam dialog) supaya polling &
badge unseen-count tetap berjalan walau dialognya ditutup — lihat komentar
di file sumbernya untuk detail arsitekturnya.

```
GET /runtime/tasks?candidateGroup=<candidate group>
```

Tidak ada endpoint khusus "notifikasi" di Flowable — fitur ini murni
polling endpoint pencarian task biasa pada interval yang ditentukan user
(input "Interval Cek", dipaksa minimum 10 detik di sisi client:
`Math.max(10, ...)`, default 30 detik), lalu diff ID task hasil polling
saat ini terhadap `Set` ID task dari polling sebelumnya (`knownIds`,
disimpan di closure composable, bukan `ref` — tidak perlu memicu
re-render sendiri).

| Tahap | Perilaku |
| - | - |
| Baseline (polling pertama setelah "Mulai Pantau" diklik) | Hasil pertama ini HANYA dipakai untuk mengisi `knownIds` awal, tidak menghasilkan entri log/notifikasi apa pun (task yang sudah ada saat mulai memantau bukan "task baru"). `setInterval` untuk polling berikutnya baru dijadwalkan setelah baseline ini berhasil — kalau baseline gagal, pemantauan tidak jadi dimulai sama sekali. |
| Polling berikutnya | Task dengan ID yang belum ada di `knownIds` dianggap baru — untuk setiap task baru, satu entri ditambahkan ke daftar log (dibatasi maksimum 30 entri terbaru), badge unseen-count di lonceng toolbar bertambah satu, dan kalau `Notification.permission === 'granted'`, notifikasi peramban native (`new Notification(...)`) ditampilkan juga (gagalnya notifikasi peramban tidak dianggap fatal — entri log tetap tercatat). |

Tombol "Izinkan Notifikasi Peramban" hanya memanggil
`Notification.requestPermission()` bawaan browser — bukan endpoint
Flowable. Menghentikan pemantauan (`clearInterval`) juga tidak memanggil
endpoint apa pun — murni membersihkan timer & state lokal di sisi client.

## 12. Bandingkan Versi Diagram

Sumber: `src/composables/useCompareVersions.ts` + `src/components/CompareVersionsDialog.vue`.
Tidak ada endpoint diff bawaan di Flowable REST API — fitur ini mengambil
XML mentah dua versi lalu membandingkannya sepenuhnya di sisi peramban
lewat `DOMParser`.

### 12.1 Cari Versi

```
GET /repository/process-definitions?key=<process definition key>&sort=version&order=desc&size=100
```

Respons: `{ data: ProcessDefinitionVersion[] }`, field per versi: `id`,
`version`, `suspended`. Hasil (sudah terurut versi-terbaru-dulu dari query
di atas) mengisi kedua dropdown "Versi A"/"Versi B" — dropdown B
otomatis terisi versi terbaru (index 0), dropdown A otomatis terisi versi
sebelumnya (index 1, atau index 0 juga kalau cuma ada satu versi).

### 12.2 Ambil XML & Diff

```
GET /repository/process-definitions/{id}/resourcedata
```

Dipanggil dua kali paralel (`Promise.all`) untuk versi A dan versi B yang
dipilih user, mengembalikan XML BPMN mentah masing-masing versi sebagai
teks. XML kemudian di-parse dengan `DOMParser` bawaan browser: setiap
elemen yang punya atribut `id` dikumpulkan ke dalam map keyed-by-id
(`{ id, tag, name, sourceRef, targetRef, attachedToRef }`), KECUALI tag
yang murni struktural/diagram-interchange (`definitions`, `process`,
`collaboration`, `participant`, `BPMNDiagram`, `BPMNPlane`, `BPMNShape`,
`BPMNLabel`, `BPMNEdge`, `Bounds`, `waypoint`, `extensionElements`,
`incoming`, `outgoing`, `documentation` — daftar lengkap `DIFF_SKIP_TAGS`
di composable-nya).

Kedua map (versi A dan versi B) lalu dibandingkan murni di client (tidak
ada request tambahan):

| Kondisi | Kategori |
| - | - |
| ID hanya ada di map B | Ditambahkan |
| ID hanya ada di map A | Dihapus |
| ID ada di keduanya, minimal satu field (`tag`, `name`, `sourceRef`, `targetRef`, `attachedToRef`) berbeda | Diubah — disertai daftar deskripsi perubahannya (mis. "tipe: Approval / Task → Service Task", "nama: ... → ...", "titik asal alur berubah"). |
| ID ada di keduanya, semua field sama | Tidak berubah |
