# SukiOffice v3 Live — Cara Export Model Blender ke GLTF + Animasi

> Status jujur: v3.0 yang live di Vercel memakai avatar PROSEDURAL
> (kotak/kapsul + animasi kode), BUKAN file GLTF manusia asli.
> Dokumen ini adalah jalur upgrade ke GLTF asli, langkah demi langkah,
> supaya tidak ada klaim palsu bahwa model Blender sudah ada.

## 1. Struktur folder models/ (yang diharapkan loader nanti)

```
models/
  README.md                 <- file ini (penanda, bukan model)
  avatars/
    employee_01.glb         <- BELUM ADA, isi bila sudah export
    employee_02.glb
    ...
  rooms/
    SO_Lobby.glb            <- BELUM ADA
    SO_Coworking.glb
    SO_CEO.glb
    SO_Meeting1.glb
    SO_Meeting2.glb
    SO_Huddle.glb
    SO_Kitchen.glb
    SO_Prayer.glb
    SO_Restroom.glb
  props/
    desk.glb
    chair_ergonomic.glb
    laptop.glb
```

Jangan commit file `.blend` besar ke repo GitHub utama bila >100MB —
pakai Git LFS atau simpan di Drive, lalu export `.glb` kecil (<5MB per file)
ke repo ini.

## 2. Export dari Blender (langkah persis)

1. Buka model di Blender 4.x. Pastikan:
   - Skala: 1 unit Blender = 1 meter. Avatar tinggi ~1.7 unit.
   - Origin di lantai (kaki di Z=0), menghadap +Y (Blender) / +Z (glTF).
   - Semua mesh sudah Apply Transform (Ctrl+A → All Transforms).
   - Material PBR: Base Color, Metallic, Roughness, Normal — jangan
     pakai material Eevee-only yang tidak ikut export.
2. Animasi: buat Action terpisah per state, beri nama persis:
   `Idle`, `Typing`, `Walking`, `Meeting`, `CoffeeSip`, `Prayer`,
   `Stretch`. Satu rig, satu armature per avatar.
   - Jangan bake ke 60fps penuh bila file membengkak — 30fps cukup.
   - Pastikan NLA Track aktif untuk semua action yang mau ikut.
3. Export: File → Export → glTF 2.0 (.glb)
   - Format: glTF Binary (.glb)
   - Centang: Selected Objects (bila export per objek), Apply Modifiers,
     +Y Up, Animations, Skinning, Shape Keys (bila ada).
   - Compression: centang Draco HANYA bila decoder sudah disiapkan di
     loader (lihat catatan Draco di bawah). Untuk v3.1, disarankan
     TANPA Draco dulu supaya loader v3.0 yang sederhana tidak gagal.
4. Uji di viewer sebelum push: https://gltf-viewer.donmccurdy.com
   — cek animasi jalan, skala benar, material tidak hitam/pink.

## 3. Mengganti avatar prosedural dengan GLTF (titik kode)

Di `index.html`, fungsi `buildAvatar(p)` adalah satu-satunya tempat
avatar dibuat. Ganti isinya dengan:

```js
// Pseudocode, bukan kode yang sudah dites di v3.0:
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
const loader = new GLTFLoader();
loader.load('models/avatars/' + p.id + '.glb', (gltf) => {
  const model = gltf.scene;
  // simpan mixer: new THREE.AnimationMixer(model)
  // petakan clip: Idle/Typing/Walking/dll ke state di updateEmployees()
  // fallback: bila load gagal, tetap pakai buildAvatar prosedural lama
});
```

Aturan wajib dari insiden v2.2 (lightShaft hilang):
- Jangan hapus fungsi lama sebelum fungsi baru teruji di HP.
- Loader GLTF harus punya fallback prosedural — bila file 404,
  avatar tetap muncul sebagai kotak, bukan blank.
- Cek semua nama action: bila `Typing` tidak ditemukan di clip,
  jangan crash — pakai `Idle` sebagai fallback.

## 4. Cannon.js — bila benar-benar butuh fisika

v3.0 sengaja pakai AABB sederhana (batas ruangan + jarak meja) karena
FPS HP di v2.3 adalah 60 dan Cannon penuh akan menambah beban CPU
di HP 3–6GB. Bila nanti butuh rigid-body sungguhan:

- Pakai `cannon-es` via jsdelivr (bukan `cannon.js` lama yang sudah
  tidak dirawat): `https://cdn.jsdelivr.net/npm/cannon-es@0.20.0/dist/cannon-es.js`
- Buat satu `CANNON.World` dengan gravitasi (0, -9.82, 0), satu
  ground plane, dan satu body kapsul per avatar yang sedang jalan.
- Sinkronisasi: setiap frame, salin `body.position` ke `group.position`,
  bukan sebaliknya. Jangan jalankan `world.step` lebih dari 60Hz di HP.
- Uji dulu di desktop: bila FPS turun di bawah 30 di A76, kembalikan
  ke AABB — fisika bukan fitur yang boleh mengorbankan kantor hidup.

## 5. Checklist sebelum push GLTF ke production

- [ ] Semua `.glb` lolos viewer + animasi jalan
- [ ] Total ukuran models/ < 20MB (target HP)
- [ ] Loader punya fallback prosedural bila 404
- [ ] `node --check` pada script module lolos
- [ ] Tes di HP: FPS, klik avatar, teleport MiniMap, reload (state tersimpan)
- [ ] Tidak ada nama karyawan nyata yang diklaim tanpa izin orangnya —
      roster demo diganti hanya setelah tim nyata menyetujui nama/rolenya
