# SukiOffice Virtual — Phase Zero Audit (Masterprompt)

Tanggal audit: 27 Okt 2026, WITA. Auditor: Upin (Muse), mode read-only dulu,
baru stabilisasi kecil di v3.1 setelah audit ini.
Sumber yang benar-benar diperiksa: file lokal `index.html` (61.734 byte),
`DEPLOY-STATUS.md`, `instructions.md`, `README-UPGRADE.md`, struktur
`models/`, riwayat deploy Vercel, dan screenshot tes HP Suki 27 Okt 07:54.
TIDAK ada klaim dari memori yang tidak diverifikasi ke file/deploy di sini.

## 1. Arsitektur & entry point — TERBUKTI

- Satu file statis `index.html`, tanpa build, tanpa package.json,
  tanpa framework React, tanpa backend, tanpa database server.
- Repo GitHub: `saripkdi01-boop/sukioffice-threejs`, branch `main`.
- Vercel project `sukioffice-threejs` (id `prj_QDLJ9nZZBEszLyjbQOtsw14c5oJb`):
  Framework Other, Build Command kosong, Output `./`, push ke main =
  auto-deploy. Deploy terakhir sebelum audit: `dpl_Fn4sUZmnJExS984cgjjXRtoWdYir`,
  commit `a46eb5f` (v3 Live), state READY, production.
- Dependensi runtime: Three.js r160 via jsdelivr importmap, plus
  `OrbitControls`, `RoomEnvironment`, `PointerLockControls` dari
  `three/addons/`. Tidak ada dependensi npm, tidak ada Cannon.js,
  tidak ada IndexedDB, tidak ada API eksternal selain CDN jsdelivr.
- State: hanya `localStorage` key `sukioffice-live-v3` (ruangan,
  aktivitas, posisi avatar). Tidak ada auth, tidak ada multi-user,
  tidak ada sinkronisasi antar perangkat.
- Aset 3D: prosedural dari kode (Box/Cylinder/Plane/CanvasTexture).
  Folder `models/avatars|rooms|props` KOSONG kecuali `README.md`.
  Tidak ada file `.glb` SukiOffice di repo.

## 2. Inventaris fitur — klasifikasi masterprompt

Legenda: WORKING = terverifikasi berfungsi. PARTIAL = sebagian jalan.
SIMULATED = tampilan saja, tanpa eksekusi bisnis nyata.
BROKEN = fitur yang ada tapi gagal. MISSING = belum ada.
UNVERIFIED = bukti belum cukup.

| Fitur | Status | Bukti |
|---|---|---|
| 9 ruangan + navigasi tombol | WORKING | v2.3 screenshot HP: Lobby terlihat, tombol 9 ruangan ada |
| Galeri produk Lobby (8 panel klik) | WORKING | Screenshot 07:54: klik Marketplace → info muncul di panel |
| FPS + draw calls | WORKING | Screenshot: FPS 60, Draw calls 75 (v2.3, A76) |
| Panel UI lipat v2.4 | UNVERIFIED di HP | Kode ada, deploy READY, belum ada screenshot tes HP v2.4 |
| Avatar v3 Live (12 roster) | UNVERIFIED di HP | Kode ada, sintaks OK, deploy READY, belum ada screenshot tes HP v3 |
| Animasi avatar (typing/walking/meeting/coffee/prayer) | SIMULATED | Animasi prosedural di loop, bukan bukti kerja nyata |
| Persist avatar (localStorage) | PARTIAL | Kode simpan/muat ada, belum dites reload di HP di v3 |
| Profil avatar + Undang ke Meeting | PARTIAL | Kode ada, belum dites klik avatar di HP di v3 |
| Workspace View (bisa ketik) | SIMULATED | Layar simulasi + textarea, bukan environment proyek asli |
| MiniMap teleport + fade | PARTIAL | Kode ada, belum dites di HP di v3 |
| Speech bubble | SIMULATED | Kalimat demo hardcode per avatar, bukan percakapan nyata |
| Handoff file | SIMULATED | Selector + pesan simulasi, tidak ada file yang benar-benar berpindah |
| Mode Jalan PointerLock | PARTIAL | Kode ada, hanya desktop; di HP sengaja ditolak dengan alert |
| Dark scene + env RoomEnvironment | UNVERIFIED di HP | Kode ada + try/catch, belum ada screenshot v3 |
| Kitchen steam, prayer timer | SIMULATED | Animasi + timer demo 180 detik, bukan jadwal sholat nyata |
| Meeting video wall | SIMULATED | CanvasTexture 4 avatar + grafik, bukan video call nyata |
| Task & project engine | MISSING (sebelum v3.1) | Tidak ada di v3.0 — ditambahkan sebagai engine LOKAL di v3.1 |
| Auth, organisasi, role, permission | MISSING | Tidak ada kode auth di index.html |
| Backend API, database relasional | MISSING | Tidak ada endpoint; localStorage bukan database kolaboratif |
| AI agent execution (tool, approval, log) | MISSING | Tidak ada provider/tool terhubung di SukiOffice |
| Finance, CRM, HR, procurement | MISSING | Hanya panel produk statis, bukan modul bisnis |
| CEO Command Center (KPI nyata) | MISSING (sebelum v3.1) | v3.1 menambahkan dashboard dari data task LOKAL saja |
| Tes otomatis | MISSING | Tidak ada test runner, hanya `node --check` sintaks |
| Aksesibilitas (keyboard, reduced-motion) | PARTIAL (sebelum v3.1) | Tombol bisa difokuskan, tapi belum ada shortcut & reduced-motion |

## 3. Keamanan — hasil scan

- Scan `index.html` untuk pola `api_key/secret/token/password`: TIDAK ADA
  kecocokan. Tidak ada kredensial yang tertanam di file ini.
- Risiko yang MASIH ADA dan harus dicatat, bukan disembunyikan:
  - Semua data di localStorage browser: siapa pun yang memegang HP/
    browser bisa membaca/mengubah state avatar dan (mulai v3.1) task.
    Ini BUKAN tempat untuk data keuangan, pelanggan, atau HR sensitif.
  - Tidak ada auth = tidak ada batasan siapa yang boleh mengubah state.
    Untuk demo single-user ini dapat diterima; untuk produksi
    multi-user, ini adalah blocker Phase 3, bukan sekadar kekurangan.
  - CDN jsdelivr adalah satu-satunya dependensi eksternal: bila CDN
    down, modul Three.js gagal dan halaman blank (sudah ada pesan
    error di panel sejak v2.1, tapi tetap single point of failure).

## 4. Performa & mobile — bukti

- v2.3 di A76: FPS 60, Draw calls 75. Itu baseline yang bagus.
- Risiko v3: 12 avatar + 9 PointLight ruangan + env map menaikkan
  draw calls dan beban GPU. Anggaran yang ditetapkan untuk v3.1:
  draw calls < 150 di Lobby, FPS target ≥30 di A76. Bila tes HP
  menunjukkan di bawah itu, langkah pertama adalah mengurangi
  PointLight, bukan menambah efek baru.
- WebGL gagal: sebelum v3.1, bila WebGL gagal, pengguna hanya
  melihat pesan error — fungsi bisnis tidak bisa diakses. Ini
  melanggar prinsip masterprompt §4.3/§15 dan diperbaiki di v3.1
  dengan panel fallback tanpa 3D.

## 5. Peta dependensi (sederhana, sesuai kenyataan)

```
index.html (satu file)
  ├─ jsdelivr: three@0.160.0 + addons (Orbit/RoomEnv/PointerLock)
  ├─ localStorage: sukioffice-live-v3, sukioffice-tasks-v31
  ├─ Vercel static hosting (tanpa serverless function)
  └─ GitHub main → auto-deploy
Tidak ada: Supabase, API, auth provider, AI provider, payment, email.
```

## 6. Rencana bertahap (dari audit ini, bukan janji)

- Phase 1 (v3.1, dikerjakan setelah audit ini): stabilisasi +
  fallback tanpa WebGL + reduced-motion + keyboard + Task Engine
  LOKAL dengan transisi valid. Rollback: file backup v2.4 lokal
  (`index-v24-backup.html`) dan commit GitHub sebelumnya.
- Phase 2: polish UI/operasional panel tanpa menambah backend palsu.
- Phase 3 (BUTUH KEPUTUSAN SUKI, belum dikerjakan): backend +
  auth + database. Opsi yang realistis untuk Suki (tanpa server
  sendiri): Supabase (sudah dipakai SukiApps) untuk auth + tabel
  task/project, atau tetap lokal bila SukiOffice memang hanya
  demo single-user. Jangan mulai Phase 3 sebelum Suki memilih
  opsi ini — karena itu menentukan seluruh arsitektur.
- Phase 4–8 masterprompt (workflow engine, AI execution, modul
  departemen, sinkronisasi 3D↔data, production readiness):
  MISSING, tidak boleh diklaim selesai dari v3.1.

## 7. Kesimpulan audit

Yang benar-benar hidup hari ini: kantor 3D prosedural yang bisa
dijelajahi, galeri produk yang bisa diklik, dan (di v3) avatar
demo yang bergerak. Yang belum hidup: seluruh lapisan bisnis
(task nyata multi-user, approval yang mengikat, finance, CRM,
AI execution). v3.1 di bawah ini menutup satu celah itu secara
jujur — task engine LOKAL dengan aturan transisi — tanpa
berpura-pura menjadi backend produksi.

# AUDIT v3.3 — Optimalisasi (cabang optimasi-v33)

## Baseline statis v3.2 (dari file, terverifikasi)
- `index.html` v3.2: 88.555 byte / 1.246 baris (HEAD 998038b).
- Renderer: antialias off, pixelRatio min(dpr, 1.5), shadow PCFSoft 1024, 1 DirectionalLight (castShadow) + Hemisphere + PointLight per ruangan.
- FAKTA KODE (bukan dugaan): `mat()` bikin material baru per mesh; `box()`/`cyl()` bikin geometri baru per mesh — TANPA sharing. `buildAvatar` bikin ulang geometri kaki/lengan/torso/kepala/rambut per avatar (12x).
- Semua mesh `castShadow + receiveShadow` → shadow pass menggandakan draw calls ruangan aktif.
- 9 ruangan + 12 avatar dibangun eager saat load (bukan lazy).
- Yang sudah bagus: view per-ruangan (visible toggle), avatar ruangan lain di-skip dari animasi (`if (!vis) return`), fog murah, tanpa tekstur jaringan/HDR, 1 CanvasTexture helper, fallback tanpa WebGL, reduced-motion dihormati, state localStorage <2 KB.

## Baseline live v3.2 (TERUKUR via browser, 27 Okt 11:19 WITA, Lobby)
- Meta persis: "FPS: 6 • Draw calls: 138 • v3.2" lalu FPS 7, draw calls tetap 138.
- 3D tampil penuh pada pemuatan tab pertama (tanpa penantian tambahan teramati; tanpa angka stopwatch persis — tidak dikarang).
- Ruangan pertama: Lobby (tombol Lobby tersorot di MiniMap; area resepsionis/sofa; avatar Fajar + Rudi di lobby).
- Error: NIHIL. Tidak ada teks error, tidak ada "Memuat 3D..." macet.
- Panel lain saat itu: "👥 Crew aktif: 6/12", "📋 Tugas jalan: 1", "🚧 Blocked: 1".
- CATATAN KEJUJURAN ANGKA: FPS 6–7 diukur di browser VM (kemungkinan software rendering tanpa GPU) — BUKAN angka FPS di HP pengguna. Jangan sajikan 6–7 sebagai "FPS situs untuk pengguna". Angka yang mesin-independen dan bisa dipakai sebagai baseline: **draw calls 138** (Lobby). FPS riil di HP (A76) tetap harus dari tes tap Suki — v2.3 dulu 60 FPS di A76, tapi itu v2.3, bukan v3.2.
- Implikasi: 138 ≈ mesh terlihat Lobby × 2 (color pass + shadow pass). Shadow trim v3.3 (frame jendela + tali pendant) memangkas caster shadow pass; cache tidak mengubah jumlah call tapi memangkas memori/state; DPR adaptif menjaga FPS di GPU lemah.

## Perubahan v3.3 (cabang optimasi-v33, syntax OK via node --check, +2.758 byte → 91.313 byte)
1. Cache geometri (`geoBox/geoCyl/geoSphere/geoPlane`) + cache material (`mat()` keyed by warna/sifat) — semua helper + avatar pakai cache. Satu-satunya material yang dimutasi per-frame (steam) sengaja TIDAK di-cache.
2. Shadow trim: frame jendela tipis + tali pendant (`castShadow: false`) — bayangannya tak terlihat di shadow map 1024. Furnitur + avatar tetap casting.
3. DPR adaptif: FPS <45 selama 3 detik → turun 1.5 → 1.25 → 1.0; FPS >55 selama 10 detik → naik lagi (maks = DPR awal perangkat). Dilewati saat tab tersembunyi + reduced-motion. Log via console.info.
4. Preconnect `cdn.jsdelivr.net` (CDN modul Three.js).
5. Avatar: geometri dibagi semua avatar; material kaki + rambut dibagi (konstan); material kemeja dibagi torso + kedua lengan (warna sama).

## TIDAK diubah (sesuai masterprompt)
- Layout 9 ruangan, gaya visual (dark + teal, fog, light shaft), isi brand wall/galeri, semua fitur (profil, workspace, MiniMap, task engine lokal, CEO dashboard, kanban, denah 2D, tema siang/malam).
- Fallback tanpa WebGL, reduced-motion, localStorage `sukioffice-live-v3`.
- TANPA Phase 3 (backend/auth/DB — keputusan Suki menggantung), TANPA GLB manusia (T-006 blocked), TANPA Cannon.js, TANPA IndexedDB, TANPA audio langkah, TANPA shader cursor, TANPA drag file 3D.
- TANPA deploy ke main — kerja di cabang, laporan sebelum/sesudah dulu, merge/deploy menunggu "gas" kedua Suki.

## Verifikasi (menyusul setelah angka baseline datang)
- Preview cabang via browser: FPS + draw calls panel, jalur tes HP (hard refresh → avatar → profil → teleport → workspace → reload persist), jalur tanpa WebGL (daftar tugas terbaca).
