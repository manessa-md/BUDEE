# Menerapkan Algoritma Chlorophyll pada Citra Landsat 8

## 1. Pendahuluan
Landsat-8 adalah satelit penginderaan jauh milik Amerika Serikat yang diluncurkan pada 11 Februari 2013. Satelit ini memiliki dua sensor utama, yaitu **Operational Land Imager (OLI)** dan **Thermal Infrared Sensor (TIRS)**. Sensor OLI digunakan untuk mengamati reflektansi di spektrum tampak dan inframerah dekat (NIR), sementara TIRS digunakan untuk mengamati emisi termal dari permukaan bumi.

Landsat-8 memiliki resolusi spasial:
- 30 meter untuk band tampak (B2-B4), NIR (B5), dan SWIR (B6-B7).
- 100 meter untuk band termal (TIRS: B10 dan B11).
- 15 meter untuk band pankromatik (B8).

Pada modul ini, kita akan menerapkan algoritma estimasi konsentrasi klorofil-a (Chl-a) menggunakan dua metode berbeda pada citra Landsat-8, yaitu **algoritma Arief 2006** dan **algoritma Hu et al. 2012**. Selain itu, akan dilakukan uji akurasi terhadap data lapangan.

---

## 2. Langkah-langkah Implementasi
### 2.1. Menentukan Area Penelitian
Menentukan area yang akan dianalisis dengan membuat poligon area penelitian:
```javascript
// Menentukan area penelitian dari koleksi fitur
var AOI = ee.FeatureCollection("projects/ee-budeetraining/assets/Banggai_area"); // Ganti sesuai dengan aset Anda
```

### 2.2. Mengimpor Citra Landsat-8
Citra yang digunakan adalah **Landsat 8 Level 2, Collection 2, Tier 2**. Proses ini mencakup:
1. Pemfilteran berdasarkan tanggal.
2. Masking awan dan bayangan awan menggunakan band QA_PIXEL.
3. Pemilihan band yang diperlukan dan pemotongan (clipping) berdasarkan area penelitian.

```javascript
// Fungsi untuk masking awan
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
              .filterDate('2022-07-01', '2022-09-30')
              .map(maskL8sr)
              .select('SR_B[1-7]')
              .map(function(image){return image.rename(['B1', 'B2', 'B3', 'B4','B5','B6','B7']).clip(AOI)});
var L8com = L8col.median();

// Menampilkan citra RGB
Map.addLayer(L8col, {bands: ['B4', 'B3', 'B2'], min:0, max: 0.3}, "RGB Landsat", false);
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

### 2.4. Mengimplementasi Algoritma CHL
#### Algoritma Arief 2006
```javascript
function CHLarief2006(img){
  var B2 = img.select("B2");
  var B3 = img.select("B3");
  var a = ee.Image(B2).subtract(ee.Image(B3));
  var b = ee.Image(B2).add(ee.Image(B3));
  var rrs = ee.Image(a).divide(ee.Image(b));
  var CHL = ((ee.Image(rrs.multiply(17.912)).subtract(0.3343)).rename('CHLarief2006'));
  return ee.Image(CHL.copyProperties(img, ['system:time_start']));
}

var CHLcom = CHLarief2006(L8com);
print('Arief 2006 image composite', CHLcom);
Map.addLayer(CHLcom, {min: 1, max: 3}, "Chl Arief", false);
```

#### Algoritma Hu et al. 2012
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
    return ee.Image(CHL.copyProperties(image, ['system:time_start']));
}

var CHLcom_Hu = CI(L8com);
print('Hu et al. image composite', CHLcom_Hu);
Map.addLayer(CHLcom_Hu, {min: 1, max: 3}, "Chl Hu", false);
```

### 2.5. Uji Akurasi dengan Data Lapangan
```javascript
var pointExtract = CHLcom.reduceRegions(point, ee.Reducer.first(), 30);
var pointE = pointExtract.filter(ee.Filter.neq('first', null));
print ('pointE chl', pointE);

var chart = ui.Chart.feature.byFeature(pointE, 'Chl', ['first'])
  .setChartType('ScatterChart')
  .setOptions({
    titleX: 'Measured Chl',
    titleY: 'Predic Chl',
    pointSize: 3,
    trendlines: { 0: {showR2: true, visibleInLegend: true} }
  });
print(chart);
```

## 3. Kesimpulan
Dari hasil validasi:
- **Algoritma Arief 2006**: R² = 0.059, RMSE = 0.82.
- **Algoritma Hu et al. 2012**: R² = 0.028, RMSE = 200.91.

**Algoritma Arief 2006 menunjukkan hasil yang lebih baik dibandingkan Algoritma Hu 2012 untuk estimasi klorofil-a.**

---

## 4. Tugas Modifikasi Kode
Sebagai latihan tambahan, lakukan modifikasi berikut:
1. **Gunakan Landsat 9** sebagai alternatif untuk melihat perbedaan hasil.
2. **Tambahkan mask air** untuk menghilangkan daerah daratan dari analisis.
3. **Coba metode estimasi klorofil lain**, seperti algoritma OC3-OceanColor3.

Silakan implementasikan dan bandingkan hasilnya dengan algoritma di atas. Selamat mencoba!

