# Menerapkan Algoritma Chlorophyll pada citra  Landsat 8
---
Citra Landsat-8 Merupakan satelite observasi bumi milik Amerika yang diluncurkan pada tanggal 11 Februari 2013.  Landsat-8  memiliki dua sensor yaitu sensor Operational Land Imager (OLI) dan Thermal Infrared Sensor (TIRS). Kedua sensor ini menyediakan resolusi spasial 30 meter (visible, NIR, SWIR), 100 meter (thermal), dan 15 meter (pankromatik).


Pada modul ini akan menjelaskan mengenai pengampilakasin algoritma Chlorophyl pada Citra Landsat-8 yang dapat dilakukan dengan langkah-langkah sebagai berikut:
1. Menentukan Area Penelitian
2. Mengimport citra yang akan digunakan
3. Mengimport Data lapangan pada GEE
4. Mengimplementasi Algoritma CHL pada citra
5. Melakukan uji akurasi algoritma dengan data lapangan

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


## 3. Mengimport Data Lapangan 
Mengimport data yang digunakan yang nantinya akan digunakan untuk uji akurasi

![3_8](https://github.com/manessa-md/BUDEE/assets/108891611/3fce73d3-6862-45de-9455-4bae17c3ce03)

```
//Data
var point = ee.FeatureCollection("projects/ee-budeetraining/assets/Survey_point"); // ganti dengan link dari asset masing-masing
print(point);

//tampilkan di peta
Map.addLayer(point,{color:"red"}, "Titik Survey", false);
Map.centerObject(point);
```
## 4. Mengimpelentasi Algortima CHL
pada module ini menggunakan algoritma  CHL di peroleh dari penelitian terdahulu. 

![3_9](https://github.com/manessa-md/BUDEE/assets/108891611/0ea20e99-78f2-456e-a834-5430fcd81fbc)

```
function CHLarief2006(img){
  var B2 = img.select("B2");
  var B3 = img.select("B3");
  var a = ee.Image(B2).subtract(ee.Image(B3));
  var b = ee.Image(B2).add(ee.Image(B3));
  var rrs = ee.Image(a).divide(ee.Image(b));
  var CHL = ((ee.Image(rrs.multiply(17.912)).subtract(0.3343)).rename('CHLarief2006'));
  return ee.Image(CHL.copyProperties(img, ['system:time_start']));
}

//2. Implementasi Alg Arief
var CHLcom = CHLarief2006(L8com);
print('Arief 2006 image composite', CHLcom);

//3. Tampilan Peta
Map.addLayer(CHLcom, {min: 1, max: 3}, "Chl Arief", false);
```

![3_10](https://github.com/manessa-md/BUDEE/assets/108891611/fc0e94c4-478b-46c8-8d0c-c93791571e07)

## 5 Uji Akurasi Dengan Data Lapangan
Melakukan uji akurasi dari data algoritmanya di implementasikan pada citra Landsat-8 dengan data lapangan, untuk mengetahui keakuratan data dihasilkan oleh implementasikan pada citra.

```
//Uji Akurasi dengan data Lapangan
//1. Ekstraksi Nilai
var pointExtract = CHLcom.reduceRegions(point, ee.Reducer.first(), 30);
var pointE = pointExtract.filter(ee.Filter.neq('first', null));
print ('pointE chl', pointE);

//2. Grafik 
var chart = ui.Chart.feature.byFeature(pointE, 'Chl', ['first'])
  .setChartType('ScatterChart')
  .setOptions({
    titleX: 'Measured Chl',
    titleY: 'Predic Chl',
    pointSize: 3,
    trendlines: { 0: {showR2: true, visibleInLegend: true},
                   1: {showR2: true, visibleinLegen: true}}
  });
print(chart);
```
![3_12](https://github.com/manessa-md/BUDEE/assets/108891611/abafa8c8-c2c1-4e5d-bf6f-db81323f68e3)

Dari Hasil validasi yang dilakukan, dapat mengetahui hasil uji akurasi dari R-square dan RMSE.]
Dengan menggunakan Algoritma Arief2006 Diketahui Hasil R-square adalah 0.059 dan hasil RMSE adalah 0.82

```
//3. Metrik Akurasi
var observationTraining = ee.Array(pointE.aggregate_array('Chl'));
var predictionTraining = ee.Array(pointE.aggregate_array('first'));
//Compute Residuals
var residualsTraining = observationTraining. subtract(predictionTraining);
//Compute RMSE with equation, print it
var rmseTraining = residualsTraining.pow(2).reduce('mean', [0]).sqrt();
print('RMSE', rmseTraining);
```

![3_13](https://github.com/manessa-md/BUDEE/assets/108891611/b82d2ab1-5970-47b8-a17a-a1514067cb52)


## Pada modul ini juga mencoba algoritma CHL dari penelitian Hu 2012

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
            'Red': image.select('B4'), // *Designations for SR datasets>>
            'lambdaRed': 670,
            'Green': image.select('B3'),
            'lambdaGreen': 555,
            'Blue': image.select('B2'),
            'lambdaBlue': 443
        });
        
    var CIp = result.multiply(230.47).subtract(0.4287);
    var CHL = ee.Image(10).pow(CIp).rename('CHLhu'); 
   
    return ee.Image(CHL.copyProperties(image, ['system:time_start']));
}


/// Algoritma Hu
var CHLcom = CI(L8com);
print('Hu et al. image composite', CHLcom);

//3. Tampilan Peta
Map.addLayer(CHLcom, {min: 1, max: 3}, "Chl hu", false);
```
![3_11](https://github.com/manessa-md/BUDEE/assets/108891611/0cc8aa27-923a-43cf-92fb-6b766fbb189a)


```
//Uji Akurasi dengan data Lapangan
//1. Ekstraksi Nilai
var pointExtract = CHLcom.reduceRegions(point, ee.Reducer.first(), 30);
var pointE = pointExtract.filter(ee.Filter.neq('first', null));
print ('pointE chl', pointE);

//2. Grafik 
var chart = ui.Chart.feature.byFeature(pointE, 'Chl', ['first'])
  .setChartType('ScatterChart')
  .setOptions({
    titleX: 'Measured Chl',
    titleY: 'Predic Chl',
    pointSize: 3,
    trendlines: { 0: {showR2: true, visibleInLegend: true},
                   1: {showR2: true, visibleinLegen: true}}
  });
print(chart);
```
![3_14](https://github.com/manessa-md/BUDEE/assets/108891611/df8b540e-5041-43dd-af74-af3c5c0cd45c)


```
//3. Metrik Akurasi
var observationTraining = ee.Array(pointE.aggregate_array('Chl'));
var predictionTraining  = ee.Array(pointE.aggregate_array('first'));
//Compute Residuals
var residualsTraining = observationTraining.subtract(predictionTraining);
//Compute RMSE with equation, print it
var rmseTraining = residualsTraining.pow(2).reduce('mean', [0]).sqrt();
print('RMSE', rmseTraining);
```

![3_15](https://github.com/manessa-md/BUDEE/assets/108891611/95287633-89ce-48de-8bd7-ee5d5722616b)

Dari pengggunaan Algoritma CHL penelitian Hu2012, Diketahui hasil validasi data dari uji akurasi menggunakan R-square adalah 0.028 dan RMSE = 200,91.

Dari kedua penggunaan Algoritma CHL pada Citra Landsat 8, Algorima Arief2006 menghasilkan data yang lebih baik dibandingkan menggunakan Algoritma Hu2012






