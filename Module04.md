# TimeSeries CHL Dengan Citra Landsat-8
 Timeseries data merupakan data yang terekam pada interval tertentu. Pada pengaplikasiannya di Google Earth Engine (GEE) Tanggal Citra yang digunakan dapat di atur.
 Berikut langkah timeseries CHL dengan Landsa-8:

### 1. Membuat Area Penelitian
Pada tahap ini dapat membuat area penelitian dengan mengklik tanda di kotak merah untuk menambahkan geometry

![3_6](https://github.com/manessa-md/BUDEE/assets/108891611/d5a72016-90a1-4b55-a187-b3fcf34355d2)

Area penelitian diberi kode agar memudahkan code script selanjutnya 
```
//Area Penelitian
var AOI = geometry;
```

### 2. Mengimport Citra Landsat 8
Pada tahap ini dapat dilakukan dengan mencari citra pada tabel pencarian dengan memasukan keyword nama citra satelit "Landsat 8".

![3_1](https://github.com/manessa-md/BUDEE/assets/108891611/50b8ea11-a0e4-42b5-a933-8024b87e765b)


### 3. Pemilihan Tanggal Pada Citra
Data Timeseries merupakan kumpulan data yang terekam pada interval tertentu. Pada kode script dapat di ubah pada bagian ".filterDate"

![4_1](https://github.com/manessa-md/BUDEE/assets/108891611/c65b681d-0ae3-49de-b481-a8f9b23c95f8)

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

//Map the Function
var L8col = ee.ImageCollection("LANDSAT/LC08/C02/T2_L2")
              .filterDate('2021-01-01', '2022-09-30')
              .map(maskL8sr)
              .select('SR_B[1-7]')
              .map(function(image){return image.clip(AOI)});
var L8com = L8col.median();
Map.addLayer(L8col, {bands: ['SR_B4', 'SR_B3', 'SR_B2'], min:0, max: 0.3}, "RGB Landsat", false);
```
