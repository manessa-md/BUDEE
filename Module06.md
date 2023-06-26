# TimeSeries Dengan Citra Modis
---
MODIS (Moderate Resolution Imaging Spectroradiometer) merupakan instrumen yang beroperasi pada satelit Terra. Satelit ini memiliki lebar sapuan sebesar 2330 km dan memotret seluruh permukaan bumi dalam satu atau dua hari. Data Timeseries merupakan kumpulan hasil data pada interval waktu tertentu. Data Timeseries dapat dilakukan dengan menggunakan citra Modis. Dengan langkah-langkah sebagai berikut:


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

## 5. Menampilkan Citra Modis
Setelah dilakukan pemilihan band dan visualisai. Data dapat ditampilkan dengan code script :

```
Map.setCenter(123.8547, -0.8266, 4);
Map.addLayer(chlor, Vis, 'Chlorophyll a concentration mg/m^3');
```

![6_3](https://github.com/manessa-md/BUDEE/assets/108891611/63c05753-d4e5-49ce-b5ff-f6a9c3beeec4)


## 6. Membuat Grafik TimeSeries
```
// part 02 membuat grafik timeseries 
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
                title: 'Chlorofil-a',
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

Namun grafik akan error karena terlalu banyak element yang di running

![6_4](https://github.com/manessa-md/BUDEE/assets/108891611/c33455fb-7e55-45a6-b18d-0cd5854cf7f0)

Sehingga Perlu Adanya Filtering Data

## 7. FIltering Data
### Hal yang harus dilakukan telebih dahulu yaitu membuat area penelitian

![3_6](https://github.com/manessa-md/BUDEE/assets/108891611/f621c364-bd02-4b1e-879e-6ab8b730e2e9)


Hal tersebut akan memudahkan dalam mengclip data citra

### Kemudian melakukan filter tanggal
Filter tanggal dilakukan untuk memilih rentang tanggal yang akan digunakan dalam analisis

```
var dataset = ee.ImageCollection('NASA/OCEANDATA/MODIS-Terra/L3SMI')
                .filterDate('2018-01-01', '2023-06-30')
                
                
function clips(image){return image.clip(aoi).copyProperties(image, ['system:time_start'])}

var chlor = dataset.select(['chlor_a']).map(clips);

var Vis = {
  min: 0.0,
  max: 4,
  palette: [
    '3500a8','0800ba','003fd6',
    '00aca9','77f800','ff8800',
    'b30000','920000','880000'
  ]
};

Map.setCenter(123.8547, -0.8266, 10);
Map.addLayer(chlor, Vis, 'Chlorophyll a concentration mg/m^3');
```

![6_5](https://github.com/manessa-md/BUDEE/assets/108891611/623f07f7-80bf-4bc6-9d72-f35acc50433a)

## 8. Buat Grafik Timeseries dari data Citra yang telah di filter
Data yang telah difilter akan tampil pada grafik.

```
// part 02 membuat grafik timeseries 
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
                title: 'Chlorofil-a',
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

![6_6](https://github.com/manessa-md/BUDEE/assets/108891611/bc63e3cc-aa9f-4804-9017-268deaea102e)







