---
layout: default
title: "Module 1: Dasar Coding JavaScript dan Pengenalan Google Earth Engine"
permalink: /BUDEE/module1.html
---

{% include _sidebar.md %}


# Pelatihan Google Earth Engine 
Module Google Earth Engine Interface
--------------

Google earth engine memiliki tampilan yang sangat sederhana dengan fungsi yang sangat beragam. Pengolahan data secara spasial maupun statistik dapat dilakukan pada aplikasi ini. 

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/gee_editor.png)

**Gambar 1. User Interface Google Earth Engine**

## 1. Search box
Merupakan kotak pencarian seperti pada laman google.com, namun pada aplikasi google earth engine search box digunakan hanya untuk mencari citra satelit. Kata kunci pencarian dapat menggunakan nama citra yang dicari atau fungsi dari citra tersebut. Berikut merupakan contoh pencarian citra sentinel pada search box.

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/searchbox.png)

**Gambar 2. Search Box Google Earth Engine**

## 2. Script, doc dan asset
### Script
Pada bagian script terdapat script _coding_ yang sudah pernah dibuat dan di save, serta terdapat beberapa contoh script aplikasi citra satelit yang dibuat oleh tim google. 

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/leftpanel.png)

**Gambar 3. Left Panel Google Earth Engine**

Pada bagian ini juga terdapat menu **new** untuk menambahkan reprosit0ry, folder, file. Repository merupakan tempat menyimpan dan folder. Sedangkan folder merupakan kumpulan file. Pembuatan reprository, folder, dan file dapat dilakukan dengan memilih menu **new** kemudian pilih apa yang akan dibuat dan diberikan nama sesuai kebutuhan.

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/repositoy.png)

**Gambar 4. Pembuatan Repository Google Earth Engine**

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/folder.png)

**Gambar 5. Pembuatan Folder Google Earth Engine**

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/77d428e0f74866bff2a1e87174fa48a0f9f21c1d/Image/eeInterface/file.png)

**Gambar 6. Pembuatan File Google Earth Engine**

### Doc
Pada bagian doc terdapat instruksi atau contoh dari function. Bagian ini sangat berguna karena terkadang kita tidak dapat menghafal semua script dan fungsinya. 

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/a01a101452988f53cad2cefcf2df3100dabe3f33/Image/eeInterface/docs.png)

**Gambar 7. Docs ee.Filter Google Earth Engine**

### Asset
Bagian terakhir yaitu asset, yaitu bagian yang memiliki fungsi sebagai tempat untuk mengunggah dan menyimpan file spasial (json, kml, shp dan sebagainya) dari komputer menuju earth engine. Menambahkan asset (file geospasial) dilakukan dengan memilih menu **new** kemudian pilih jenis file yang akan ditambahkan (Csv, Shp, Geotiff, Json). 

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/4e3cfc96e2fa5b007551a240752ad58fc702402e/Image/eeInterface/newasset.png)

**Gambar 8. New Asset Google Earth Engine**

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/4e3cfc96e2fa5b007551a240752ad58fc702402e/Image/eeInterface/shapefileasset.png)

**Gambar 9. Menambahkan Shapefile pada Google Earth Engine**

## 3. Code Editor
Merupakan bagian google earth engine untuk pengguna menuliskan _coding_ atau perintah pengolahan data spasial terhadap komputer google (cloud computing). Pada bagian ini perintah dituliskan dalam bahasa pemrograman javascript. Pada bagian ini juga terdapat tools save untuk menyimpan hasil _coding_, run untuk menjalankan _coding_ dan reset untuk menghapus semua _coding_ yang dibuat.

![eeInterface](https://github.com/manessa-md/UNODC-PAPUA-EE-2022.github.io/blob/14e81ca556bf6d42306c3c11466b3ee6cd34eca3/Image/eeInterface/code.png)

**Gambar 10. Code Editor Google Earth Engine**

## 4. Inspector, console dan task
### Inspector
Inspector merupakan bagian dalam earth engine dimana computer google mendeteksi kesalahan perintah yang diberikan. Biasanya terdapat warna merah pada baris perintah yang salah. 
### Console
Console merupakan bagian earth engine untuk menampilkan hasil data non spasial seperti data citra dan hasil pengolahan statistik. 
### Task
Sedangkan task merupakan bagian untuk melakukan dan melihat upload data hasil pengolahan di google earth engine.

## 5. Geometry tools
Geometry tools merupakan alat dalam google earth engine yang memiliki fungsi untuk membuat bentuk bentuk geometri dengan nilai koordinat di muka peta. Bentuk geometri yang dapat dibuat berupa titik, garis dan polygon. Langkah pembuatan objek geometry dilakukan dengan memilih objek yang akan dibuat kemudian langsung membuat objek dalam peta. Berikut merupakan contoh pembuatan geometry titik, garis, polygon dan rectangle.

![image](https://github.com/manessa-md/BUDEE/assets/108908781/18556869-3048-4baa-aba0-a20a954beeb8)

**Gambar 10. Pembuatan Geometry Point Google Earth Engine**

![image](https://github.com/manessa-md/BUDEE/assets/108908781/4e9e8321-9e0e-42e3-a07e-1794e4233322)

**Gambar 11. Pembuatan Geometry Garis Google Earth Engine**

![image](https://github.com/manessa-md/BUDEE/assets/108908781/be4f7a21-d6a5-446a-86ae-2708a76a1ebe)
![image](https://github.com/manessa-md/BUDEE/assets/108908781/bb703f44-1176-4a8e-ab3b-9586e77ed658)

**Gambar 12. Pembuatan Geometry Polygon dan Rectangle Google Earth Engine**

## 6. Muka Peta
Merupakan bagian utama dimana hasil dari perintah _coding_ dapat ditampilkan melalui proses visualiasi. Pada bagian muka peta juga terdapat layer dan jenis peta dasar. Layer merupakan lapisan peta yang ditampilkan selain peta dasar sedangkan peta dasar yang disediakan google terdapat 2 jenis yaitu peta satelit dan garis.

![image](https://github.com/manessa-md/BUDEE/assets/108908781/ff97a149-1bbd-49a1-962c-f2408e65f4af)

**Gambar 13. Peta Garis Google Earth Engine**

![image](https://github.com/manessa-md/BUDEE/assets/108908781/7d100fcc-dbf7-40b7-9c41-715bfcf7a3be)

**Gambar 14. Peta Satelit Google Earth Engine**

---------

# Module 1b Dasar JavaScript


## 1.1. Apa itu JavaScript
[Javascript](https://www.javascript.com/) adalah sebuah bahasa pemrograman atau perintah pada komputer yang pada awalnya digunakan untuk pengembangan website. Bahasa javascript diciptakan oleh Brendan Eich yang merupakan karyawan Netscape. Versi awal bahasa javascript hanya dipakai di kalangan Netscapedengan fungsi yang masih terbatas. Pada tahun 1996 JavaScript secara resmi dinamakan sebagai ECMAScript. Bahasa ECMAScript terus dikembangkan sampai akhirnya menjadi JavaScript hingga saat ini. Penggunaan bahasa javascript ini sangat global dan banyak digunakan oleh pengembang software dan website. Pada tahun 2016, 92% website diketahui telah menggunakan JavaScript. JavaScript tergolong bahasa tingkat tinggi. Artinya ia punya aturan penulisan yang menyerupai bahasa manusia. Dengan begitu, belajar JavaScript jadi lebih mudah. Bahkan untuk pemula sekalipun. Bahasa JavaScript sendiri harus dijalankan oleh interpreter. Code harus diterjemahkan ke dalam bahasa yang dimengerti komputer agar bisa dijalankan. Nah, proses penerjemahan ini dilakukan secara otomatis oleh web browser. Dalam perkembangannya bahasa javascript juga dapat untuk memberi perintah kepada komputer untuk melakukan sebuah perintah seperti mengolah data spasial, melakukan perhitungan algoritma, hingga memvisualiasikan hasil. 

## 1.2. Dasar _Coding_ di JavaScript
Sama seperti bahasa dalam bahasa Javascript juga terdapat beberapa kata yang digunakan untuk melakukan sesuatu. Berikut merupakan beberapa _coding_ atau kata dasar yang sering digunakan untuk mengiring perintah lanjutan.

### 1. var
Merupakan _coding_ yang digunakan untuk mendefinisikan sebuah variabel. Pendefinisian variabel dalam javascript dapat dilakukan menggunakan var, let, dan const. let dan const digunakan dalam mendefinisikan variabel yang lebih detail. Apabila variabel general maka gunakan **const** sedangkan apabila variabel dapat berubah maka dapat menggunakan **let**. Variabel juga merupakan wadah untuk menyimpan data (menyimpan nilai data), sebagai berikut. 
```
var papua = "Kota Jayapura"
```

### 2. print
Merupakan _coding_ yang digunakan untuk mencetak hasil dari variabel, sebagai berikut.
```
var papua = "Kota Jayapura"
print(papua); //Hasil akan menunjukkan Kota Jayapura
```

### 3. if
Merupakan _coding_ yang digunakan untuk menandai atau menarik sebuah pernyataan dengan sebuah kondisi tertentu, sebagai berikut.
```
if (condition) {
  //  block of code to be executed if the condition is true
}
```

```
if (waktu > 18) {
  ucapan = "Selamat Malam";
}
```

### 4. function
_Coding_ ini merupakan perintah untuk menjalankan suatu fungsi tertentu, sebagai berikut.
```
var x = cobaFunction(5, 10);   //Contoh variabel x menggunakan function

function cobaFunction(a, b) {
  return a * b;                
}

print (x)

= 50
```

### 5. return
Merupakan _coding_ yang digunakan untuk menutup / exit function. Contoh dapat dilihat pada bagian function.

## 1.2. Penulisan variabel
Penamaan variabel dalam javascript tidak dapat dilakukan sembarangan seperti menulis dalam word, terdapat beberapa ketentuan yang harus diikuti, sebagai berikut.

### 1. Variabel tidak boleh dituliskan dengan menggunakan angka di depannya.
```
// salah
var 123jayapura = "Papua";
  
// benar
var jayapura123 = "Papua";
```

### 2. Variabel dapat dituliskan mengguankan awalan underscore. 
```
var _jayapura = "Papua";
```

### 3. Variabel disarankan menggunakan camelCase apabila terdiri dari dua suku kata
```
var wonderfulIndonesia = "Wonderfull Papua";
```

```
var link = '86836482971a35a5e735a17e93c23272';
Export.table.toDrive({
  collection: ee.FeatureCollection([ee.Feature(null, stats)]),
  description: 'exported_stats_demo_' + link,
  fileFormat: 'CSV'
})
```

## 1.3. Jenis jenis data dalam Javascript 
Dalam mengenali jenis data javascript dapat langsung mengenali jenis data yang dituliskan (_dynamic typing_), tidak seperti pada bahasa C, C++, Java, dsb yang harus menuliskan tipe data pada saat pembuatan variabel (_static typing_). Berikut merupakan beberapa jenis data yang digunakan dalam bahasa javascript

1. Object
2. Date
3. Array
4. String (teks)
5. Integer atau Number (bilangan bulat)
6. Boolean
7. Float (bilangan Pecahan)
8. Function

### 1.3.1. Object
Jenis data object merupakan tipe data yang memiliki lebih dari satu nilai. Perbedaan dengan array terletak pada indeks, dimana object tidak punya indeks tapi nama. Selain itu, pada object dapat memasukan Function dan Array sehingga lebih praktis.
```
var jayapura = {provinsi:"Papua", administrasi:"Kota", luas:940, jumlahpenduduk:315.872};

//contoh lain menggunakan enter sehingga lebih mudah dilihat

var person = {
  provinsi: "Papua",
  administrasi: "Kota",
  luas: 940,
  jumlahpenduduk: 315.872
};
```

### 1.3.2. Date
Penulisan tanggal pada javascript dapat dilakukan dengan menggunakan struktur **new date()**. Berikut merupakan beberapa bentuk penulisan tanggal yang dapat dilakukan pada bahasa javascript.
```
new Date()
new Date(year, month, day, hours, minutes, seconds, milliseconds)
new Date(milliseconds)
new Date(date string)

///contoh
var d = new Date(2018, 11, 24, 10, 33, 30);
var d = new Date(2018, 11);
var d = new Date("October 13, 2014 11:13:00");
```

### 1.3.3. Array
Jenis data array merupakan jenis data yang memiliki banyak isi. Seperti variabel dengan banyak isi. Array diciptakan untuk mempersingkat variabel tertentu.
```
var kota1 = "Jayapura";
var kota2 = "Sentani";
var kota3 = "Biak";
```

### 1.3.4. String 
String merupakan jenis data berupa teks atau tulisan, sebagai berikut.

```
//Contoh
var indonesia = "papua";
```

### 1.5.5. Number 
Merupakan jenis data berupa bilangan, sebagai berikut
```
var indonesia = 1945
```

### 1.4.6. Boolean
Merupakan jenis data berupa dengan dua hasil seperti YES / NO, ON / OFF, TRUE / FALSE, BENAR / SALAH.
```
Boolean(10 > 9)   //true
```

### 1.4.7 Float
Hampir sama dengan jenis data bilangan, namun data float memiliki desimal, sebagai berikut.
```
var y = 192e5;      // 19200000
var z = 192e-5;     // 0.00192
```

### 1.4.8 Function
Function merupakan sebuah fungsi khusus yang dibuat untuk melakukan tugas tertentu seperti menghitung.
Berikut merupakan rumus dasar function pada javascript.
```
function name(parameter1, parameter2, parameter3) {
  // code to be executed
}

//contoh
var x = cobaFunction(5, 10);   //Contoh variabel x menggunakan function

function cobaFunction(a, b) {
  return a * b;                
}

print (x)

= 50
```

[JavaScript background](https://developers.google.com/earth-engine/tutorial\_js\_01)



