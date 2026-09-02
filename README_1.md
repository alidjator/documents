# BPMN Studio — Developer Guide

Panduan teknis untuk developer yang mau memahami, mengubah, atau mengintegrasikan BPMN Studio (single-file HTML app, dihasilkan oleh `build_modeler.py`) dengan REST API Flowable.

Dokumen ini akan bertambah per fitur. Untuk saat ini baru mencakup **Deploy ke Flowable**; fitur lain (Start Instance, Task, Grup & User, Kontrol Proses, Lacak Proses, Riwayat/Audit Trail, Catatan Approval, Lampiran Dokumen, Notifikasi) akan menyusul di revisi berikutnya.

## Daftar Isi

- [Konvensi umum](#konvensi-umum)
- [Deploy ke Flowable](#deploy-ke-flowable)

## Konvensi Umum

Semua fitur di BPMN Studio yang bicara ke Flowable mengikuti pola yang sama:

- **Base URL**: field "URL REST Flowable" di tiap modal, default `https://api-aws.satu.solutions/flowable-rest/service`, disimpan per-modal (tanpa trailing slash — kode selalu strip `/+$`). Semua endpoint di bawah ditulis relatif terhadap base URL ini.
- **Autentikasi**: HTTP Basic Auth. Kalau username DAN password sama-sama kosong, header `Authorization` tidak dikirim sama sekali (asumsi: server tidak butuh auth). Kalau salah satu diisi, header dikirim sebagai `Authorization: Basic ' + btoa(username + ':' + password)`.
- **CORS / sandbox**: semua panggilan dilakukan langsung dari `fetch()` di browser pengguna, bukan dari server perantara. Ini berarti server Flowable tujuan harus mengizinkan CORS untuk origin halaman ini, atau panggilan akan gagal dengan `Failed to fetch`. BPMN Studio juga bisa dijalankan di dalam lingkungan sandbox (mis. sebagai Artifact) yang memblokir sebagian pemanggilan API eksternal. Untuk fitur yang murni mengirim data (bukan sekadar membaca), BPMN Studio menyediakan fallback "Salin Perintah curl" yang dijalankan dari terminal pengguna sendiri, sehingga tidak terkena kedua batasan itu.
- **Tidak ada perantara**: kredensial yang dimasukkan ke form hanya dipakai untuk memanggil Flowable langsung dari perangkat pengguna; tidak pernah dikirim ke pihak lain atau disimpan di server manapun.

---

## Deploy ke Flowable

**Lokasi kode**: `build_modeler.py`, bagian `/* ---------------- deploy to Flowable ---------------- */`. Tombol pembuka: `#btn-deploy` (dropdown Flowable). Handler kirim: `#btn-deploy-send`. Handler fallback: `#btn-deploy-curl`.

### Alur

1. User klik "Deploy ke Flowable" di dropdown → BPMN Studio memanggil `modeler.saveXML({ format: true })` (API bpmn-js) untuk meng-generate ulang XML BPMN terbaru dari diagram yang sedang dibuka, lalu menyimpannya di variabel `deployXmlCache`.
2. Modal terbuka, field "Nama file deployment" diisi otomatis dari nama file yang sedang aktif (ekstensi diganti `.bpmn`).
3. User isi URL REST, username/password (opsional), lalu klik salah satu:
   - **"Deploy Langsung"** → kirim `fetch()` langsung dari browser.
   - **"Salin Perintah curl"** → generate & salin perintah `curl` yang setara ke clipboard, untuk dijalankan manual dari terminal (perlu file `.bpmn`-nya sudah diunduh lebih dulu lewat modal Export).

### Endpoint

```
POST {baseUrl}/repository/deployments
```

Tidak ada query parameter yang wajib.

### Request

| Aspek | Detail |
|---|---|
| Method | `POST` |
| Content-Type | `multipart/form-data` — **jangan** di-set manual; browser (lewat objek `FormData`) yang mengisi header ini beserta `boundary`-nya secara otomatis. Kalau di-set manual, boundary akan rusak dan request gagal di-parse server. |
| Header lain | `Authorization: Basic <base64(user:pass)>` — hanya dikirim kalau username atau password diisi. |
| Body | 1 form-data part bernama `file`, berisi byte XML BPMN sebagai `Blob` (`type: 'application/octet-stream'`), dengan nama file sesuai isian "Nama file deployment". |

Contoh persis apa yang dikirim BPMN Studio (`btn-deploy-send`):

```js
var form = new FormData();
var blob = new Blob([deployXmlCache], { type: 'application/octet-stream' });
form.append('file', blob, f.filename); // f.filename mis. "berita_acara_approval_executable.bpmn"

fetch(f.url + '/repository/deployments', {
  method: 'POST',
  headers: headers, // { Authorization: 'Basic ...' } atau {} kalau kredensial kosong
  body: form
});
```

Perintah curl yang setara (dihasilkan tombol "Salin Perintah curl"):

```bash
curl -u "username:password" -X POST "https://host/flowable-rest/service/repository/deployments" \
  -F "file=@nama_file.bpmn;type=application/octet-stream"
```

### Aturan & batasan penting dari Flowable (bukan dari BPMN Studio)

Ini semua perilaku Flowable engine sendiri, jadi berlaku juga kalau developer lain mau memanggil endpoint ini langsung tanpa lewat BPMN Studio:

- **Ekstensi nama file wajib** salah satu dari: `.bpmn`, `.bpmn20.xml`, `.bar`, atau `.zip`. Nama file dengan ekstensi lain ditolak dengan `400 Bad Request` — makanya BPMN Studio selalu memaksa `.bpmn` di field "Nama file deployment".
- **Hanya 1 file yang diproses per request.** Flowable mengambil file pertama yang ditemukan di multipart body dan mengabaikan sisanya — nama field form-nya (`file`) sebenarnya tidak diperiksa secara ketat oleh server, tapi BPMN Studio tetap memakai nama `file` karena itu konvensi resmi di dokumentasi/Swagger Flowable. Untuk deploy lebih dari satu resource sekaligus (mis. BPMN + form definition), harus dibungkus jadi satu file `.bar`/`.zip` — bukan dikirim sebagai beberapa part multipart.
- **Field `tenantId` (opsional, tidak dipakai BPMN Studio saat ini)**: kalau perlu deploy ke tenant tertentu (multi-tenant Flowable), bisa ditambahkan sebagai form-field biasa bernama `tenantId`. Karena BPMN Studio tidak mengirim field ini, semua deployment dari tool ini masuk ke tenant default.
- **Jangan coba kirim nama deployment lewat form-field `deploymentName`/`deploymentKey`** — meski terdokumentasi sebagai form-field, secara teknis Flowable hanya membacanya dari **query string URL** (`POST /repository/deployments?deploymentName=...`), bukan dari body multipart; form-field dengan nama itu akan diam-diam diabaikan. BPMN Studio tidak memakai parameter ini sama sekali — nama deployment yang muncul di Flowable otomatis mengikuti nama file yang dikirim.

### Response sukses — `201 Created`

```json
{
  "id": "25001",
  "name": "berita_acara_approval_executable.bpmn",
  "deploymentTime": "2026-09-02T10:00:00.000+0000",
  "category": null,
  "parentDeploymentId": null,
  "url": "https://host/flowable-rest/service/repository/deployments/25001",
  "tenantId": ""
}
```

- `category` selalu `null` dari endpoint create ini (tidak bisa diisi lewat create-deployment).
- BPMN Studio hanya menampilkan `id`, `name`, dan `deploymentTime` ke user di kotak status; field lain tersedia di response tapi tidak ditampilkan — kalau perlu field lain (mis. `url` untuk link langsung ke resource), tinggal tambahkan di handler `.then()` pada `btn-deploy-send`.

### Response gagal

| Status | Penyebab umum |
|---|---|
| `400 Bad Request` | Request bukan multipart, tidak ada file terlampir, atau nama file berekstensi tidak dikenali (lihat aturan ekstensi di atas). |
| `401 Unauthorized` | Server butuh auth tapi kredensial Basic Auth salah/kosong. |
| `403 Forbidden` | Operasi ditolak terlepas dari identitas (mis. dibatasi kebijakan/role di server). |
| *(gagal konek, bukan dari Flowable)* | Biasanya CORS belum diizinkan server untuk origin halaman ini, server tidak aktif/tidak terjangkau, atau BPMN Studio berjalan di sandbox yang memblokir panggilan API eksternal. Browser melaporkan ini sebagai exception generik (`Failed to fetch`), bukan sebagai response HTTP — makanya BPMN Studio mengarahkan ke fallback "Salin Perintah curl" di pesan errornya. |

### Kenapa ada dua cara (Deploy Langsung vs Salin Perintah curl)?

`fetch()` dari browser tunduk pada kebijakan CORS server tujuan dan pada pembatasan jaringan lingkungan tempat BPMN Studio dijalankan. Perintah `curl` yang setara dijalankan dari terminal pengguna sendiri, sehingga selalu berhasil selama URL/kredensial benar dan server terjangkau dari jaringan pengguna — ini alasan kenapa BPMN Studio selalu menyediakan versi curl sebagai fallback untuk setiap operasi tulis (create/update/delete) ke Flowable, tidak hanya di fitur Deploy.
