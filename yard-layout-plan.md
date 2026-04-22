# Yard Layout Implementation Plan

Dokumen ini adalah rencana implementasi untuk menyempurnakan `index.html` menjadi yard layout designer untuk parkiran mobil. Fokus utamanya adalah satu yard utama, slot dengan informasi line dan row, status slot, drag and drop yang stabil, rotate, save/load, dan panel editing.

## Target Akhir

Aplikasi menjadi editor layout yard yang memungkinkan user:

- Mengelola satu yard utama.
- Membuat slot parkir di dalam yard.
- Mengatur informasi slot seperti line, row, label, dan status.
- Melihat status slot: tersedia, terpesan, dan terisi.
- Drag and drop slot di canvas.
- Rotate slot secara 90 derajat atau bebas dengan handle.
- Generate banyak slot sekaligus berdasarkan line dan row.
- Menyimpan dan memuat layout dalam format JSON.
- Melihat statistik total dan per status.

## Struktur Data Target

### Yard

```js
{
  id,
  name,
  x,
  y,
  width,
  height,
  rotation
}
```

### Slot

```js
{
  id,
  yardId,
  line,
  row,
  label,
  status,
  x,
  y,
  width,
  height,
  rotation
}
```

Nilai `status`:

```js
"available" // tersedia
"reserved"  // terpesan
"occupied"  // terisi
```

## Phase 1 - Rapikan Fondasi Existing

Tujuan phase ini adalah memperbaiki struktur yang sudah ada di `index.html` tanpa mengubah besar-besaran.

- [ ] Samakan class selected antara CSS dan JavaScript.
- [ ] Perbaiki karakter rusak seperti `Rotate 90Â°` dan ikon rotate yang rusak.
- [ ] Ubah istilah internal dari `spot` menjadi `slot` secara bertahap.
- [ ] Ganti `occupied: boolean` menjadi `status: string`.
- [ ] Buat constant `SLOT_STATUSES`.
- [ ] Update render slot agar menampilkan label status yang benar.
- [ ] Update statistik agar menghitung total, tersedia, terpesan, dan terisi.
- [ ] Update legend agar sesuai dengan status slot.
- [ ] Update mini map agar warna slot mengikuti status.

Contoh constant:

```js
const SLOT_STATUSES = {
  available: { label: 'TERSEDIA', color: '#22c55e' },
  reserved: { label: 'TERPESAN', color: '#f59e0b' },
  occupied: { label: 'TERISI', color: '#ef4444' }
};
```

## Phase 2 - Tambahkan Konsep Yard

Tujuan phase ini adalah menambahkan satu area yard utama sebagai container/zona visual.

- [ ] Tambahkan state yard utama.
- [ ] Tambahkan tombol edit nama yard.
- [ ] Simpan yard utama sebagai metadata layout.
- [ ] Tidak perlu render tanda batas yard di canvas.
- [ ] Hubungkan setiap slot ke yard utama.
- [ ] Saat slot dibuat, gunakan yard utama sebagai parent.
- [ ] Hindari UI multi-yard agar workflow tetap sederhana.

Catatan implementasi:

- Slot tetap bisa absolute terhadap canvas agar drag/drop lebih sederhana.
- Yard berfungsi sebagai zona logis tunggal tanpa tanda batas visual.
- Validasi boundary slot terhadap yard bisa ditambahkan setelah drag dasar stabil.

## Phase 3 - Slot Dengan Line dan Row

Tujuan phase ini adalah memastikan setiap slot memiliki informasi line dan row.

- [ ] Tambahkan field `line` pada slot.
- [ ] Tambahkan field `row` pada slot.
- [ ] Tambahkan field `label` otomatis berdasarkan line dan row.
- [ ] Tampilkan label slot dalam format seperti `A-01`.
- [ ] Tampilkan informasi tambahan seperti `Line A / Row 01`.
- [ ] Tambahkan edit line dan row pada context menu atau property panel.
- [ ] Update search agar bisa mencari berdasarkan label, line, dan row.

Format label yang disarankan:

```text
A-01
A-02
B-01
B-02
```

## Phase 4 - Multi Slot Generator

Tujuan phase ini adalah menyempurnakan fitur `+ Multi Spot` menjadi generator slot berbasis line dan row.

- [ ] Ubah dialog `Tambah Multi Spot` menjadi `Tambah Multi Slot`.
- [ ] Gunakan yard utama sebagai tujuan slot.
- [ ] Tambahkan input prefix line, misalnya `A`.
- [ ] Tambahkan input jumlah line.
- [ ] Tambahkan input jumlah row per line.
- [ ] Tambahkan input start row.
- [ ] Tambahkan input posisi awal X dan Y.
- [ ] Tambahkan input gap X dan gap Y.
- [ ] Tambahkan input status default.
- [ ] Generate label otomatis.
- [ ] Setelah generate, update canvas, sidebar, statistik, dan mini map.

Contoh hasil:

```text
A-01, A-02, A-03
B-01, B-02, B-03
C-01, C-02, C-03
```

## Phase 5 - Sempurnakan Drag and Drop

Tujuan phase ini adalah membuat drag/drop terasa stabil dan predictable.

- [ ] Pertimbangkan migrasi dari mouse event ke pointer event.
- [ ] Tambahkan snap grid yang bisa dikonfigurasi.
- [ ] Tambahkan toggle `Snap to Grid`.
- [ ] Pastikan drag tetap akurat setelah slot di-rotate.
- [ ] Clamp slot agar tidak keluar canvas.
- [ ] Clamp slot agar tidak keluar yard aktif jika fitur boundary yard diaktifkan.
- [ ] Update posisi hanya pada slot yang sedang di-drag.
- [ ] Update statistik/list/mini map setelah drop.
- [ ] Tambahkan auto-scroll saat drag mendekati tepi viewport.
- [ ] Tambahkan multi-select untuk memindahkan beberapa slot sekaligus.

Prioritas:

1. Single slot drag stabil.
2. Drag tetap benar setelah rotate.
3. Snap grid stabil.
4. Multi-select drag.
5. Auto-scroll.

## Phase 6 - Sempurnakan Rotate

Tujuan phase ini adalah membuat rotate lebih rapi dan tidak menggeser slot secara aneh.

- [ ] Perbaiki rotate handle agar muncul saat slot selected.
- [ ] Gunakan `transform-origin: center center`.
- [ ] Simpan rotation dalam derajat.
- [ ] Tambahkan rotate 90 derajat dari context menu.
- [ ] Tambahkan rotate -90 derajat.
- [ ] Tambahkan reset rotation.
- [ ] Tambahkan rotate bebas via handle.
- [ ] Tambahkan snap angle, misalnya 15 derajat.
- [ ] Setelah rotate, hitung rotated bounds.
- [ ] Clamp slot agar tetap berada di canvas.
- [ ] Update mini map agar bisa menggambar slot yang di-rotate.

Context menu yang disarankan:

```text
Ubah Nama
Ubah Line/Row
Set Tersedia
Set Terpesan
Set Terisi
Rotate 90 deg
Rotate -90 deg
Reset Rotation
Hapus
```

## Phase 7 - Status Slot

Tujuan phase ini adalah mengganti toggle status sederhana menjadi status slot yang lengkap.

- [ ] Ganti `toggleStatus()` menjadi `setSlotStatus(status)`.
- [ ] Tambahkan pilihan status di context menu.
- [ ] Tambahkan warna berbeda untuk setiap status.
- [ ] Tampilkan status pada body slot.
- [ ] Tampilkan status pada daftar slot.
- [ ] Tambahkan filter status di sidebar.
- [ ] Update statistik status.

Mapping warna yang disarankan:

```js
available: '#22c55e'
reserved: '#f59e0b'
occupied: '#ef4444'
```

## Phase 8 - Property Panel

Tujuan phase ini adalah mengganti penggunaan `prompt()` dengan panel editing yang lebih nyaman.

- [ ] Tambahkan panel detail slot di sidebar.
- [ ] Saat slot selected, tampilkan field slot.
- [ ] Field yang bisa diedit:
  - [ ] Label
  - [ ] Yard
  - [ ] Line
  - [ ] Row
  - [ ] Status
  - [ ] X
  - [ ] Y
  - [ ] Width
  - [ ] Height
  - [ ] Rotation
- [ ] Update canvas saat field berubah.
- [ ] Validasi input angka.
- [ ] Pastikan perubahan langsung tersimpan di state memory.

## Phase 9 - Save, Load, dan Migrasi Data

Tujuan phase ini adalah memastikan layout bisa disimpan dan dimuat kembali dengan schema baru.

Format JSON target:

```js
{
  version: 2,
  yardIdCounter,
  slotIdCounter,
  yards: [],
  slots: [],
  canvas: {
    width,
    height,
    gridSize
  }
}
```

Checklist:

- [ ] Update `saveLayout()` untuk schema baru.
- [ ] Update `loadLayout()` untuk schema baru.
- [ ] Tambahkan migrasi dari format lama yang masih memakai `spots`.
- [ ] Jika file lama tidak punya yard, buat default yard bernama `Main Yard`.
- [ ] Pastikan line, row, status, posisi, ukuran, dan rotation tetap tersimpan.
- [ ] Validasi file JSON sebelum dimuat.
- [ ] Tampilkan pesan error yang jelas jika load gagal.

## Phase 10 - UX dan Visual Polish

Tujuan phase ini adalah membuat editor lebih mudah dipakai.

- [ ] Yard utama tidak perlu tanda batas visual di canvas.
- [ ] Slot memiliki layout teks yang ringkas dan tidak bertabrakan.
- [ ] Sidebar memiliki filter yard, status, dan search.
- [ ] Toolbar dipisahkan berdasarkan action:
  - [ ] Yard actions
  - [ ] Slot actions
  - [ ] View actions
- [ ] Tambahkan zoom in/out.
- [ ] Tambahkan reset view.
- [ ] Tambahkan fit to content.
- [ ] Tambahkan collision warning saat slot bertabrakan.
- [ ] Mini map mengikuti warna status.

## Phase 11 - Testing Manual

Skenario manual yang harus dicek:

- [ ] Tambah yard baru.
- [ ] Tambah satu slot ke yard aktif.
- [ ] Tambah 10 x 10 slot dengan generator.
- [ ] Drag satu slot.
- [ ] Drag slot setelah rotate.
- [ ] Rotate slot 90 derajat.
- [ ] Rotate slot bebas via handle.
- [ ] Ubah status slot menjadi tersedia.
- [ ] Ubah status slot menjadi terpesan.
- [ ] Ubah status slot menjadi terisi.
- [ ] Filter slot berdasarkan status.
- [ ] Search slot berdasarkan label.
- [ ] Edit line dan row.
- [ ] Save layout.
- [ ] Load layout.
- [ ] Load file format lama.
- [ ] Pastikan mini map tetap benar.
- [ ] Pastikan statistik tetap benar.

## Urutan Implementasi Yang Disarankan

1. Refactor status slot dari `occupied` ke `status`.
2. Perbaiki selected state, rotate handle, dan karakter rusak.
3. Update statistik, legend, daftar slot, dan mini map.
4. Tambahkan line dan row pada slot.
5. Sempurnakan multi slot generator.
6. Tambahkan konsep single yard.
7. Sempurnakan drag and drop.
8. Sempurnakan rotate.
9. Tambahkan property panel.
10. Update save/load dan migrasi data.
11. Polish UI dan testing manual.

## Catatan Teknis

- Pertahankan implementasi sebagai single-file `index.html` terlebih dahulu agar sesuai dengan mockup saat ini.
- Jika file mulai terlalu besar, baru pecah menjadi `style.css` dan `script.js`.
- Hindari refactor besar sekaligus. Lakukan bertahap agar fitur existing tetap bisa dites setelah setiap phase.
- Gunakan state utama sebagai sumber kebenaran, lalu render ulang UI dari state.
- Untuk performa, update DOM hanya pada slot yang berubah jika jumlah slot sudah besar.
