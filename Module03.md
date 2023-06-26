# Menerapkan Algoritma Chlorophyll pada citra  Landsat 8
---
Pengampilakasin algoritma Chlorophyl dapat dilakukan dengan langkah-langkah sebagai berikut:
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
var point = ee.FeatureCollection("projects/ee-amalias20l/assets/CTD2");
print(point);

//tampilkan di peta
Map.addLayer(point,{color:"red"}, "Titik Survey", false);
Map.centerObject(point);
```
## 4. Mengimpelentasi Algortima CHL
pada module ini mencoba menggunakan dua algoritma, algoritma di peroleh dari penelitian terdahulu. 

### 1. Algoritma Arief 2006
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


### 2. Algoritma Hu 2012

```
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
      var CHL = result.rename('CHLhu');
    return ee.Image(CHL.copyProperties(image, ['system:time_start']));
}

/// Algoritma Hu
var CHLcom = CI(L8com);
print('Hu et al. image composite', CHLcom);

//3. Tampilan Peta
Map.addLayer(CHLcom, {min: 1, max: 3}, "Chl hu", false);
```
![3_11](https://github.com/manessa-md/BUDEE/assets/108891611/0cc8aa27-923a-43cf-92fb-6b766fbb189a)









