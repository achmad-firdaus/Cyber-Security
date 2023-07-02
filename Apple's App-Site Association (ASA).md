# Apple's App-Site Association (ASA) <img src="https://raw.githubusercontent.com/MartinHeinz/MartinHeinz/master/wave.gif" width="30px">
![](https://img.shields.io/badge/-Cyber_Security-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=26D4C8&width=)
  <a href="https://www.instagram.com/achmd.f_/?next=%2F">
    <img alt="Abhishek's Instagram" width="22px" src="https://raw.githubusercontent.com/hussainweb/hussainweb/main/icons/instagram.png" />
  </a>
  <a href="https://www.linkedin.com/in/achmad-firdaus-b4a290116/">
    <img alt="Abhishek's LinkedIN" width="22px" src="https://raw.githubusercontent.com/peterthehan/peterthehan/master/assets/linkedin.svg" />
  </a>


![image](https://github.com/achmad-firdaus/Cyber-Security/assets/77251566/b674c1d1-b1b7-4053-a945-a7f280c6a2b9)

Pada bagian ini saya akan sedikit menjelaskan tentang Apple's App-Site Association (ASA), apa itu ? apakah harus dapat diakses ? apakah itu adalah robots.txt yang baru ?


Tags:
![](https://img.shields.io/badge/Apple's_App_Site_Association-(ASA)-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=2bbc8a&color=2ba9bc)
<a href="https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html">
![](https://img.shields.io/badge/-Reference-informational?style=flat&logo=<LOGO_NAME>&logoColor=white&color=2ba9bc&width=)
</a>


### LIST
- [<b>Introduction</b>](#introduction-)
  - [What Is Apple's App-Site Association (ASA) ?](#what-is-apples-app-site-association-asa-)
  - [Where Is the file and What Is the file name ?](#where-is-the-file-and-what-is-the-file-name-)
  - [Does It have to be accessible via HTTPS ?](#does-it-have-to-be-accessible-via-https-)
  - [Is Apple's App-Site Association (ASA) the new robots.txt ?](#is-apples-app-site-association-asa-the-new-robotstxt-)



### 👻
  - #### What Is Apple's App-Site Association (ASA) ?
    Apple's App-Site Association (ASA) adalah kerangka teknologi yang diperkenalkan oleh Apple yang memfasilitasi integrasi yang mulus antara aplikasi iOS dan situs web. Ini memungkinkan pengembang untuk menjalin koneksi antara aplikasi iOS asli mereka dengan situs web atau aplikasi web mereka. Tujuan utama dari ASA adalah untuk menciptakan pengalaman pengguna yang konsisten dan terintegrasi di berbagai platform.

    ASA bergantung pada konsep universal link, yaitu URL yang dapat digunakan untuk membuka baik aplikasi iOS asli maupun situs web yang sesuai. Dengan mengonfigurasi file ASA di situs web mereka dan mengimplementasikan kode yang diperlukan di aplikasi iOS mereka, pengembang dapat menghubungkan domain web tertentu dengan aplikasi mereka. Asosiasi ini memastikan bahwa ketika pengguna mengetuk universal link yang terkait dengan domain situs web aplikasi, tautan tersebut akan membuka aplikasi daripada situs web, jika aplikasi sudah terpasang di perangkat pengguna.



  - #### Where Is the file and What Is the file name ?
    File Apple's App-Site Association (ASA) biasanya diberi nama "apple-app-site-association" adalah file JSON yang dihosting di situs web pengembang. File ini harus ditempatkan di direktori utama (root directory) situs web, yaitu direktori utama di mana file dan folder situs lainnya disimpan. File ini berisi informasi tentang identifier paket aplikasi, domain web yang terkait dengannya, dan detail konfigurasi tambahan. File ini sangat penting untuk menjalin koneksi antara aplikasi dan situs web. Biasanya akan seperti ini:
    - https://{{domain}}/apple-app-site-association , atau
    - https://{{domain}}/.well-known/apple-app-site-association.



  - #### Does It have to be accessible via HTTPS ?
    Ketika pengguna berinteraksi dengan universal link, iOS memeriksa keberadaan file ASA di domain web yang sesuai. Jika file ada dan pengguna telah menginstal aplikasi yang terkait, iOS dengan mulus membuka aplikasi dan meneruskan data yang relevan dari tautan tersebut. 

    Kerangka kerja App-Site Association memungkinkan pengembang untuk menyederhanakan pengalaman pengguna dengan transisi yang mulus antara aplikasi dan situs web, tergantung pada perangkat dan preferensi pengguna. Ini mendorong branding dan fungsionalitas yang konsisten bagi bisnis yang memiliki aplikasi iOS asli dan keberadaan web. File harus dapat diakses melalui HTTPS tanpa pengalihan apa pun.



  - #### Is Apple's App-Site Association (ASA) the new robots.txt ?
    Nope. <br>
    File robots.txt dan Apple's App-Site Association (ASA) adalah dua hal yang berbeda,
File robots.txt digunakan untuk memberikan instruksi kepada perayap web (crawlers) mengenai bagaimana mereka harus mengakses dan mengindeks halaman-halaman situs web. Ini adalah file teks khusus yang ditempatkan di direktori root situs web dan berisi aturan-aturan yang mengatur perilaku perayap web (crawlers). File robots.txt digunakan secara umum di web untuk memberikan petunjuk pada mesin pencari dan perayap web (crawlers) lainnya tentang bagaimana mereka harus berperilaku ketika mengakses situs web.

    Sementara itu, Apple's App-Site Association (ASA) adalah kerangka teknologi yang diperkenalkan oleh Apple yang memungkinkan integrasi yang mulus antara aplikasi iOS dan situs web. ASA memungkinkan pengembang untuk menghubungkan aplikasi iOS mereka dengan situs web atau aplikasi web mereka melalui penggunaan universal links.

    Jadi, meskipun keduanya berhubungan dengan pengaturan situs web, file robots.txt berkaitan dengan instruksi untuk perayap web (crawlers), sedangkan ASA berkaitan dengan integrasi aplikasi iOS dan situs web.

 



