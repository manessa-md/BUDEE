---
layout: default
title: "Module 3: Dasar Coding Google Earth Engine"
permalink: /BUDEE/module3.html
---

{% include _sidebar.md %}

# PANDUAN DASAR GOOGLE EARTH ENGINE
Dasar Koding
--------------

## Mengenal Kombinasi Band dan Visualisasi Citra Sentinel-2 menggunakan Google Earth Engine 
Konten Panduan
- Memilih Citra
- Memahami Informasi Citra Sentinel-2
- Load dan Filtering Citra Sentinel-2
- Visualisasi Citra Sentinel-2
- Memilih data dari ImageCollection

### Memilih Citra

Pemilihan citra bisa dilakukan dengan mencari pada tabel pencarian dengan memasukan _keyword_ nama citra satelit "Sentinel-2".

<img width="960" alt="Mod2-S2-01" src="https://user-images.githubusercontent.com/69818715/158024527-fa08e8f8-bf59-4cff-b4ff-4381af3abc15.png">
Gambar 14. Tampilan Search Dataset Citra

### Memahami Informasi Citra Sentinel-2

Agar data citra satelit yang kita pilih sesuai dengan yang kita inginkan, maka perlu untuk _cross check_ data tersebut dengan memahami informasi terkait data citranya. Cara untuk mengetahui informasi dengan _klik_ citra yang hendak kita pilih, pada latihan kali ini menggunakan data citra Sentinel-2 level 2A, setelah itu akan tampil informasi citra seperti pada gambar berikut:

<img width="960" alt="Mod2-S2-02" src="https://user-images.githubusercontent.com/69818715/158025267-f658b97c-4fe1-4b70-ae64-da0efdad71ab.png">
Gambar 15. Informasi Citra Satelit

### Memanggil data Citra Sentinel-2

Tahap pertama pada visualisasi citra adalah proses _load_ dan _filtering_ citra satelit dimana citra satelit yang hendak dipilih di _setting_ sesuai dengan keperluan. Filter yang penting utnuk dilakukan meliputi filtering waktu/date, filtering wilayah tutupan citra, filtering persentase citra dan masking citra. 
```
/**
 * Function to mask clouds using the Sentinel-2 QA band
 * @param {ee.Image} image Sentinel-2 image
 * @return {ee.Image} cloud masked Sentinel-2 image
 */
function maskS2clouds(image) {
  var qa = image.select('QA60');

  // Bits 10 and 11 are clouds and cirrus, respectively.
  var cloudBitMask = 1 << 10;
  var cirrusBitMask = 1 << 11;

  // Both flags should be set to zero, indicating clear conditions.
  var mask = qa.bitwiseAnd(cloudBitMask).eq(0)
      .and(qa.bitwiseAnd(cirrusBitMask).eq(0));

  return image.updateMask(mask).clip(geometry).divide(10000);
}
var S2 = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
                  .filterDate('2022-01-01', '2023-06-10')
                  .filter(ee.Filter.bounds(geometry))
                  // Pre-filter to get less cloudy granules.
                  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE',15))
                  .map(maskS2clouds);
                  
print(S2)
```
![image](https://github.com/manessa-md/BUDEE/assets/108908781/1d5f37b0-8e7a-4b46-bbd7-05dc17c8cb18)
Gambar 16. Tampilan Coding Load dan Filtering Citra Satelit

### Visualisasi Citra Sentinel-2

Data citra yang ditampilkan masih berupa ImageCollection (Koleksi Citra) Sentinel-2. Data ImageCollection yang telah di pilih diatas dapat disesuaikan tampilannya agar sesuai dengan informasi yang akan ditampilkan. Visualisasi terdiri dari komposit band atau pallete (pemilihan warna). Selain itu, dapat mengatur hasil minimal dan maksimal data nilai piksel/panjang gelombang.

```
var visualization = {
  min: 0.0,
  max: 0.3,
  bands: ['B4', 'B3', 'B2'],
};

Map.addLayer(S2.first(), visualization, 'RGB');
```
![image](https://github.com/manessa-md/BUDEE/assets/108908781/f87e58b1-d216-418b-91b1-fe907bec676a)
Gambar 17. Tampilan Visualisasi Citra Sentinel-2

### Memilih Visualisasi Data Image dari ImageCollection

Pada tahap visualisasi juga dapat memungkinkan untuk menampilkan beberapa image dari Image Collection sentinel-2. Hal tersebut dilakukan untuk memilih image yang sesuai untuk divisualisasikan atau untuk tujuan perbandingan antar image. Pemilihan tampilan image diawali dengan perintah .toList() dan dilanjutkan dengan .get().

```
var listOfImages = S2.toList(S2.size());
var img1 = ee.Image(listOfImages.get(0));
var img2 = ee.Image(listOfImages.get(1));

print(img1)

Map.addLayer(img1, visualization, 'img1');
```
![image](https://github.com/manessa-md/BUDEE/assets/108908781/ef78434f-8e5a-4f36-aa53-028a16edaa6d)

Gambar 18. Tampilan Hasil Pemilihan Data Image

Informasi image yang terpilih dapat diketahui dengan menggunakan perintah print(). Kemudian, informasi mengenai band dan tanggal citra akan muncul pada box console.

![image](https://github.com/manessa-md/BUDEE/assets/108908781/3180bb61-6141-41fa-85d8-c40afdd8ae9e)

Gambar 19. Tampilan Informasi Tanggal Citra pada Pemilihan Data Image


