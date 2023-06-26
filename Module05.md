# Global Change Observation Mission
 Global Change Observation Mission (GCOM) merupakan project obeservasi global jangka panjang terhadap perubahan lingkungan bumi yang dikeluarkan oleh JAXA.
 GCOM terdiri dari dua satelit yaitu GCOM-W and GCOM-C. Satelit GCOM-W memiliki misi untuk mengobservasi perubahan sirkulasi perairan. 
 Sedangkan, Satelit GCOM-C memiliki misi untuk mengobservasi perubahan iklim melalui Second Generation Global Imager (SGLI).
 SGLI melakukan pengukuran permukaan dan atmosfer yang terkait dengan siklus karbon dan radiasi seperti awan, aeroso, ocean color, vegetasi, salju dan es.
 
### 1. Membuat Area Penelitian
 Selain menggunakan tools geometry, area penelitian dapat dibuat dengan mengimport shapefile atau mengakses asset shapefile.
 
 ![image](https://github.com/manessa-md/BUDEE/assets/108908781/0b655594-b026-4faf-9e80-dd5586d5b217)
 Gambar 1. Mengimport asset shapefile area penelitian pada kolom Imports

 Jika shapefile area penelitian terdiri dari beberapa area, maka area penelitian dapat dipilih sesuai dengan yang dibutuhkan dengan menggunakan code .filter()

```
 var poi = zone.filter(ee.Filter.eq('Zona', 'Flores'));
```

### 1. Membuka data Klorofil-a pada GCOM-C




