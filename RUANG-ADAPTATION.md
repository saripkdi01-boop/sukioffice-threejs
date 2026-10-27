# Adaptasi Desain Ruang ke SukiOffice — v3.2

Sumber: https://github.com/yugienugraha/ruang
Lisensi sumber: MIT © yugienugraha (tertera di repo sumber).
Cara adaptasi: dari README/desain publiknya, BUKAN fork kode.
Tidak ada file sumber Ruang yang disalin verbatim ke repo ini —
semua kode di `index.html` ditulis ulang untuk arsitektur SukiOffice
(satu file statis, tanpa server), mengikuti pola logikanya.

## Pola Ruang yang diadaptasi (dan di mana di SukiOffice)

1. **HUD di atas kantor** — Ruang: crew aktif, gateway, tugas,
   cron berikutnya, failed reads. SukiOffice: chip di bawah header
   (Crew aktif, Tugas jalan, Blocked, View 3D/2D, Tema) yang
   diperbarui tiap detik dari data lokal.
2. **Panel samping 3 tab** — Ruang: Crew / Stats / Activity.
   SukiOffice: tombol 🗂️ Panel Ruang → panel kanan dengan tab
   yang sama. Crew = klik stasiun untuk fokus ke avatar;
   Stats = runtime, aktivitas, status tugas, up next;
   Activity = 20 log terakhir (lokal, bukan sesi Hermes).
3. **Tombol overlay tanpa meninggalkan kantor** — Ruang: Tasks,
   Calendar, Tokens dibuka di atas Office, Esc/✕ menutup.
   SukiOffice: Tasks, Panel Ruang, Workspace, dan Profil dibuka
   di atas 3D; panel utama tetap bisa dilipat (v2.4).
4. **View 3D/2D yang diingat browser** — Ruang: 3D default, 2D
   bila WebGL tidak ada, pilihan diingat. SukiOffice: tombol
   🗺️ 3D/2D, disimpan di `localStorage sukioffice-view`.
   Denah 2D dibuat dengan CSS/HTML saja (tanpa aset gambar),
   seperti Ruang: denah Workspace + Lounge-nya juga CSS-only.
5. **Tema siang/malam** — Ruang: siang terang, malam lampu
   menyala. SukiOffice: tombol Tema, disimpan di
   `localStorage sukioffice-theme`; malam = background gelap
   + fog gelap, siang = background terang + matahari lebih kuat.
6. **Avatar berjalan lewat lorong, bukan menembus furniture** —
   Ruang: agen berjalan lewat aisle dan pintu, tidak menembus
   meja/dinding. SukiOffice: walking memakai path
   `[lorong (0, 2.8), tujuan]` sebelum ke spot akhir.
7. **Idle wandering tiap ~32 detik ke stop yang jelas** —
   Ruang: tiap 32 detik idle pindah ke lounge, ping-pong,
   arcade, galon, bakso, kopi, bendera, dengan bubble tujuan.
   SukiOffice: tiap ~32 detik avatar idle wandering ke
   Lounge Lobby / Pantry-Galon, dengan bubble "Menuju: ..."
   dan log Activity. Avatar di ruangan lain pindah diam-diam
   TANPA memindah view pengguna (karena SukiOffice memakai
   view per-ruangan, bukan satu gedung terbuka seperti Ruang).
8. **Meeting: presenter + kursi** — Ruang: yang pertama
   presentasi di whiteboard, sisanya duduk. SukiOffice:
   undangan ke Meeting 2 memindahkan avatar ke ruangan itu
   dengan aktivitas meeting; presenter adalah avatar pertama
   yang sudah ada di ruangan itu (spot pertama).
9. **Kanban board** — Ruang: kolom triage→done, search,
   filter assignee, klik kartu = detail penuh. SukiOffice:
   tombol Kanban di panel Tugas, kolom mengikuti status
   SukiOffice (Backlog→Cancelled), klik kartu = detail.
   Search/filter assignee belum ada di v3.2 — itu backlog.
10. **Tombol Tidur** — Ruang: 💤 Tidur mengirim idle ke tempat
    tidur, diingat browser. SukiOffice: 💤 Tidur menghentikan
    wandering avatar idle, diingat di `localStorage
    sukioffice-sleep`. Tidak ada kamar dorm Lantai 2 di
    SukiOffice (lihat di bawah), jadi tidur = idle diam di
    tempat, bukan pindah ke dorm.
11. **Sentuhan Indonesia** — Ruang: galon, bakso cart, kopi
    keliling, bendera Merah Putih, AC split, LED. SukiOffice
    menambahkan yang paling murah dan paling masuk akal:
    tiang + bendera Merah Putih di Lobby, gerobak bakso,
    sepeda kopi keliling, dan dispenser + galon di Kitchen.
    Tidak menambahkan game room, dorm Lantai 2, atau ruko
    balcony — karena SukiOffice sudah punya 9 ruangan sendiri
    (Prayer Room, Restroom, dll.) yang harus dipertahankan
    sesuai perintah awal, bukan diganti gedung Ruang.

## Yang TIDAK disalin / TIDAK ada di SukiOffice

- Server Node + Hermes CLI (`hermes profile list`, `kanban`,
  `cron`, `sessions`, `insights`, `logs`) — SukiOffice tidak
  punya server, jadi tidak ada data crew/token/sesi nyata.
  Semua angka di HUD/Stats/Activity adalah data lokal
  (avatar demo + task lokal v3.1).
- Folder/memori agen (SOUL.md, MEMORY.md, USER.md), access
  code, profile lock PIN, dan redaksi secret server Ruang —
  tidak relevan untuk file statis demo ini, dan tidak boleh
  diklaim ada.
- React Three Fiber, Vite, dan struktur `src/` + `server/`
  Ruang — SukiOffice tetap satu file `index.html` tanpa build,
  supaya tetap bisa dikembangkan dari HP dan deploy Vercel
  statis tanpa kuota build yang boros.
- Game room (ping-pong, arcade, karambol) dan dorm Lantai 2 —
  sengaja tidak ditambahkan di v3.2; bila Suki mau, itu
  ruangan baru, bukan pengganti 9 ruangan yang sudah ada.

## Atribusi

Desain diadaptasi dari Ruang, MIT © yugienugraha.
Bila ada bagian SukiOffice yang kelak memakai kode Ruang
secara langsung, lisensi MIT-nya wajib dipertahankan di
file tersebut beserta copyright aslinya.
