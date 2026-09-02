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
