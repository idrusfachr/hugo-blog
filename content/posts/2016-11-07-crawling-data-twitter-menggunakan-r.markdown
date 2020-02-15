---
title: Crawling Data Twitter Menggunakan R
author: idrusfachr
date: '2016-11-07'
slug: crawling-data-twitter-menggunakan-r
categories:
  - R
tags:
  - R
  - text mining
  - Twitter
lastmod: '2020-02-09T13:52:46+07:00'
description: ''
show_in_homepage: yes
show_description: no
license: ''
featured_image: ''
featured_image_preview: ''
comment: yes
toc: yes
autoCollapseToc: yes
math: no
---
**Note :** _Artikel ini dipublikasikan pada 2016 diblog lama saya, tapi contoh code nya diperbarui dengan data terbaru saat dipindah keblog ini_ 

Pada postingan sebelumnya, saya telah sedikit berbagi tentang social media analytics dan sempat menyinggung sumber data yang digunakan dalam social media analytics diantaranya sosial media, news portal, blog dll. Kali ini, saya akan berbagi salah satu tahap awal dalam analytics yakni pengumpulan data (data harvesting).
Sesuai judulnya, postingan ini akan membahas tentang cara mendapatkan data dari Twitter sebagai bahan analisis. Dari sosial media populer seperti facebook dan instagram, twitter ini yang paling baik soal open access data. Twitter memberikan akses kepada khalayak untuk mengkonsumsi data mereka lewat API yang mereka sediakan. thanks Jack Dorsey!. API twitter sendiri terdiri 2 jenis yaitu REST API dan Streaming API. Lebih afdholnya langsung ke TKP aja gan untuk penjelasannya :D. Secara singkat bedanya, kalo REST API kita ngambil data di masa lalu hingga sekarang, kalo streaming API kita ngambil data mulai dari sekarang hingga ke depan tergantung berapa lama kita mau ambil. Nah kali ini kita akan menggali kenangan masa lalu di twitter lewat REST API.

Terima kasih kepada sohib kita Jeff (sok kenal) yang sudah membuat package R yang dia beri nama twitteR sehingga kita tinggal dengan mudah menggunakannya lewat function-function yang udah dibuat. Oke langsung saja ke tahap-tahapnya.

### 1. Gernerate access token
Generate access token API key dan API secret untuk bisa mengakses twitter API. Cara membuatnya langsung saja ke tulisan [ini](https://www.nurandi.id/blog/twitter-authentication-dengan-roauth-dan-httr/), inshaa Allah cukup jelas.

### 2. Install `twitteR` package
Ada 2 cara untuk install package di R, pertama dengan build in function di R install.package atau melalui github repository menggunakan package devtools
install package dari CRAN
Ini cara paling praktis untuk install package ke R, caranya sangat mudah tinggal tulis fungsi install.packages("package_name") package akan otomatis terinstalll beserta dependensinya.

install package melalui Github menggunakan library `devtools`
Terkadang ada package yang belum terdaftar di CRAN sehingga belum bisa kita install menggunakan `install.packages`, tapi kita tetap bisa menginstall package tersebut melalui repository development-nya dengan bantuan package devtools.Berikut adalah syntax untuk install  `twitteR` package dengan dua cara diatas:


```r
#install twitteR package dari CRAN
install.packages("twitteR")

#install twitteR package dari github repository
library(devtools)
install_github("twitteR", username="geoffjentry")  
```

### 4. Aktifkan package dan twitter authentication
Setelah `twitteR` package terinstall, kita tinggal mengaktifkan package dengan function `library(twitteR)`, setelah itu kita perlu melakukan proses autentifikasi menggunakan credential yang kita dapat di step 1 untuk bisa mengakses API twitter. Di package twitteR sendiri telah ada fungsi untuk menjalankan autentifikasi tanpa perlu menggunakan bantuan package lain seperti `httr`.



```r
library(twitteR) #aktifkan package twitteR

api_key = "xxxxxxxxxxxxQylT" #change with yours
api_secret = "KZCxxxxxxxxxxxxxxxxxxxxxxxiQy" #change with yours
token = "38xxxxxxxxxxxxxxxxxxxxxxxnsp" #change with yours
token_secret = "IYxxxxxxxxxxxxxxxxxxxxxxxxxxORkf7" #change with yours
setup_twitter_oauth(api_key, api_secret, token, token_secret) #fungsi untuk autentifikasi
```

Setelah credential telah diaktifkan, sekarang kita sudah bisa mengakses data dari API twitter.

### 5. Crawl data dari twitter
Untuk menggenerate data kita menggunakan fungsi `searchTwitter`, fungsi ini mempunyai beberapa parameter, yang terpenting adalah keyword yakni keyword apa yang ingin kita download dari twitter. Kita juga bisa menambah parameter n yakni jumlah tweet yang ingin kita ambil serta mengatur waktu dari rentang kapan data yang mau kita ambil (tentunya dari masa lalu hingga sekarang). Berikut contohnya.



```r
#ambil 100 tweet berbahasa indonesia yang mengandung keyword corona dari tanggal 7 hingga 9 Februari 2020
tweets = searchTwitter("corona", n = 1000, since = "2020-02-07", until = "2020-02-09", lang = 'id')
```
`searchTwitter` akan mengembalikan data bertipe list, jika ingin melakukan analisis akan lebih mudah jika kita menggunakan data bertipe data frame, untung saja di `twitteR` telah ada build in function untuk merubah data bertipe list ke data frame menggunakan fungsi `twListToDF`.


```r
#merubah data list ke data frame
tweets_df = twListToDF(tweets)
#data telah berbentuk dataframe dan siap dianalisa
names(tweets_df) #untuk melihat kolom apa saja yang dimiliki oleh data
```

```
##  [1] "text"          "favorited"     "favoriteCount" "replyToSN"    
##  [5] "created"       "truncated"     "replyToSID"    "id"           
##  [9] "replyToUID"    "statusSource"  "screenName"    "retweetCount" 
## [13] "isRetweet"     "retweeted"     "longitude"     "latitude"
```
output : `searchTwitter` memberikan data dengan 16 kolom sebagai diatas.
contoh tweet yang kita dapat adalah.


```r
head(tweets_df)
```

```
##                                                                                                                                                  text
## 1                                                          RT @MrZiel: @datteamejanai @cheesyfloat Kejang kejang karna corona paling dikira kesurupan
## 2 RT @dr_koko28: CORONA VIRUS\n\n- 722 people dead\n- 5900 in serious condition\n- Over 34,000 infections\n- Cases confirmed in 25 countries.\n\nAdv…
## 3     RT @msaid_didu: Semoga Bpk paham bhwa \nPertumbuhan 5 % itu sampai Desember 2019.\nCorona baru terjadi Januari 2020 dan itu di China.\nMari ki…
## 4        RT @fahiraidris: Baru-baru ini #Singapura meningkatkan status ancaman virus #Corona di negaranya. Kemlu pun mengingatkan wisatawan Indonesi…
## 5                                              RT @CNNIndonesia: Korban Meninggal Akibat Virus Corona di China Jadi 803 Orang https://t.co/pdMvH2av9a
## 6     RT @SisiLusipara: Pedulikah Ia?\n\nPM Singapura Unggah Imbauan Waspada Corona via Youtube, Bagaimana Jokowi?\nDownload Link || https://t.co/so…
##   favorited favoriteCount replyToSN             created truncated replyToSID
## 1     FALSE             0      <NA> 2020-02-08 23:59:57     FALSE       <NA>
## 2     FALSE             0      <NA> 2020-02-08 23:59:55     FALSE       <NA>
## 3     FALSE             0      <NA> 2020-02-08 23:59:42     FALSE       <NA>
## 4     FALSE             0      <NA> 2020-02-08 23:59:42     FALSE       <NA>
## 5     FALSE             0      <NA> 2020-02-08 23:59:39     FALSE       <NA>
## 6     FALSE             0      <NA> 2020-02-08 23:59:38     FALSE       <NA>
##                    id replyToUID
## 1 1226294626266439681       <NA>
## 2 1226294620373405696       <NA>
## 3 1226294566178848769       <NA>
## 4 1226294565889462272       <NA>
## 5 1226294551771398144       <NA>
## 6 1226294548936052736       <NA>
##                                                                           statusSource
## 1 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
## 2 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
## 3 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
## 4 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
## 5 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
## 6 <a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>
##        screenName retweetCount isRetweet retweeted longitude latitude
## 1   serendipitwty          825      TRUE     FALSE        NA       NA
## 2     GilangGum29          342      TRUE     FALSE        NA       NA
## 3  Voirmountagnes         2363      TRUE     FALSE        NA       NA
## 4       AbolichdZ          421      TRUE     FALSE        NA       NA
## 5 NovrikoSulistio          228      TRUE     FALSE        NA       NA
## 6   anitamarlian2           41      TRUE     FALSE        NA       NA
```

Parameter diatas seperti keyword, n, date range dapat kita ubah sesuai dengan keinginan kita, bisa juga kita menambahkan parameter lainnya sesuai dengan yang ada di dokumentasi `twitteR` package. Tentu saja, REST API ini punya limit dalam memberikan data kepada client baik rentang waktu yang bisa diakses maupun jumlah tweet yang diberikan, ada baiknya untuk membaca dulu dokumentasi twitter API sebelum mengaksesnya baik melalui R, python atau bahasa lainnya. Sekian, semoga bermanfaat.
<!--more-->
