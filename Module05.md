# Global Change Observation Mission
 Global Change Observation Mission (GCOM) merupakan project obeservasi global jangka panjang terhadap perubahan lingkungan bumi yang dikeluarkan oleh JAXA.
 GCOM terdiri dari dua satelit yaitu GCOM-W and GCOM-C. Satelit GCOM-W memiliki misi untuk mengobservasi perubahan sirkulasi perairan. 
 Sedangkan, Satelit GCOM-C memiliki misi untuk mengobservasi perubahan iklim melalui Second Generation Global Imager (SGLI).
 SGLI melakukan pengukuran permukaan dan atmosfer yang terkait dengan siklus karbon dan radiasi seperti awan, aeroso, ocean color, vegetasi, salju dan es.
 
### 1. Membuat Area Penelitian
 Selain menggunakan tools geometry, area penelitian dapat dibuat dengan mengimport shapefile atau mengakses asset shapefile.
 
![image](https://github.com/manessa-md/BUDEE/assets/108908781/694e4287-0e0c-4036-935e-220e1127e2f3)
Gambar 1. Mengimport asset shapefile area penelitian pada kolom Imports

 Jika shapefile area penelitian terdiri dari beberapa area, maka area penelitian dapat dipilih sesuai dengan yang dibutuhkan dengan menggunakan code .filter()

```
 var poi = zone.filter(ee.Filter.eq('Zona', 'Flores'));
```

### 2. Membuka data Klorofil-a pada GCOM-C
 GCOM-C menyediakan produk konsentrasi klorofil-a Level 3 (L3) melalui pigmen fotosintetik pada fitoplaknton di lapisan permukaan laut.

 ![image](https://github.com/manessa-md/BUDEE/assets/108908781/a3ea8ea4-7563-44b4-b0bd-b108c4390860)
 Gambar 2. Produk Klorofil-a dari Satelit GCOM-C

 Dalam membuka data klorofil-a, diperlukan beberapa pengaturan seperti pengaturan tanggal menggunakan .filterDate() dan mem-filter data satelit menggunakan .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'), dimana 'D' menampilkan data daytime. Berikut merupakan code untuk membuka data klorofil-a

 ```
// Data Penginderaan Jauh Chlorophyll-a

/// GCOM-C/SGLI L3 Chlorophyll-a Concentration (V1) 

//// Membuka data menggunakan script berikut

var dataset = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V1')
                .filterDate('2020-01-01', '2020-02-01')
                // filter to daytime data only
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
```
Kemudian, dibutuhkan pengaturan kalibrasi slope coefficient sebagai berikut:

```
var image = dataset.mean().multiply(0.0016).log10();
```

Tahapan terakhir ialah visualisasi, berikut merupakan script visualiasi:

```
// Setting Visualisasi
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

// Menampilkan image pada Map
Map.addLayer(image, vis, 'Chlorophyll-a concentration');
Map.setCenter(123.8547, -0.8266, 5);
```

### 3. Hasil Konsentrasi Klorofil-a oleh Satelit GCOM-C

![image](https://github.com/manessa-md/BUDEE/assets/108908781/b89ad12a-c069-4e1a-9af7-15596df74e1e)
Gambar 3. Hasil Konsentrasi Klorofil-a pada Wilayah Indonesia oleh Satelit GCOM-C

# Time Series Global Change Observation Mission
Pembuatan time series dengan menggunakan data Global Change Observation Mission (GCOM) ialah dengan menggabungkan beberapa citra dengan rentang waktu yang berbeda.
Melalui time series, kita dapat mengetahui informasi variasi dari suatu parameter.

### 1. Memilih Beberapa Citra GCOM-C Produk Klorofil-a dengan Perbedaan Rentang Waktu 
Pemilihan rentang waktu harus disesuaikan dengan ketersediaan data Citra Satelit. Pada saat ini, GCOM-C menyediakan produk klorofil-a terdiri dari tiga version yaitu 'JAXA/GCOM-C/L3/OCEAN/CHLA/V1' ; 'JAXA/GCOM-C/L3/OCEAN/CHLA/V2'; dan 'JAXA/GCOM-C/L3/OCEAN/CHLA/V3'

![image](https://github.com/manessa-md/BUDEE/assets/108908781/5b3b5901-1424-4183-88c7-170f6233398f)
Gambar 4. Ketersediaan data dalam rentang waktu pada setiap versi produk klorofil-a GCOM-C

Pemilihan rentang waktu pada setiap versi produk yang berbeda diusahakan tidak beririsan. Sedangkan, pengaturan parameter dapat dilakukan seperti tahapan sebelumnya. Berikut merupakan code pemilihan beberapa citra:

```
// Data Penginderaan Jauh Chlorophyll-a

/// GCOM-C/SGLI L3 Chlorophyll-a Concentration (V1) 

//// Membuka data menggunakan script berikut


var v1 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V1')
                .filterDate('2018-01-01', '2020-06-28')
                // filter to daytime data only
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
                
var v2 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V2')
                .filterDate('2020-06-28', '2021-11-28')
                // filter to daytime data only
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
                
var v3 = ee.ImageCollection('JAXA/GCOM-C/L3/OCEAN/CHLA/V3')
                .filterDate('2021-11-29', '2023-06-22')
                // filter to daytime data only
                .filter(ee.Filter.eq('SATELLITE_DIRECTION', 'D'));
```
### 2. Pengaturan Kalibrasi Slope Coefficient
Berikut merupakan kalibrasi slope coefficient:

```
function calibrate(image){
  return image.multiply(0.0016).copyProperties(image, ['system:time_start']);
}
```
### 3. Penggabungan Beberapa Citra yang Telah Terpilih
Pembuatan timeseries dilakukan dengan menggabungkan beberapa citra dengan rentang waktu yang berbeda. Codenya ialah sebagai berikut:

```
var gcom = v1.merge(v2).merge(v3).select(['CHLA_AVE']).map(calibrate);
```

### 4. Visualisasi Timeseries Citra
Berikut merupakan code untuk visualisasi citra:

```
// Setting Visualisasi
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

// Menampilkan image pada Map
Map.addLayer(gcom, vis, 'Chlorophyll-a concentration');
Map.setCenter(123.8547, -0.8266, 5);
```
### 5. Hasil Timeseries Konsentrasi Klorofil-a pada Citra GCOM-C

![image](https://github.com/manessa-md/BUDEE/assets/108908781/c8a10d17-46cb-46a3-89ec-fc566fd94deb)
Gambar 5. Hasil Timeseries Konsentrasi Klorofil-a pada Wilayah Indonesia oleh Satelit GCOM-C
