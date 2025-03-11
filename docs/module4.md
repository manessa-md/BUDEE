---
layout: default
title: "GCOM"
permalink: /BUDEE/module4.html
---

{% include _sidebar.md %}

# **Global Change Observation Mission (GCOM)**

## **Pendahuluan**
Global Change Observation Mission (GCOM) merupakan proyek observasi global jangka panjang yang dikembangkan oleh **Japan Aerospace Exploration Agency (JAXA)** untuk memantau perubahan lingkungan Bumi. Misi ini bertujuan untuk mengumpulkan data yang dapat digunakan dalam analisis perubahan iklim, sirkulasi lautan, dan ekosistem global.

GCOM terdiri dari dua satelit utama:
1. **GCOM-W (Water)**: Memfokuskan pada observasi perubahan sirkulasi perairan, termasuk salinitas dan suhu laut.
2. **GCOM-C (Climate)**: Mengamati perubahan iklim melalui **Second Generation Global Imager (SGLI)**, yang melakukan pengukuran atmosfer dan permukaan terkait siklus karbon dan radiasi, seperti awan, aerosol, warna laut, vegetasi, salju, dan es.

## **1. Membuat Area Penelitian**
Dalam **Google Earth Engine (GEE)**, area penelitian dapat ditentukan dengan beberapa cara:
- Menggunakan **tools geometry** secara manual.
- Mengimpor **shapefile** dari aset pengguna.
- Mengakses **shapefile** yang tersedia dalam **Google Earth Engine Assets**.

Gambar berikut menunjukkan bagaimana cara mengimpor **shapefile** area penelitian:

![image](https://github.com/manessa-md/BUDEE/assets/108908781/694e4287-0e0c-4036-935e-220e1127e2f3)
*Gambar 1. Mengimport asset shapefile area penelitian pada kolom Imports*

Jika shapefile mencakup beberapa area, kita dapat memilih lokasi spesifik menggunakan metode `.filter()`. Contoh berikut menunjukkan bagaimana kita hanya memilih area "Flores":
```javascript
var poi = zone.filter(ee.Filter.eq('Zona', 'Flores'));
```

## **2. Membuka Data Klorofil-a pada GCOM-C**
**Klorofil-a** adalah pigmen fotosintetik utama dalam fitoplankton yang dapat digunakan sebagai indikator produktivitas primer di lautan. **GCOM-C menyediakan data Level 3 (L3)** untuk konsentrasi **Klorofil-a**, yang dikalkulasi berdasarkan reflektansi multispektral.

### **Langkah-Langkah Membuka Data Klorofil-a di GEE**
#### **1. Mengakses Data dari Google Earth Engine**
Kode berikut digunakan untuk mengakses dataset **GCOM-C/SGLI L3 Chlorophyll-a Concentration (V1)**:
```javascript
var dataset = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V1')
                .filterDate('2020-01-01', '2020-02-01')
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D')); // Hanya daytime data
```
#### **2. Kalibrasi Data**
Dataset ini memerlukan kalibrasi dengan **slope coefficient** sebagai berikut:
```javascript
var image = dataset.mean().multiply(0.0016).log10();
```
#### **3. Visualisasi Data**
Setelah data dikalibrasi, kita bisa menampilkannya dengan menggunakan parameter visualisasi berikut:
```javascript
var vis = {
  bands: ['CHLA_AVE'],
  min: -2,
  max: 2,
  palette: [
    '3500a8','0800ba','003fd6',
    '00aca9','77f800','ff8800',
    'b30000','920000','880000'
  ]
};

Map.addLayer(image, vis, 'Chlorophyll-a concentration');
Map.setCenter(123.8547, -0.8266, 5);
```
Gambar berikut menunjukkan hasil visualisasi **Klorofil-a** oleh **GCOM-C**:
![image](https://github.com/manessa-md/BUDEE/assets/108908781/b89ad12a-c069-4e1a-9af7-15596df74e1e)
*Gambar 2. Hasil Konsentrasi Klorofil-a di Wilayah Indonesia oleh Satelit GCOM-C*

## **3. Time Series Global Change Observation Mission**
Analisis **time series** memungkinkan kita untuk mengamati tren perubahan dalam data klorofil-a selama periode tertentu.

### **1. Memilih Beberapa Citra GCOM-C**
Dataset **GCOM-C** menyediakan tiga versi produk klorofil-a:
- **V1**: 2018-01-01 hingga 2020-06-28
- **V2**: 2020-06-28 hingga 2021-11-28
- **V3**: 2021-11-29 hingga 2023-06-22

Kode berikut mengimpor ketiga dataset tersebut:
```javascript
var v1 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V1')
                .filterDate('2018-01-01', '2020-06-28')
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
                
var v2 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V2')
                .filterDate('2020-06-28', '2021-11-28')
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
                
var v3 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V3')
                .filterDate('2021-11-29', '2023-06-22')
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
```

### **2. Kalibrasi Data**
```javascript
function calibrate(image){
  return image.multiply(0.0016).copyProperties(image, ['system:time_start']);
}
```

### **3. Menggabungkan Data untuk Analisis Time Series**
```javascript
var gcom = v1.merge(v2).merge(v3).select(['CHLA_AVE']).map(calibrate);
```

### **4. Visualisasi Time Series**
```javascript
var vis = {
  bands: ['CHLA_AVE'],
  min: 0,
  max: 5,
  palette: [
    '3500a8','0800ba','003fd6',
    '00aca9','77f800','ff8800',
    'b30000','920000','880000'
  ]
};

Map.addLayer(gcom, vis, 'Chlorophyll-a concentration');
Map.setCenter(123.8547, -0.8266, 5);
```

Gambar berikut menunjukkan hasil analisis **time series** dari **GCOM-C**:
![image](https://github.com/manessa-md/BUDEE/assets/108908781/c8a10d17-46cb-46a3-89ec-fc566fd94deb)
*Gambar 3. Time Series Konsentrasi Klorofil-a di Wilayah Indonesia oleh GCOM-C*

---
## **Tugas Modifikasi Kode**
1. **Modifikasi area penelitian**: Pilih area lain selain "Bangai" untuk melihat perbedaan pola distribusi klorofil-a.
2. **Ubah rentang waktu**: Coba gunakan rentang waktu lebih panjang atau lebih pendek untuk melihat variasi konsentrasi klorofil-a.
3. **Eksperimen dengan palet warna**: Modifikasi palet warna untuk meningkatkan interpretasi visualisasi data.

Setelah melakukan modifikasi, analisis bagaimana perubahan-perubahan tersebut mempengaruhi hasil visualisasi dan kesimpulan yang dapat diambil.

