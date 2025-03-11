# TimeSeries CHL dengan Citra Landsat-8

## 1. Pendahuluan
Timeseries data merupakan data yang terekam pada interval waktu tertentu. Dalam Google Earth Engine (GEE), data citra dapat dipilih berdasarkan rentang tanggal yang diinginkan untuk analisis perubahan spasial dan temporal.

Modul ini menjelaskan langkah-langkah penerapan algoritma estimasi klorofil-a (Chl-a) menggunakan **Landsat-8** dengan dua algoritma, yaitu **Arief 2006** dan **Hu et al. 2012**, serta menampilkan hasil dalam bentuk grafik timeseries.

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

### 2.4. Implementasi Algoritma CHL
#### Algoritma Arief 2006
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
```javascript
var chart = ui.Chart.image.series({
          imageCollection: CHLcol,
          region: AOI,
          reducer: ee.Reducer.mean(),
          scale: 100,
          xProperty: 'system:time_start'
        })
        .setSeriesNames(['CHLArief2006'])
        .setOptions({
          title: 'Chlorofil-a Timeseries',
          hAxis: {title: 'Date', titleTextStyle: {italic: false, bold: true}},
          vAxis: {title: 'Chl mg-3', titleTextStyle: {italic: false, bold: true}},
          lineWidth:5,
          colors: ['e37d05'],
          curveType: 'function'
        });
print(chart);
```

### 2.6. Implementasi Algoritma CHL Hu et al. 2012
```javascript
function CI(image) {
    var result = image.expression(
        'Green - ( Blue + (lambdaGreen - lambdaBlue) / (lambdaRed - lambdaBlue) * (Red - Blue) )',
        {
            'Red': image.select('SR_B4'),
            'lambdaRed': 670,
            'Green': image.select('SR_B3'),
            'lambdaGreen': 555,
            'Blue': image.select('SR_B2'),
            'lambdaBlue': 443
        });
    var CIp = result.multiply(230.47).subtract(0.4287);
    var CHL = ee.Image(10).pow(CIp).rename('CHLhu');
    return ee.Image(CHL.copyProperties(image, ['system:time_start']));
}

var CHLcol_Hu = L8col.map(CI);
print('Hu et al. image composite', CHLcol_Hu);
Map.addLayer(CHLcol_Hu.mean(), {min: 1, max: 3}, "Chl Hu", false);
```

### 2.7. Grafik Timeseries Algoritma Hu et al. 2012
```javascript
var chart_Hu = ui.Chart.image.series({
          imageCollection: CHLcol_Hu,
          region: AOI,
          reducer: ee.Reducer.mean(),
          scale: 100,
          xProperty: 'system:time_start'
        })
        .setSeriesNames(['CHLhu'])
        .setOptions({
          title: 'Chlorofil-a Timeseries - Hu et al. 2012',
          hAxis: {title: 'Date', titleTextStyle: {italic: false, bold: true}},
          vAxis: {title: 'Chl mg-3', titleTextStyle: {italic: false, bold: true}},
          lineWidth:5,
          colors: ['1d6b99'],
          curveType: 'function'
        });
print(chart_Hu);
```

---

## 3. Kesimpulan
Dari hasil grafik timeseries:
- **Algoritma Arief 2006** menunjukkan nilai Chl-a yang lebih stabil.
- **Algoritma Hu et al. 2012** memiliki fluktuasi yang lebih tinggi dalam nilai estimasi.

Modul ini memungkinkan analisis tren perubahan klorofil-a secara temporal menggunakan citra Landsat-8.

---

## 4. Tugas Modifikasi Kode
Sebagai latihan tambahan, lakukan modifikasi berikut:
1. **Gunakan Landsat-9** untuk membandingkan hasil timeseries.
2. **Coba tambahkan area penelitian lain** untuk melihat perbedaan estimasi Chl-a.
3. **Eksplorasi metode estimasi lainnya**, seperti Ocean Color 3 (OC3).

Silakan eksplorasi dan bandingkan hasilnya. Selamat mencoba!

