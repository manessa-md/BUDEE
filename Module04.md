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
Data Timeseries merupakan kumpulan data yang terekam pada interval tertentu. Pada kode script dapat di ubah pada bagian ".filterDate".
Interval tanggal dapat disesuaikan dengan kebutuhan analisis

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
## 4. Mengimport data lapangan
Dilakukan import data lapangan 
```
//Data
var point = ee.FeatureCollection("projects/ee-amalias20l/assets/CTD2");
print(point);

//tampilkan di peta
Map.addLayer(point,{color:"red"}, "Titik Survey", false);
Map.centerObject(point);
```

![3_8](https://github.com/manessa-md/BUDEE/assets/108891611/24a7d901-b981-458e-87ef-80484f8bb553)

## 5. Implementasi Algoritma
Algoritma CHL diperoelh dari penelitian terdahulu. Berikut Algoritma Arief2006

![3_9](https://github.com/manessa-md/BUDEE/assets/108891611/ee940a85-1b04-4f70-a5e2-0539e10f57f5)


```
//Implementasi Algoritma
//1. Fungsi
function CHLarief2006(img){
  var B2 = img.select("SR_B2");
  var B3 = img.select("SR_B3");
  var a = ee.Image(B2).subtract(ee.Image(B3));
  var b = ee.Image(B2).add(ee.Image(B3));
  var rrs = ee.Image(a).divide(ee.Image(b));
  var CHL = ((ee.Image(rrs.multiply(17.912)).subtract(0.3343)).rename('CHLarief2006'));
  return ee.Image(CHL.copyProperties(img, ['system:time_start']));
}

//2. Implementasi Alg Arief
var CHLcol = L8col.map(CHLarief2006);
print('Arief 2006 image composite', CHLcol);

//3. Tampilan Peta
Map.addLayer(CHLcol.mean(), {min: 1, max: 3}, "Chl", false);

```
## 6. Menampilkan Grafik TimeSeries
Pada data grafik adan melihat rata-rata chl pada interval waktu yang di pilih 

```
//4. Grafik 
var chart = 
    ui.Chart.image
        .series({
          imageCollection:CHLcol,
          region: AOI,
          reducer: ee.Reducer.mean(),
          scale: 100,
          xProperty: 'system:time_start'
        })
        .setSeriesNames(['CHLArief2006'])
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

Grafik Timeseries Data CHL dengan Algoritma Arief2006

![4_2](https://github.com/manessa-md/BUDEE/assets/108891611/1144d8a0-7dc0-4aae-9086-aa81303326bc)


## 7. Dilakukan juga implementasi algoritma chl dari penelitian lain Hu 2012

```
// CI Algorithm, (Hu et al. 2012)
function CI(image) {
    /*
      Calculates the Color Index (CI) by difference in reflectance of bands from input image
      Formulation:
        result = Green - [ Blue + (lambdaGreen - lambdaBlue) / (lambdaRed - lambdaBlue) * (Red - Blue) ]
      Where :
        Red, Green, Blue are Reflectances in the respective bands of sat image
        lambdaRed, lambdaGreen, lambdaBlue
          are instrument-specific wavelengths closest to 670, 555, 443 respectively of bands
    */
    var result = image.expression(
        'Green - ( Blue + (lambdaGreen - lambdaBlue) / (lambdaRed - lambdaBlue) * (Red - Blue) )',
        {
            'Red': image.select('SR_B4'), // *Designations for SR datasets>>
            'lambdaRed': 670,
            'Green': image.select('SR_B3'),
            'lambdaGreen': 555,
            'Blue': image.select('SR_B2'),
            'lambdaBlue': 443
        });
      var CHL = result.rename('CHLhu')  
    return ee.Image(CHL.copyProperties(image, ['system:time_start']));
}

/// Algoritma Hu
var CHLcol = L8col.map(CI);
print('Hu et al. image composite', CHLcol);

//3. Tampilan Peta
Map.addLayer(CHLcol.mean(), {min: 1, max: 3}, "Chl", false);
```
![3_11](https://github.com/manessa-md/BUDEE/assets/108891611/199a131e-5ce9-45cd-8ce2-65a07bff4af0)


Kemudian dibuat grafik Timeseries dari Implementasi Algoritma Hu 2012 dengan Citra Landsat-8


```

//4. Grafik 
var chart = 
    ui.Chart.image
        .series({
          imageCollection:CHLcol,
          region: AOI,
          reducer: ee.Reducer.mean(),
          scale: 100,
          xProperty: 'system:time_start'
        })
        .setSeriesNames(['CHLhu'])
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


![4_3](https://github.com/manessa-md/BUDEE/assets/108891611/7fbe2279-97c7-4780-bbaa-882710f2d5a3)





