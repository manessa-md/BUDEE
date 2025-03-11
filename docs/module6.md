---
layout: default
title: "Module 6: LANDSAT"
---

{% include _sidebar.md %}

# **Menerapkan Algoritma Chlorophyll pada Citra Landsat 8**

## **Pendahuluan**
Landsat-8 merupakan satelit observasi bumi milik Amerika Serikat yang diluncurkan pada 11 Februari 2013. Satelit ini memiliki dua sensor utama, yaitu:
1. **Operational Land Imager (OLI)** - Menghasilkan citra dengan resolusi spasial 30 meter (visible, NIR, SWIR), 100 meter (thermal), dan 15 meter (pankromatik).
2. **Thermal Infrared Sensor (TIRS)** - Digunakan untuk pemetaan suhu permukaan tanah dan laut.

Pada modul ini, kita akan menerapkan algoritma estimasi klorofil (Chlorophyll, CHL) pada citra Landsat-8. Langkah-langkah yang akan dilakukan meliputi:
1. Menentukan Area Penelitian
2. Mengimpor Citra Landsat-8
3. Mengimpor Data Lapangan ke Google Earth Engine (GEE)
4. Mengimplementasikan Algoritma CHL
5. Melakukan Uji Akurasi Algoritma

---

## **1. Menentukan Area Penelitian**

Langkah pertama adalah menentukan area penelitian dengan menambahkan kotak area (Area of Interest, AOI). Untuk mempermudah pemrosesan data, buatlah objek **geometry** yang merepresentasikan AOI tersebut.

**Contoh kode:**
```javascript
// Menentukan Area Penelitian
var AOI = ee.FeatureCollection("projects/ee-budeetraining/assets/Banggai_area"); // Sesuaikan dengan data Anda
```

![Gambar Area Penelitian](https://github.com/manessa-md/BUDEE/assets/108891611/26a62f5c-4e12-4cbf-9629-64e2217afc24)

---

## **2. Mengimpor Citra Landsat-8**
Citra Landsat-8 dapat diakses melalui Google Earth Engine (GEE). Modul ini menggunakan dataset **Landsat 8 Level 2, Collection 2, Tier 2**.

### **Langkah-langkah:**
- Mencari citra Landsat-8 melalui tabel pencarian di GEE.
- Menggunakan fungsi *masking* untuk menghilangkan awan dan bayangan awan.
- Menyeleksi band yang diperlukan dan melakukan *clipping* berdasarkan AOI.

**Kode untuk mengimpor citra Landsat-8:**
```javascript
// Fungsi untuk menghilangkan awan
function maskL8sr(image) {
  var cloudShadowBitMask = (1 << 3); // 1000 dalam biner
  var cloudsBitMask = (1 << 5); // 100000 dalam biner
  var qa = image.select('QA_PIXEL');
  var mask = qa.bitwiseAnd(cloudShadowBitMask).eq(0)
               .and(qa.bitwiseAnd(cloudsBitMask).eq(0));
  return image.updateMask(mask);
}

// Mengimpor citra Landsat-8
var L8col = ee.ImageCollection("LANDSAT/LC08/C02/T2_L2")
              .filterDate('2022-07-01', '2022-09-30')
              .map(maskL8sr)
              .select('SR_B[1-7]')
              .map(function(image) {
                  return image.rename(['B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7']).clip(AOI);
              });
var L8com = L8col.median();
Map.addLayer(L8col, {bands: ['B4', 'B3', 'B2'], min: 0, max: 0.3}, "RGB Landsat", false);
```

![Gambar Citra Landsat](https://github.com/manessa-md/BUDEE/assets/108891611/b07d0e81-7aab-4c3e-ba83-719f053ffc0f)

---

## **3. Mengimpor Data Lapangan**
Data lapangan digunakan untuk validasi hasil estimasi klorofil. Data ini dapat diunggah sebagai **FeatureCollection** pada GEE.

**Kode untuk mengimpor data lapangan:**
```javascript
// Mengimpor data survey lapangan
var point = ee.FeatureCollection("projects/ee-budeetraining/assets/Survey_point"); // Sesuaikan dengan aset Anda
print(point);

// Menampilkan titik survey di peta
Map.addLayer(point, {color: "red"}, "Titik Survey", false);
Map.centerObject(point);
```

![Gambar Data Lapangan](https://github.com/manessa-md/BUDEE/assets/108891611/3fce73d3-6862-45de-9455-4bae17c3ce03)

---

## **4. Mengimplementasikan Algoritma CHL**

### **A. Algoritma Arief (2006)**

Algoritma ini menggunakan rasio band untuk menghitung klorofil:
```javascript
function CHLarief2006(img){
  var B2 = img.select("B2");
  var B3 = img.select("B3");
  var rrs = B2.subtract(B3).divide(B2.add(B3));
  var CHL = rrs.multiply(17.912).subtract(0.3343).rename('CHLarief2006');
  return CHL.copyProperties(img, ['system:time_start']);
}
```

### **B. Algoritma Hu (2012)**

Algoritma ini menggunakan indeks warna:
```javascript
function CI(image) {
    var result = image.expression(
        'Green - ( Blue + (lambdaGreen - lambdaBlue) / (lambdaRed - lambdaBlue) * (Red - Blue) )',
        {
            'Red': image.select('B4'),
            'lambdaRed': 670,
            'Green': image.select('B3'),
            'lambdaGreen': 555,
            'Blue': image.select('B2'),
            'lambdaBlue': 443
        });
    
    var CIp = result.multiply(230.47).subtract(0.4287);
    var CHL = ee.Image(10).pow(CIp).rename('CHLhu');
    return CHL.copyProperties(image, ['system:time_start']);
}
```

---

## **5. Uji Akurasi dengan Data Lapangan**

Uji akurasi dilakukan dengan membandingkan nilai klorofil hasil estimasi dengan data lapangan menggunakan **Scatter Plot** dan menghitung **RMSE**.

```javascript
var pointExtract = CHLcom.reduceRegions(point, ee.Reducer.first(), 30);
var pointE = pointExtract.filter(ee.Filter.neq('first', null));

var chart = ui.Chart.feature.byFeature(pointE, 'Chl', ['first'])
  .setChartType('ScatterChart')
  .setOptions({
    titleX: 'Measured Chl',
    titleY: 'Predicted Chl',
    pointSize: 3,
    trendlines: {0: {showR2: true, visibleInLegend: true}}
  });
print(chart);
```


![Gambar Uji Akurasi](https://github.com/manessa-md/BUDEE/assets/108891611/df8b540e-5041-43dd-af74-af3c5c0cd45c)

**Kesimpulan:** Algoritma Arief (2006) menunjukkan hasil lebih baik dibandingkan Hu (2012) berdasarkan nilai R² dan RMSE.

### **Tugas Modifikasi Kode**
1. Modifikasi algoritma CHL dengan menggunakan kombinasi band lain (misalnya B5 dan B3).
2. Bandingkan hasilnya dengan algoritma yang sudah ada.
3. Tambahkan metode validasi lain seperti Mean Absolute Error (MAE) atau Coefficient of Determination (R²).

