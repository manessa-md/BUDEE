# Menerapkan Algoritma Chlorophyll pada citra  Landsat 8
---
Pengampilakasin algoritma Chlorophyl dapat dilakukan dengan langkah-langkah sebagai berikut:
1. Menentukan Area Penelitian
2. Mengimport citra yang akan digunakan
3. Mengimplementasi Algoritma CHL pada citra
4. Melakukan uji akurasi algoritma dengan data lapangan

## 1. Menentukan Area Penelitian
Tentukan Area yang akn dianalisis dengan menambahkan kotak area dengan cara memilih kotak warna merah :

![3_6](https://github.com/manessa-md/BUDEE/assets/108891611/26a62f5c-4e12-4cbf-9629-64e2217afc24)

Kemudian beri kode geometry yang dibuat untuk memudahkan script code berikutnya :
```
//Area Penelitian
var AOI = geometry;
```

## 2. Mengimport Citra Yang Akan Digunakan
Citra yang digunakan pada module ini adalah Citra Landsat 8.
Pengimportan citra sentinel dapat dikukan dengan mencari pada tabel pencarian dengan memasukan _keyword_ nama citra satelit "Landsat 8".

Pada module ini menggunakan Citra Landsat 8 Level 2, Collection 2, Tier 2

![3_1](https://github.com/manessa-md/BUDEE/assets/108891611/b07d0e81-7aab-4c3e-ba83-719f053ffc0f)

```
//Landsat-8
function maskL8sr(image) {
  // Bits 3 and 5 are cloud shadow and cloud, respectively
  var cloudShadowBitMask = (1 << 3); // 1000 in base 2
  var cloudsBitMask = (1 << 5); // 100000 in base 2

  // Get the pixel QA band
  var qa = image.select('QA_PIXEL');

  // Both flags should be set to zero, indicating clear conditions
  var mask = qa
    .bitwiseAnd(cloudShadowBitMask).eq(0)
    .and(qa.bitwiseAnd(cloudsBitMask).eq(0));

  // Mask image with clouds and shadows
  return image.updateMask(mask);
}

var clip_rmnp = function(image) {
  return image.clip(rmnp_boundary);
};

//Data Landsat-8
var L8col = ee.ImageCollection("LANDSAT/LC08/C02/T2_L2")
              .filterDate('2022-07-01', '2022-09-30')
              .map(maskL8sr)
              .select('SR_B[1-7]')
              .map(function(image){return image.rename(['B1', 'B2', 'B3', 'B4','B5','B6','B7']).clip(AOI)});
var L8com = L8col.median();
Map.addLayer(L8col, {bands: ['B4', 'B3', 'B2'], min:0, max: 0.3}, "RGB Landsat", false);
```
![3_7](https://github.com/manessa-md/BUDEE/assets/108891611/918055e4-4a47-4d13-80ea-7e1ab0738d36)


## 3. Implementasi Algoritma CHL


