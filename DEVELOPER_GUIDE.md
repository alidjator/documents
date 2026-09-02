# Developer Guide — Referensi Endpoint Flowable

| Dibuat oleh | Tanggal |
| - | - |
| alidjator@gmail.com | 2 September 2026 |

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
| 13 | [Deploy DMN](#13-deploy-dmn) | `useDeployDmn.ts` |
| 14 | [Uji Coba Decision](#14-uji-coba-decision) | `useExecuteDecision.ts` |
| 15 | [Muat dari Flowable (BPMN)](#15-muat-dari-flowable-bpmn) | `useLoadBpmnFromFlowable.ts` |
| 16 | [Muat dari Flowable (DMN)](#16-muat-dari-flowable-dmn) | `useLoadDmnFromFlowable.ts` |
| 17 | [Grup & User (di editor DMN)](#17-grup--user-di-editor-dmn) | `useIdentity.ts` (dipakai bersama) |
| 18 | [Bandingkan Versi Decision](#18-bandingkan-versi-decision) | `useCompareDmnVersions.ts` |
| 19 | [Riwayat Eksekusi Decision](#19-riwayat-eksekusi-decision) | `useDecisionExecutionHistory.ts` |
| 20 | [Pantau Decision Gagal (No-Hit)](#20-pantau-decision-gagal-no-hit) | `useNotifyDecisionFailures.ts` |
| 21 | [Dipakai oleh Proses Mana Saja?](#21-dipakai-oleh-proses-mana-saja) | `useDecisionUsage.ts` |

Fitur 1–12 dan 15 di atas adalah bagian dari `<BpmnEditor>`. Fitur 13, 14,
16, 18, 19, dan 20 adalah bagian dari `<DmnEditor>` (komponen terpisah,
independen — lihat [README.md](./README.md#arsitektur)) dan memakai
`VITE_FLOWABLE_BASE_URL` yang sama dengan fitur BPMN, dengan **caveat
penting** yang diulang di setiap bagian DMN: endpoint DMN-nya belum
diverifikasi terhadap server Flowable sungguhan. Fitur 17 dipakai oleh
KEDUA editor sekaligus lewat komponen/composable yang sama — lihat
bagiannya untuk penjelasan kenapa tidak ada versi DMN terpisah. Fitur 21
adalah PENGECUALIAN pada caveat DMN di atas — kedua endpoint yang
dipakainya adalah endpoint proses yang sudah terverifikasi (bagian 7/12),
bukan endpoint DMN.

**Panel Properti** (`usePropertiesPanel.ts` + `PropertiesPanel.vue`, bagian
dari `<BpmnEditor>`) sengaja TIDAK masuk daftar bernomor di atas — dokumen
ini adalah referensi endpoint Flowable REST, dan Panel Properti tidak
memanggil satu pun endpoint Flowable. Panel ini murni membaca/menulis
model diagram lewat API bawaan bpmn-js (`modeling.updateModdleProperties`,
`bpmnFactory`, event bus `selection.changed`/`element.changed`/`root.set`)
untuk mengedit ID/nama proses, Candidate Groups/Users/Assignee &
multi-instance pada User Task, Decision Table Reference Key pada Business
Rule Task, dan Condition Expression pada Sequence Flow — semuanya
tersimpan langsung di XML diagram, baru terkirim ke Flowable saat fitur 1
(Deploy) dipakai. Lihat [README.md](./README.md#catatan-teknis) untuk
detail lebih lanjut, termasuk bagian mana yang porting 1:1 dari versi HTML
single-file dan bagian mana yang baru ditambahkan.

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

## 13. Deploy DMN

Sumber: `src/composables/useDeployDmn.ts` + `src/components/DeployDmnDialog.vue`
(bagian dari `<DmnEditor>`).

> **Caveat — baca sebelum mengandalkan fitur ini di produksi:** berbeda
> dari fitur 1–12 di atas, endpoint ini **belum diverifikasi** terhadap
> server Flowable sungguhan. Flowable menyediakan REST API DMN lewat
> modul terpisah (`flowable-dmn-rest`); path di bawah mengikuti konvensi
> yang didokumentasikan Flowable, tapi apakah modul ini aktif dan
> terjangkau di base URL yang sama dengan REST API proses
> (`VITE_FLOWABLE_BASE_URL`) tergantung cara server Anda di-deploy. Kalau
> permintaan gagal dengan HTTP 404, periksa dulu path/port REST API DMN
> di server Anda sebelum mengira composable-nya salah — hanya
> `useDeployDmn.ts` yang perlu disesuaikan kalau ternyata beda.

```
POST /dmn-repository/deployments
Content-Type: multipart/form-data  (di-set otomatis oleh browser, JANGAN
                                     di-set manual — akan merusak boundary)
```

Body (`FormData`), satu part — sama persis polanya dengan fitur 1 (Deploy
ke Flowable untuk BPMN):

| Field | Tipe | Keterangan |
| - | - | - |
| `file` | `Blob` (`application/octet-stream`) | XML DMN saat ini, dengan nama file sesuai field "Nama file deployment" di dialog (dialog mem-validasi harus berakhiran `.dmn`). |

Respons sukses, field yang dipakai aplikasi ini (`DmnDeploymentResponse`):

| Field | Keterangan |
| - | - |
| `id` | ID deployment, ditampilkan ke user. |
| `name` | Nama deployment. |
| `deploymentTime` | Timestamp, ditampilkan ke user. |
| `category`, `parentDeploymentId`, `url`, `tenantId` | Ada di respons tapi tidak dipakai di UI. |

Kalau respons bukan 2xx, body respons mentah ditampilkan di area status,
dengan tambahan saran khusus: kalau HTTP 404, kemungkinan server tidak
mengekspos REST API DMN di base URL yang sama dengan REST API proses.
Kalau `fetch()` gagal (network error/CORS), pesan generik + tombol "Salin
Perintah curl":

```
curl -u "user:pass" -X POST ".../dmn-repository/deployments" \
  -F "file=@nama.dmn;type=application/octet-stream"
```

## 14. Uji Coba Decision

Sumber: `src/composables/useExecuteDecision.ts` + `src/components/TestDecisionDialog.vue`
(bagian dari `<DmnEditor>`).

> **Caveat yang sama seperti bagian 13** — endpoint ini belum
> diverifikasi terhadap server Flowable sungguhan. Bentuk path & body di
> bawah mengikuti konvensi yang didokumentasikan Flowable (decisionKey +
> variables masuk, array hasil aturan yang cocok keluar), tapi kalau
> ternyata berbeda di server Anda, hanya `useExecuteDecision.ts` yang
> perlu disesuaikan.

```
POST /dmn-runtime/decision-executions
Content-Type: application/json
```

Body (JSON):

| Field | Wajib | Keterangan |
| - | - | - |
| `decisionKey` | Ya | Key decision yang sudah dideploy. Dialog otomatis mengisi field ini dari decision yang sedang terbuka di editor (`getActiveDecisionId()`), tapi boleh diubah manual. Kalau kosong, request tidak dikirim — langsung ditolak di sisi client dengan pesan "Isi Decision Key dulu." |
| `variables` | Tidak | Objek `{ [nama]: nilai }`, dibangun dari baris-baris "Variabel Input" di dialog (baris dengan nama kosong diabaikan). Hanya disertakan di body kalau ada minimal satu baris variabel dengan nama terisi. |

Setiap baris variabel di-cast di sisi client sesuai dropdown tipe sebelum
dikirim — persis logika yang sama dengan fitur 2 (Start Instance):

| Tipe dropdown | Cara cast | Fallback |
| - | - | - |
| `string` | Dikirim apa adanya. | — |
| `number` | `Number(raw)`. | Kalau hasilnya `NaN`, fallback ke string mentahnya. |
| `boolean` | `true` hanya kalau nilai input persis `"true"` atau `"1"`. | Selain itu dikirim `false`. |

Contoh body lengkap:

```json
{
  "decisionKey": "tentukanDiskon",
  "variables": {
    "totalBelanja": 1500000,
    "isMemberVip": true
  }
}
```

Respons sukses, field yang dipakai aplikasi ini (`ExecuteDecisionResult`):

| Field | Keterangan |
| - | - |
| `decisionResult` | Array objek, satu objek per baris aturan (rule) yang cocok — tiap objek berisi pasangan nama-kolom-output/nilai. Dirender sebagai daftar kartu, satu kartu per baris hasil. |

Kalau body respons sukses ternyata bukan JSON valid, atau JSON-nya tidak
mengandung `decisionResult`, aplikasi tetap menganggap request berhasil
dan menampilkan teks mentah responsnya apa adanya.

Kalau respons bukan 2xx, body respons mentah ditampilkan di area status,
dengan saran yang sama seperti fitur 13 untuk kasus HTTP 404. Kalau
`fetch()` gagal, pesan generik + tombol "Salin Perintah curl" — perintah
curl yang dihasilkan (`buildCurlCommand`) sengaja ditulis dengan quoting
gaya bash/macOS/Linux (kutip tunggal untuk body JSON), **berbeda** dari
perintah curl fitur 2 (Start Instance) yang bergaya Command
Prompt/PowerShell — tidak ada pola sebelumnya untuk fitur DMN ini, jadi
dipilih bentuk yang lebih portabel:

```
curl -u "user:pass" -X POST ".../dmn-runtime/decision-executions" \
  -H "Content-Type: application/json" -d '{"decisionKey":"tentukanDiskon","variables":{"totalBelanja":1500000}}'
```

## 15. Muat dari Flowable (BPMN)

Sumber: `src/composables/useLoadBpmnFromFlowable.ts` +
`src/components/LoadBpmnFromFlowableDialog.vue` (bagian dari
`<BpmnEditor>`). Kebalikan dari fitur 1 (Deploy ke Flowable) — mengambil
XML mentah sebuah Process Definition yang sudah dideploy, lalu memuatnya
ke canvas (menimpa diagram yang sedang terbuka, sama seperti "Buka…" dari
file lokal — tidak ada dialog konfirmasi, mengikuti konvensi "Buka…" yang
sudah ada).

Dua panggilan berurutan:

```
GET /repository/process-definitions?key=<key>&sort=version&order=desc&size=100
GET /repository/process-definitions/{processDefinitionId}/resourcedata
```

Endpoint pertama sama persis dengan yang dipakai fitur 12 (Bandingkan
Versi Diagram) untuk mencari semua versi sebuah key — respons `{ data:
ProcessDefinitionVersion[] }`, field `id`, `version`, `suspended`, dipakai
untuk mengisi dropdown "Versi" di dialog. Endpoint kedua (dipanggil begitu
user memilih versi & klik "Muat ke Editor") juga sama persis dengan yang
dipakai fitur 12 untuk mengambil XML satu versi — mengembalikan XML BPMN
mentah sebagai teks, langsung dioper ke `loadXml()` milik
`useBpmnModeler.ts`. **Kedua endpoint ini sudah terverifikasi** (dipakai
di fitur lain yang sudah dikonfirmasi jalan), berbeda dari endpoint DMN
di bagian 13/14/16.

Nama file yang ditampilkan setelah dimuat: `<key>-v<versi>.bpmn` (mis.
`beritaAcaraApproval-v3.bpmn`) — bukan nama asli file yang pernah
di-deploy (Flowable tidak selalu menyimpan nama file asli di response
pencarian), supaya tetap informatif kalau di-export ulang.

Kalau pencarian atau pengambilan XML gagal (HTTP non-2xx atau network
error), pesan error ditampilkan di area status dialog (mengandung status
HTTP + body respons kalau ada, atau hint CORS/network kalau `fetch()`-nya
sendiri gagal) — dialog tetap terbuka supaya user bisa coba lagi, tidak
ikut menimpa diagram yang sedang terbuka.

## 16. Muat dari Flowable (DMN)

Sumber: `src/composables/useLoadDmnFromFlowable.ts` +
`src/components/LoadDmnFromFlowableDialog.vue` (bagian dari
`<DmnEditor>`). Kebalikan dari fitur 13 (Deploy DMN), mengikuti pola yang
sama persis dengan bagian 15 di atas tapi untuk decision:

```
GET /dmn-repository/decisions?key=<key>&sort=version&order=desc&size=100
GET /dmn-repository/decisions/{decisionId}/resourcedata
```

> **Caveat yang sama seperti bagian 13/14** — path & bentuk respons kedua
> endpoint ini mengikuti konvensi yang didokumentasikan Flowable
> (menyejajarkan pola `/repository/process-definitions` di bagian 15 yang
> SUDAH terverifikasi), tapi **belum diverifikasi** terhadap server
> Flowable sungguhan. Kalau gagal dengan HTTP 404, server Anda mungkin
> mengekspos DMN REST API di base URL/path yang berbeda dari REST API
> proses — hanya `useLoadDmnFromFlowable.ts` yang perlu disesuaikan kalau
> ternyata beda.

Endpoint pertama mengembalikan `{ data: DmnDecisionVersion[] }`, field
`id`, `key`, `name`, `version` — dipakai mengisi dropdown "Versi" (label
menampilkan nama decision, fallback ke key kalau nama kosong). Endpoint
kedua mengembalikan XML DMN mentah sebagai teks, dioper ke `loadXml()`
milik `useDmnModeler.ts`. Nama file setelah dimuat: `<key>-v<versi>.dmn`.

Sama seperti bagian 15: diagram DMN yang sedang terbuka ditimpa tanpa
konfirmasi (konsisten dengan "Buka…"), dan kegagalan pencarian/pengambilan
XML menampilkan pesan error di area status dialog tanpa menutup dialognya.

## 17. Grup & User (di editor DMN)

Menu "Flowable" di `<DmnEditor>` punya item "Grup & User…" persis seperti
`<BpmnEditor>` (bagian 10) — dan itu literally komponen yang sama:
`DmnEditor.vue` meng-import `IdentityDialog.vue` dari lokasi yang sama
tanpa modifikasi apa pun, karena komponen itu sudah sepenuhnya
self-contained (`useFlowableStore()` + `useIdentity()` sendiri, satu-satunya
prop-nya adalah `modelValue`). Endpoint yang dipakai (`/identity/groups`,
`/identity/users`) juga persis sama — lihat bagian 10 untuk detail
lengkapnya, karena identity management (grup & user Flowable) bukan konsep
yang berbeda antara mengelola process instance dan mengelola decision;
tidak ada alasan untuk membuat versi kedua dari dialog atau composable-nya.

## 18. Bandingkan Versi Decision

Sumber: `src/composables/useCompareDmnVersions.ts` +
`src/components/CompareDmnVersionsDialog.vue` (bagian dari `<DmnEditor>`).
Padanan DMN dari fitur 12 (Bandingkan Versi Diagram) — sama-sama tidak ada
endpoint diff bawaan di Flowable, jadi fitur ini mengambil XML mentah dua
versi Decision lalu membandingkannya sepenuhnya di sisi peramban lewat
`DOMParser`.

> **Caveat yang sama seperti bagian 13/14/16** — endpoint di bawah
> mengikuti konvensi yang didokumentasikan Flowable tapi **belum
> diverifikasi** terhadap server Flowable sungguhan. Kalau gagal dengan
> HTTP 404, server Anda mungkin mengekspos DMN REST API di base URL/path
> yang berbeda dari REST API proses.

### 18.1 Cari Versi

```
GET /dmn-repository/decisions?key=<decision key>&sort=version&order=desc&size=100
```

Respons: `{ data: DmnDecisionVersion[] }`, field per versi: `id`, `key`,
`name`, `version`. Sama seperti bagian 12.1: dropdown "Versi B" otomatis
terisi versi terbaru (index 0), dropdown "Versi A" otomatis terisi versi
sebelumnya.

### 18.2 Ambil XML & Diff

```
GET /dmn-repository/decisions/{id}/resourcedata
```

Dipanggil dua kali paralel untuk versi A dan versi B, mengembalikan XML DMN
mentah masing-masing versi sebagai teks.

Perbedaan penting dari diff BPMN (bagian 12.2): elemen BPMN (task, gateway,
sequence flow) semuanya berbagi field yang sama (`name`, `sourceRef`,
`targetRef`, dst), tapi elemen DMN sangat bervariasi bentuknya — sel
`inputEntry`/`outputEntry` sebuah `rule` cuma punya teks FEEL di elemen
anak `<text>`, kolom `input`/`output` cuma punya `label`/`typeRef`,
`decisionTable` cuma punya `hitPolicy`. Daripada menangani tiap tag secara
khusus, `parseDmnElements()` mengekstrak setiap elemen ber-`id` menjadi
bentuk generik `{ id, tag, label, content }`:

- `label` — atribut `label` atau `name` elemen tersebut (kosong kalau
  tidak ada).
- `content` — gabungan semua atribut elemen selain `id`/`label`/`name`
  (format `nama=nilai`), ditambah teks dari elemen anak langsung `<text>`
  kalau ada (ini yang menangkap kondisi/nilai FEEL sebuah
  `inputEntry`/`outputEntry`, atau ekspresi sebuah `inputExpression`).

Tag yang dilewati (`DIFF_SKIP_TAGS`, murni struktural/diagram-interchange):
`definitions`, `DMNDI`, `DMNDiagram`, `DMNShape`, `DMNEdge`, `Bounds`,
`waypoint`, `extensionElements`.

Kedua map (versi A dan versi B) lalu dibandingkan murni di client:

| Kondisi | Kategori |
| - | - |
| ID hanya ada di map B | Ditambahkan |
| ID hanya ada di map A | Dihapus |
| ID ada di keduanya, `tag`/`label`/`content` ada yang berbeda | Diubah — disertai daftar deskripsi perubahannya (mis. "isi: \"Wajib\" → \"Tidak Wajib\"" untuk `outputEntry` yang nilainya berubah, atau "label: ... → ..." untuk kolom yang di-rename). |
| ID ada di keduanya, semuanya sama | Tidak berubah |

Pendekatan generik ini berarti diff bekerja seragam untuk setiap level DMN
(decision, decisionTable, input/output, inputExpression, rule dan
sel-selnya) tanpa perlu kode khusus per tipe elemen — konsekuensinya,
deskripsi perubahan kurang deskriptif dibanding versi BPMN (yang punya
istilah seperti "titik asal alur berubah"), tapi tetap menunjukkan dengan
tepat elemen mana dan bagian mana yang berubah.

## 19. Riwayat Eksekusi Decision

Sumber: `src/composables/useDecisionExecutionHistory.ts` +
`src/components/DecisionExecutionHistoryDialog.vue` (bagian dari
`<DmnEditor>`). Padanan DMN dari fitur 6 (Riwayat / Audit Trail) — bedanya,
fitur 6 melacak *proses*, fitur ini melacak *decision*: setiap kali
Flowable mengevaluasi sebuah decision (baik dipanggil dari Business Rule
Task di sebuah process instance, maupun lewat "Uji Coba Decision"/panggilan
API langsung), evaluasinya bisa dicatat di riwayat DMN engine kalau history
level server mendukungnya.

> **Caveat — lebih berlapis dari fitur DMN lain:** selain path/bentuk
> respons yang belum diverifikasi terhadap server Flowable sungguhan
> (sama seperti bagian 13/14/16/18), fitur ini juga bergantung pada
> *history level* DMN engine server Anda benar-benar merekam eksekusi
> decision — kalau tidak, hasil pencarian akan selalu kosong walau
> decision-nya memang sudah pernah dievaluasi berkali-kali. Kosongnya
> hasil BUKAN otomatis berarti composable-nya salah; lihat pesan status
> saat hasil kosong untuk pengingat ini.

```
GET /dmn-history/historic-decision-executions?decisionKey=<key>
    [&instanceId=<process instance id>][&failed=true]
    &sort=startTime&order=desc&size=100
```

| Parameter | Wajib | Keterangan |
| - | - | - |
| `decisionKey` | Ya | Key decision. Kalau kosong, request tidak dikirim — ditolak di sisi client dengan pesan "Isi Decision Key dulu." |
| `instanceId` | Tidak | Process Instance ID — dipakai untuk mempersempit ke satu process instance saja, supaya bisa disilangkan dengan Process Instance ID yang sama di fitur 6 (Riwayat / Audit Trail) atau fitur 4 (Lacak Proses) di BPMN Studio. |
| `failed` | Tidak | Dikirim `true` kalau checkbox "Hanya yang gagal (no-hit / error)" dicentang — berguna untuk memantau kasus decision yang gagal dievaluasi (mis. tidak ada rule yang cocok padahal Business Rule Task pemanggilnya diset `decisionTaskThrowErrorOnNoHits="true"`, seperti pada `examples/approval-berita-acara.bpmn`). |

Respons: `{ data: HistoricDecisionExecution[] }`, field per eksekusi:

| Field | Keterangan |
| - | - |
| `id` | ID record eksekusi. |
| `decisionDefinitionId`, `decisionKey`, `decisionName`, `decisionVersion` | Identitas decision & versi mana yang dievaluasi. |
| `startTime`, `endTime` | Waktu evaluasi — durasi dihitung di sisi client (`endTime - startTime`) lalu diformat lewat `formatDuration()` (dipakai bersama dari `useAuditTrail.ts`, lihat fitur 6). |
| `instanceId`, `executionId`, `activityId` | Kalau evaluasi ini dipicu dari sebuah process instance: ID instance, ID execution, dan activity id Business Rule Task pemanggilnya. Kosong/null kalau decision dijalankan berdiri sendiri (mis. lewat "Uji Coba Decision"). |
| `failed` | `true` kalau evaluasi gagal (mis. no-hit dengan `decisionTaskThrowErrorOnNoHits`) — baris hasil dengan `failed: true` diberi garis tepi merah di UI, sama seperti pola warna error di fitur lain. |

Hasil pencarian kosong ditampilkan sebagai pesan status yang menyebutkan
tiga kemungkinan penyebab sekaligus (belum pernah dievaluasi, filter
terlalu sempit, atau history level server belum mencatat eksekusi decision)
— sengaja tidak menyimpulkan salah satu, karena dari sisi client ketiganya
tidak bisa dibedakan. Kegagalan pencarian (HTTP non-2xx atau network error)
ditampilkan seperti fitur DMN lain, dengan tambahan saran khusus untuk HTTP
404 (kemungkinan server tidak mengekspos REST API history DMN di base
URL/path yang sama dengan REST API proses).

## 20. Pantau Decision Gagal (No-Hit)

Sumber: `src/composables/useNotifyDecisionFailures.ts` +
`src/components/NotifyDecisionFailuresDialog.vue` + lonceng badge di
`DmnEditorToolbar.vue` (bagian dari `<DmnEditor>`). Padanan DMN dari fitur
11 (Notifikasi Task Baru) — arsitekturnya port 1:1 dari
`useNotifyTasks.ts`: harus dibuat **satu kali saja** di `DmnEditor.vue`
(bukan di dalam dialog), supaya polling & badge unseen-count tetap
berjalan walau dialognya ditutup. Lihat komentar di `useNotifyTasks.ts`
untuk detail arsitektur lengkapnya — bagian ini hanya mencatat bedanya.

> **Caveat** — endpoint yang dipakai (`/dmn-history/historic-decision-executions`)
> sama persis dengan fitur 19, jadi caveat-nya juga sama: belum
> diverifikasi terhadap server Flowable sungguhan, dan bergantung pada
> history level DMN engine server benar-benar mencatat eksekusi decision.

```
GET /dmn-history/historic-decision-executions?decisionKey=<key>&failed=true&sort=startTime&order=desc&size=100
```

Beda dari fitur 11 (yang polling `/runtime/tasks?candidateGroup=...` tanpa
filter tambahan), polling di sini **selalu** menyertakan `failed=true` —
fitur ini secara sengaja hanya peduli pada evaluasi yang gagal, bukan
setiap evaluasi (itu tugas fitur 19 kalau perlu semuanya). Selebihnya pola
diff-nya identik dengan fitur 11:

| Tahap | Perilaku |
| - | - |
| Baseline (polling pertama setelah "Mulai Pantau" diklik) | Hanya mengisi `knownIds` awal dari ID eksekusi gagal yang sudah ada — tidak menghasilkan entri log/notifikasi (kegagalan yang sudah terjadi sebelum mulai memantau bukan "kegagalan baru"). Interval berikutnya baru dijadwalkan setelah baseline ini berhasil. |
| Polling berikutnya | Eksekusi gagal dengan ID yang belum ada di `knownIds` dianggap baru — satu entri log ditambahkan (dibatasi 30 entri terbaru, sama seperti fitur 11), badge unseen-count di lonceng toolbar DMN bertambah satu, dan notifikasi peramban native ditampilkan kalau diizinkan. |

Interval dipaksa minimum 10 detik di sisi client (`Math.max(10, ...)`),
default 30 detik — sama seperti fitur 11. Menghentikan pemantauan murni
`clearInterval` + reset state lokal, tidak memanggil endpoint apa pun.

## 21. Dipakai oleh Proses Mana Saja?

Sumber: `src/composables/useDecisionUsage.ts` +
`src/components/DecisionUsageDialog.vue` (bagian dari `<DmnEditor>`).
Menjawab pertanyaan "sebelum saya deploy ulang decision ini, proses mana
saja yang bakal kena dampaknya" — Flowable tidak punya endpoint untuk ini,
jadi fitur ini dibangun sepenuhnya di sisi client dengan endpoint yang
SUDAH ADA dan SUDAH TERVERIFIKASI (bukan endpoint DMN):

```
GET /repository/process-definitions?latest=true&size=100
GET /repository/process-definitions/{id}/resourcedata
```

Endpoint pertama sama persis dengan yang dipakai fitur 7 (Dashboard
Ringkasan) untuk mengambil versi terbaru tiap Process Definition — bedanya,
fitur ini juga membaca field `id` dari responsnya (Dashboard Ringkasan
tidak perlu, karena dia query endpoint lain by `key`) untuk memanggil
endpoint kedua. Endpoint kedua sama persis dengan yang dipakai fitur 12
(Bandingkan Versi Diagram) untuk mengambil XML mentah satu Process
Definition.

Untuk **setiap** Process Definition hasil endpoint pertama (paralel via
`Promise.allSettled` — dipilih `allSettled`, bukan `all`, supaya satu
Process Definition yang XML-nya gagal diambil tidak menggagalkan
pemeriksaan Process Definition lain; jumlah yang gagal dilaporkan di pesan
status, mis. "(2 Process Definition gagal diperiksa, dilewati)"), XML-nya
di-parse dengan `DOMParser` dan setiap elemen bertag `businessRuleTask`
diperiksa: atributnya di-scan lewat koleksi `attributes`/`localName`
(bukan `getAttribute('flowable:decisionTableReferenceKey')` — nama
atribut ber-prefix seperti ini tidak selalu bisa diambil lewat
`getAttribute` dengan pola yang sama persis tergantung cara dokumennya
di-parse, jadi dipakai teknik yang sama seperti `useCompareDmnVersions.ts`
membaca `label`/`typeRef` DMN) untuk menemukan atribut
`decisionTableReferenceKey`, dibandingkan dengan Decision Key yang dicari.

Setiap Business Rule Task yang cocok menghasilkan satu baris hasil
(`DecisionUsageMatch`): nama & versi proses asal, key proses, serta id &
nama Business Rule Task itu sendiri (satu proses bisa punya lebih dari satu
Business Rule Task yang memanggil decision yang sama — masing-masing jadi
baris terpisah). Hasil kosong menyebutkan secara eksplisit bahwa hanya
versi TERBARU tiap proses yang dipindai — Process Definition versi lama
yang sudah tidak aktif tidak diperiksa, konsisten dengan lingkup fitur 7.
