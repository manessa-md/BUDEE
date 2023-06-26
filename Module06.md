# MODIS
---


Pada modul ini akan membahas:
- Memilih Citra Modis
- Memahami Informasi Citra Modis
- Mengimport Citra Modis
- Memberi Visualisasi pada Citra
- Menampilkan Citra Modis

## 1. Memilih Citra Modis
Pada module ini citra modis yang digunakan "Ocean Color SMI: Standard Mapped Image MODIS Terra Data"

Pemilihan data citra dapat dilakukan dengan cara memasukan keyword nama citra satelit "Modis Terra OceanColour"

![6_1](https://github.com/manessa-md/BUDEE/assets/108891611/ed787fb5-7167-4f28-84a4-0a234188b82b)

## 2. Memahami Informasi Citra Modis
Pada data setiap citra, memiliki informasi data yang berbeda. Untuk mengetahui tentang Citra Modis yang digunakan, dapat dengan mengkil pada tombol kotak merah:

![6_2](https://github.com/manessa-md/BUDEE/assets/108891611/591e4ff1-8e92-49b0-af6b-bdd80d479d2d)


## 3.Mengimport Citra Modis
Pengimportan dapat dilakukan dengan menambahkan code script :

```
var dataset = ee.ImageCollection('NASA/OCEANDATA/MODIS-Terra/L3SMI')
```

## 4. Memberi Visualisasi pada Citra
Pada citra Modis, terdapat band yang langsung menampilkan data Chlorofil. Band tersebut di pilih dan diberi visualisasi

```
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

## Menampilkan Citra Modis
Setelah dilakukan pemilihan band dan visualisai. Data dapat ditampilkan dengan code script :

```
Map.setCenter(123.8547, -0.8266, 4);
Map.addLayer(chlor, Vis, 'Chlorophyll a concentration mg/m^3');
```

![6_3](https://github.com/manessa-md/BUDEE/assets/108891611/6332da7f-76e7-49cb-8b9b-534e43fff2b4)


