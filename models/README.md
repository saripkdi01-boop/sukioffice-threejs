# Folder Model 3D SukiOffice

Taruh berkas GLB karakter hasil Blender di folder ini dengan nama:

```
sukioffice-<id>.glb
```

`<id>` adalah id agen di konfigurasi kantor, contoh: `sukioffice-desainer.glb` (Salsa),
`sukioffice-dirut.glb` (Iqraa), `sukioffice-komisaris.glb` (Widia), `sukioffice-direktur.glb` (Dirga),
`sukioffice-humas.glb` (Gibran), `sukioffice-galih.glb` (Galih), dan seterusnya.

Begitu berkas ada di sini dan situs ter-deploy, kantor otomatis memakai model GLB itu
menggantikan avatar prosedural agen tersebut — tidak perlu mengubah kode.
Agen yang belum punya berkas tetap tampil prosedural.

Spesifikasi berkas (dari master prompt karakter):

- Format GLB (glTF binary), maksimal ±30.000 segitiga per karakter.
- Tekstur PBR 1024–2048, sudah ter-bake.
- Rig humanoid kompatibel Mixamo, skala 1 unit = 1 meter, kaki menapak di y=0, menghadap +Z.
- Klip animasi bernama persis: idle, typing, walk, talk, present, phone, think, wave, celebrate, coffee.
  Kantor memutar klip `idle` (atau klip pertama) sebagai animasi dasar.
- Tinggi model bebas — kantor menormalkan tinggi tampil menjadi ±1,75 m.
