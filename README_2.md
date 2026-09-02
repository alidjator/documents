# BPMN Studio (Vue 3 + TypeScript)

| Dibuat oleh | Tanggal |
| - | - |
| alidjator@gmail.com | 2 September 2026 |

Editor diagram BPMN berbasis Vue 3 + TypeScript + Vuetify yang terintegrasi
dengan Flowable REST API. Didesain untuk disematkan (embed) sebagai satu
komponen `<BpmnEditor>` ke dalam project Vue yang sudah berjalan — lihat
[Integrasi ke Project Lain](#integrasi-ke-project-lain).

Untuk referensi lengkap endpoint Flowable + parameter yang dikirim tiap
fitur, lihat [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md).

## Fitur

| Fitur | Deskripsi |
| - | - |
| Editor Diagram | Canvas BPMN (bpmn-js) dengan toolbar Baru/Buka/Undo/Redo/Export, palette drag-and-drop elemen BPMN, dan kontrol zoom. |
| Deploy ke Flowable | Kirim diagram sebagai deployment baru — key proses yang sama otomatis menjadi versi baru tanpa menimpa versi lama. |
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

## Arsitektur

**Stack:** Vue 3 (`<script setup>` + TypeScript), Vuetify 3, Pinia, bpmn-js,
Vite.

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

### Struktur direktori

```
src/
  moddle/             Ekstensi moddle BPMN untuk atribut flowable: (assignee,
                       candidateGroups, dll — wajib ada supaya diagram bisa
                       di-deploy & dieksekusi di Flowable), plus XML diagram
                       kosong default untuk "Baru".
  stores/             Pinia store: diagram.ts (filename & status "dirty"
                       diagram yang sedang dibuka), flowable.ts (baseUrl/
                       username/password/authHeader, dari .env).
  types/flowable.ts   Tipe TypeScript untuk request/response Flowable REST.
  composables/        Satu use*.ts per fitur (logika + state reaktif) —
                       lihat DEVELOPER_GUIDE.md untuk detail endpoint tiap
                       fitur.
  components/         BpmnEditor.vue (komponen utama) + EditorToolbar.vue,
                       dan satu XxxDialog.vue (plus komponen baris pendukung
                       bila ada, mis. TaskRow.vue) per fitur.
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

Komponen `<BpmnEditor>` menerima dua prop opsional:

```vue
<BpmnEditor
  :initial-xml="xmlBpmnAnda"
  initial-filename="proses-saya.bpmn"
/>
```

Kalau `initial-xml` tidak diisi, editor mulai dengan diagram kosong (satu
Start Event) — sama seperti tombol "Baru" di toolbar.

Langkah integrasi ke project Vue + TypeScript yang sudah berjalan:

1. Salin folder `src/` ke project Anda (atau jadikan package terpisah
   kalau mau dipakai di lebih dari satu project).
2. Install dependency yang sama: `bpmn-js`, `vuetify`, `@mdi/font`,
   `pinia`. Kalau project Anda belum pakai Pinia, state di
   `stores/diagram.ts` & `stores/flowable.ts` bisa diubah jadi `ref()`
   biasa tanpa mengubah cara komponen memakainya.
3. Tambahkan tiga variabel `VITE_FLOWABLE_*` (lihat [Konfigurasi
   Flowable](#konfigurasi-flowable-env)) ke `.env` project Anda.

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
- Build produksi saat ini menghasilkan satu JS bundle besar (~1.3MB,
  sebagian besar dari bpmn-js + Vuetify + font MDI). Kalau ukuran bundle
  jadi masalah di project Anda, pertimbangkan code-splitting (`import()`
  dinamis untuk `<BpmnEditor>`) atau plugin tree-shaking Vuetify.
