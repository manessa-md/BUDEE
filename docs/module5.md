---
layout: default
title: "Module 5: MODIS"
permalink: /BUDEE/module5.html
---

{% include _sidebar.md %}

# Analisis Time Series dengan Citra MODIS

## 1. Pendahuluan
MODIS (Moderate Resolution Imaging Spectroradiometer) adalah instrumen yang beroperasi pada satelit Terra dengan lebar sapuan sebesar 2330 km. MODIS mampu menangkap citra seluruh permukaan bumi dalam satu atau dua hari, menjadikannya sangat berguna untuk pemantauan lingkungan berbasis time series.

Analisis time series dilakukan untuk mengamati perubahan parameter lingkungan dalam rentang waktu tertentu. Dalam modul ini, kita akan menggunakan citra MODIS untuk memantau konsentrasi Chlorophyll-a (Chlor-a), yang menjadi indikator utama produktivitas fitoplankton di lautan.

## 2. Pemilihan Citra MODIS
Citra yang digunakan dalam modul ini adalah "Ocean Color SMI: Standard Mapped Image MODIS Terra Data". Pemilihan data dilakukan dengan memasukkan kata kunci "Modis Terra OceanColour" dalam pencarian data citra satelit.

![Pemilihan Data MODIS](https://github.com/manessa-md/BUDEE/assets/108891611/ed787fb5-7167-4f28-84a4-0a234188b82b)

## 3. Memahami Informasi Citra MODIS
Setiap dataset memiliki informasi metadata yang berbeda, termasuk resolusi spasial, rentang waktu pengambilan, dan band yang tersedia. Untuk mengetahui informasi citra yang digunakan, klik tombol informasi pada dataset yang dipilih.

![Informasi Data MODIS](https://github.com/manessa-md/BUDEE/assets/108891611/591e4ff1-8e92-49b0-af6b-bdd80d479d2d)

## 4. Mengimpor Citra MODIS
Data MODIS dapat diimpor ke Google Earth Engine dengan menambahkan kode berikut:
```javascript
var dataset = ee.ImageCollection('NASA/OCEANDATA/MODIS-Terra/L3SMI');
```

## 5. Memberikan Visualisasi pada Citra MODIS
MODIS menyediakan beberapa band, salah satunya adalah 'chlor_a' yang menunjukkan konsentrasi Chlorophyll-a. Untuk menampilkan data ini, kita dapat menggunakan kode berikut:
```javascript
var chlor = dataset.select(['chlor_a']);
var Vis = {
  min: 0.0,
  max: 4,
  palette: [
    '3500a8','0800ba','003fd6',
    '00aca9','77f800','ff8800',
    'b30000','920000','880000'
  ]
};
```

## 6. Menampilkan Citra MODIS
Setelah band dipilih dan diberi visualisasi, citra dapat ditampilkan menggunakan skrip berikut:
```javascript
Map.setCenter(123.8547, -0.8266, 4);
Map.addLayer(chlor, Vis, 'Chlorophyll a concentration mg/m^3');
```

![Visualisasi Citra MODIS](https://github.com/manessa-md/BUDEE/assets/108891611/63c05753-d4e5-49ce-b5ff-f6a9c3beeec4)

## 7. Membuat Grafik Time Series
Untuk melihat perubahan konsentrasi Chlorophyll-a dalam periode waktu tertentu, kita dapat membuat grafik time series menggunakan kode berikut:
```javascript
var chart = ui.Chart.image
              .series({
                imageCollection:chlor,
                region: point,
                reducer: ee.Reducer.mean(),
                scale: 100,
                xProperty: 'system:time_start'
        })
              .setSeriesNames(['chlor_a'])
              .setOptions({
                title: 'Chlorophyll-a',
                hAxis: {title: 'Date', titleTextStyle: {italic: false, bold: true}},
                vAxis: {
                  title: 'Chl mg-3',
                  titleTextStyle: {italic: false, bold: true}
          },
                lineWidth:5,
                colors: ['e37d05', '1d6b99'],
                curveType: 'function'
              });
print(chart);
```
Namun, grafik ini dapat mengalami error jika jumlah elemen yang diproses terlalu banyak.

![Error Grafik Time Series](https://github.com/manessa-md/BUDEE/assets/108891611/c33455fb-7e55-45a6-b18d-0cd5854cf7f0)

## 8. Filtering Data
Agar data lebih mudah dianalisis, perlu dilakukan pemangkasan berdasarkan wilayah dan rentang waktu tertentu.

### a. Menentukan Area Penelitian
Menentukan area penelitian akan mempermudah proses kliping citra agar analisis lebih fokus.

![Menentukan Area Penelitian](https://github.com/manessa-md/BUDEE/assets/108891611/f621c364-bd02-4b1e-879e-6ab8b730e2e9)

### b. Memfilter Tanggal
Untuk memilih rentang waktu yang akan dianalisis, gunakan kode berikut:
```javascript
var dataset = ee.ImageCollection('NASA/OCEANDATA/MODIS-Terra/L3SMI')
                .filterDate('2018-01-01', '2023-06-30');
                
function clips(image){return image.clip(aoi).copyProperties(image, ['system:time_start'])}

var chlor = dataset.select(['chlor_a']).map(clips);

Map.setCenter(123.8547, -0.8266, 10);
Map.addLayer(chlor, Vis, 'Chlorophyll a concentration mg/m^3');
```

![Hasil Filtering Data](https://github.com/manessa-md/BUDEE/assets/108891611/623f07f7-80bf-4bc6-9d72-f35acc50433a)

## 9. Membuat Grafik Time Series dari Data yang Difilter
Setelah data difilter berdasarkan wilayah dan tanggal, kita dapat membuat grafik time series dengan kode berikut:
```javascript
var chart = ui.Chart.image
              .series({
                imageCollection:chlor,
                region: point,
                reducer: ee.Reducer.mean(),
                scale: 100,
                xProperty: 'system:time_start'
        })
              .setSeriesNames(['chlor_a'])
              .setOptions({
                title: 'Chlorophyll-a',
                hAxis: {title: 'Date', titleTextStyle: {italic: false, bold: true}},
                vAxis: {
                  title: 'Chl mg-3',
                  titleTextStyle: {italic: false, bold: true}
          },
                lineWidth:5,
                colors: ['e37d05', '1d6b99'],
                curveType: 'function'
              });
print(chart);                
```

![Grafik Time Series](https://github.com/manessa-md/BUDEE/assets/108891611/bc63e3cc-aa9f-4804-9017-268deaea102e)

## 10. Tugas
1. Jelaskan secara singkat fungsi dari setiap bagian kode di atas.
2. Ubah rentang waktu analisis menjadi antara tahun 2015 hingga 2020.
3. Tambahkan filter spasial untuk hanya menampilkan data di area perairan tertentu.
4. Bandingkan hasil grafik time series dengan rentang waktu yang berbeda dan buat interpretasi perubahan konsentrasi Chlorophyll-a dari hasil yang didapat.

**Selamat Mengerjakan!**

