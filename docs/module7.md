---
layout: default
title: "Module 7: LANDSAT TIMESERIES"
permalink: /BUDEE/module7.html
---

{% include _sidebar.md %}

# TimeSeries CHL dengan Citra Landsat-8

## 1. Pendahuluan
Timeseries data merupakan data yang terekam pada interval waktu tertentu. Dalam Google Earth Engine (GEE), data citra dapat dipilih berdasarkan rentang tanggal yang diinginkan untuk analisis perubahan spasial dan temporal.

Modul ini menjelaskan langkah-langkah penerapan algoritma estimasi klorofil-a (Chl-a) menggunakan **Landsat-8** dengan dua algoritma, yaitu **Arief 2006** dan **Hu et al. 2012**, serta menampilkan hasil dalam bentuk grafik timeseries.

![3_6](https://github.com/manessa-md/BUDEE/assets/108891611/d5a72016-90a1-4b55-a187-b3fcf34355d2)

---

## 2. Langkah-langkah Implementasi

### 2.1. Membuat Area Penelitian
Tentukan area penelitian dengan membuat poligon area penelitian dan beri kode untuk memudahkan pemrosesan selanjutnya.
```javascript
// Area Penelitian
var AOI = ee.FeatureCollection("projects/ee-budeetraining/assets/Banggai_area"); // Ganti sesuai aset Anda
```

### 2.2. Mengimpor Citra Landsat-8
Citra yang digunakan adalah **Landsat 8 Level 2, Collection 2, Tier 2**. Proses ini mencakup:
1. Pemfilteran berdasarkan tanggal.
2. Masking awan dan bayangan awan menggunakan band QA_PIXEL.
3. Pemotongan (clipping) citra berdasarkan area penelitian.

![3_1](https://github.com/manessa-md/BUDEE/assets/108891611/50b8ea11-a0e4-42b5-a933-8024b87e765b)

```javascript
// Fungsi masking awan
function maskL8sr(image) {
  var cloudShadowBitMask = (1 << 3);
  var cloudsBitMask = (1 << 5);
  var qa = image.select('QA_PIXEL');
  var mask = qa.bitwiseAnd(cloudShadowBitMask).eq(0)
                 .and(qa.bitwiseAnd(cloudsBitMask).eq(0));
  return image.updateMask(mask);
}

// Memuat dan memproses koleksi citra Landsat-8
var L8col = ee.ImageCollection("LANDSAT/LC08/C02/T2_L2")
              .filterDate('2021-01-01', '2022-09-30')
              .map(maskL8sr)
              .select('SR_B[1-7]')
              .map(function(image){return image.clip(AOI)});

Map.addLayer(L8col.median(), {bands: ['SR_B4', 'SR_B3', 'SR_B2'], min:0, max: 0.3}, "RGB Landsat", false);
```

### 2.3. Mengimpor Data Lapangan
```javascript
// Mengimpor titik data lapangan
var point = ee.FeatureCollection("projects/ee-budeetraining/assets/Survey_point");
print(point);

// Menampilkan titik survey di peta
Map.addLayer(point, {color:"red"}, "Titik Survey", false);
Map.centerObject(point);
```

![3_8](https://github.com/manessa-md/BUDEE/assets/108891611/24a7d901-b981-458e-87ef-80484f8bb553)

### 2.4. Implementasi Algoritma CHL
#### Algoritma Arief 2006

![3_9](https://github.com/manessa-md/BUDEE/assets/108891611/ee940a85-1b04-4f70-a5e2-0539e10f57f5)

```javascript
function CHLarief2006(img){
  var B2 = img.select("SR_B2");
  var B3 = img.select("SR_B3");
  var a = ee.Image(B2).subtract(ee.Image(B3));
  var b = ee.Image(B2).add(ee.Image(B3));
  var rrs = ee.Image(a).divide(ee.Image(b));
  var CHL = ((ee.Image(rrs.multiply(17.912)).subtract(0.3343)).rename('CHLarief2006'));
  return ee.Image(CHL.copyProperties(img, ['system:time_start']));
}

var CHLcol = L8col.map(CHLarief2006);
print('Arief 2006 image composite', CHLcol);
Map.addLayer(CHLcol.mean(), {min: 1, max: 3}, "Chl Arief", false);
```

### 2.5. Menampilkan Grafik TimeSeries CHL

![4_2](https://github.com/manessa-md/BUDEE/assets/108891611/1144d8a0-7dc0-4aae-9086-aa81303326bc)

### 2.6. Implementasi Algoritma CHL Hu et al. 2012

![3_11](https://github.com/manessa-md/BUDEE/assets/108891611/199a131e-5ce9-45cd-8ce2-65a07bff4af0)

---

## 3. Kesimpulan
Dari hasil grafik timeseries:
- **Algoritma Arief 2006** menunjukkan nilai Chl-a yang lebih stabil.
- **Algoritma Hu et al. 2012** memiliki fluktuasi yang lebih tinggi dalam nilai estimasi.

Modul ini memungkinkan analisis tren perubahan klorofil-a secara temporal menggunakan citra Landsat-8.

![4_3](https://github.com/manessa-md/BUDEE/assets/108891611/7fbe2279-97c7-4780-bbaa-882710f2d5a3)

---

## 4. Tugas Modifikasi Kode
Sebagai latihan tambahan, lakukan modifikasi berikut:
1. **Gunakan Landsat-9** untuk membandingkan hasil timeseries.
2. **Coba tambahkan area penelitian lain** untuk melihat perbedaan estimasi Chl-a.
3. **Eksplorasi metode estimasi lainnya**, seperti OC3 atau algoritma berbasis NDVI.

Silakan eksplorasi dan bandingkan hasilnya. Selamat mencoba!

