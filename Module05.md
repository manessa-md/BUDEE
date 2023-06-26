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
Kemudian, dibutuhkan pengaturan slope coefficient sebagai berikut:

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



