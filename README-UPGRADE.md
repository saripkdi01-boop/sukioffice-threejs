# SukiOffice v3 Live — Upgrade Notes & Deployment

## Apa yang berubah dari v2.4 ke v3.0

- Scene default gelap (dark mode) + teal accents, fog dinamis halus,
  env mapping via RoomEnvironment + PMREM (tanpa file HDR eksternal),
  god rays tetap pakai bidang cahaya volumetrik palsu dari v2.
- Kantor hidup: 12 persona avatar demo prosedural (bukan model GLTF
  manusia asli, bukan 12 karyawan nyata), tersebar di 9 ruangan yang
  layout-nya TIDAK diubah: Lobby, Co-working, CEO, Meeting 1,
  Meeting 2 (Video Wall), Collab/Huddle, Kitchen, Prayer Room, Restroom.
- Animasi prosedural per aktivitas: idle (napas + stretching),
  typing (tangan mengetik, kepala menunduk), walking (ayunan kaki/
  tangan, pindah ke spot lain di ruangan yang sama), meeting
  (angguk + condong ke meja), coffee sip (tangan ke mulut),
  prayer (membungkuk berirama).
- EmployeeManager di `index.html` (bukan file terpisah, supaya tetap
  1 file statis tanpa build): roster, spot ruangan, lampu ruangan
  yang terang sesuai jumlah penghuni, perilaku ganti aktivitas
  tiap 25–45 detik, simpan state ke localStorage key
  `sukioffice-live-v3` (ruangan, aktivitas, posisi) dan muat lagi
  saat reload.
- Interaksi: klik avatar → kartu profil (nama, role, ruangan,
  aktivitas, kalimat simulasi) + tombol Undang ke Meeting 2;
  klik laptop/meja → Workspace View (layar simulasi + bisa mengetik);
  MiniMap → ketuk avatar lalu ketuk ruangan tujuan = teleport
  dengan fade (drag di desktop, tap di HP — drag murni di layar
  sentuh tidak diandalkan); hover/ketuk avatar → speech bubble
  simulasi; handoff file → pilih file + target, simulasi serah terima.
- Pointer: ring teal di lantai mengikuti raycast (pengganti shader
  cursor kustom penuh — shader cursor 3D penuh ditunda supaya
  tidak menambah draw call di HP).
- Mode Jalan desktop: PointerLockControls + WASD, hanya di desktop.
  Di HP, Pointer Lock tidak bekerja — cara jalan adalah tombol
  ruangan + MiniMap teleport (sudah dites di A76 di v2.3).

## Apa yang TIDAK (belum) ada di v3.0 — jujur

- Model manusia GLTF asli dari Blender: BELUM ADA. Folder `models/`
  hanya berisi README penanda. Lihat `instructions.md` untuk jalur
  export-nya. Jangan klaim ke investor/pengguna bahwa avatarnya
  adalah model Blender asli sebelum file `.glb` benar-benar ada.
- Karyawan nyata: roster adalah persona demo. Ganti hanya setelah
  tim nyata menyetujui nama dan role-nya.
- Cannon.js penuh: diganti AABB sederhana demi FPS HP. Titik
  integrasi cannon-es ada di `instructions.md`.
- IndexedDB: tidak dipakai — JSON state <2KB, localStorage cukup.
  IndexedDB baru masuk akal bila cache GLTF besar.
- Three.js r18x: tetap r160 (sudah > r134 yang diminta) supaya
  perilaku OrbitControls/RoomEnvironment yang teruji di v2.4 tidak
  berubah di HP. Naik versi = retest penuh di HP.
- 8 avatar sekaligus: total roster 12, yang dianimasikan penuh
  hanyalah avatar di ruangan yang sedang dibuka (sisanya disimpan
  sebagai state, bukan di-render penuh) — ini yang menjaga FPS.
  Bila 12 avatar di-render penuh di satu ruangan, FPS HP akan jatuh.

## Deployment ke Vercel (sama seperti v2.x)

- Repo: `saripkdi01-boop/sukioffice-threejs`, branch `main`.
- Vercel project `sukioffice-threejs`: Framework Other, Build
  Command kosong, Output `./`. Push ke main = auto-deploy.
- File yang di-push untuk v3.0: `index.html`, `instructions.md`,
  `README-UPGRADE.md`, `models/README.md`. File lain di folder
  lokal (mis. `index-v24-backup.html`, `DEPLOY-STATUS.md`) TIDAK
  di-push, supaya repo tetap bersih dan Vercel tidak membingungkan.
- Verifikasi setelah deploy: judul v3 Live, panel terlipat default,
  FPS muncul dalam 1 detik, klik avatar menampilkan kartu profil,
  reload mempertahankan ruangan/aktivitas avatar yang dipilih.

## Monetisasi (arah, bukan klaim sudah laku)

v3 Live ini paling masuk akal dijual/ditawarkan sebagai:
- Fitur showroom di GrahaOS (tour kantor developer properti),
- Demo interaktif di Hermes (avatar + workspace view sebagai
  antarmuka agen), dan
- Galeri produk SukiApps yang sudah ada di Lobby v2.3.
Harga, paket, dan janji ke pelanggan baru boleh ditulis setelah
versi GLTF asli + tim nyata sudah ada — jangan jual v3.0 demo
sebagai produk final.
