---
title: Blogging dengan R Blogdown, Hugo dan Netlify
author: idrusfachr
date: '2020-02-15'
slug: blogging-dengan-r-blogdown-hugo-dan-netlify
categories:
  - R
tags:
  - blog
  - blogdown
  - netlify
  - github
lastmod: '2020-02-15T16:58:18+07:00'
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

Akhrinya saya menengok lagi blog pribadi saya yang sudah lama terbengkalai. Sebagai informasi, blog lama saya dibuat menggunakan blogspot dan memang sudah dari awal isinya seputar data dan lebih techincal. Sekarang saya menggunakan [Hugo](https://gohugo.io/), R Blogdown dan Netlify, alasannya karena lebih mudah untuk blog pribadi dengan tema techincal dan tentu saja karena saya banyak menggunakan dan membahas R di blog saya. Pada post ini saya akan berbagi bagaimana membuat blog dengan R menggunakan blogdown hingga hosting di Netlify.

**Disclaimer** : Jika anda ingin membuat blog yang bukan bertema technical dan tidak banyak membutuhkan snippet code, grafik dll, Blogspot, Wordpress dan platform serupa sudah sangat cukup untuk kebutuhan anda. Pun, jika anda berniat membuat technical blog dan anda butuh kepraktisan dan fitur lengkap, medium.com juga lebih dari cukup. Cara yang akan saya bahas ini bisa jadi lebih ribet dan memusingkan untuk yang belum terbiasa. Tapi, kalo anda butuh technical blog yang lebih fleksibel, personal dan anda orangnya semangat belajar hal-hal baru, maka tidak ada salahnya membaca sharing saya ini.

Dengan menggunakan R blogdown, berarti anda harus setuju blog anda akan menggunakan Hugo. Sebagai informasi Hugo adalah salah satu [static site generator](https://wsvincent.com/what-is-a-static-site-generator/). Selain Hugo ada static site generator lain seperti [Jekyll](https://jekyllrb.com/), [Gatsby](gatsbyjs.org/), dll. 

## Pre-Request
Selamat datang orang yang semangat belajarnya tinggi. Untuk membuat blog sesuai post ini, maka anda harus punya/melakukan hal-hal berikut.
* 1. Membaca [blogdown online book](https://bookdown.org/yihui/blogdown/) dari pembuatnya langsung. Setidaknya chapter 1 dulu.
* 2. [R Studio](https://rstudio.com/products/rstudio/) terinstall di device anda. Apa? belum pake R studio? Anda WAJIB pakai demi kebaikan & kemudahan anda sendiri. Ga cuma untuk keperluan blogging tapi pemakaian R sehari-hari juga.
* 3. [Github](http://github.com/) account. Harus banget pake github? Yes, you should!. Github memang menyebalkan untuk orang non-hardcore programmer kayak saya. Saya benci belajar git, walaupun tahu manfaatnya. Tapi saya harus belajar github, dan anda juga!. bisa baca disini [Happy Git and GitHub for the useR](httpshttps://happygitwithr.com/)


## Membuat Blog
Saya akan bagi stepnya menjadi 3 bagian tentang apa yang harus anda lakukan di :
* R studio
* Terminal (command prompt kao di windows)
* Github
* Netlify

### 1. Di Github
Hal pertama yang dilakukan adalah membuka github dan masuk ke akun anda. 
* Buat new repository

  Buat repo baru beri nama apapun, misalnya `mypersonal-blog`. Centang initialize with README, beri deskripsi singkat kalo anda mau. Biarkan `add .gitignore` dan `add license` sebagai **None**. Seperti contoh dibawah ini:
  ![](/images/create_repo_blog.png)
* Buka repo yang baru anda buat dan klik clone this repo
Di sebelah kanan atas ada button hijau dengan tulisan `clone this repo`. Pilih **clone with https** lalu copy link yang ada. Link yg ter-copy harusnya : https://github.com/yourusername/repo-name.git. Misal:
![](/images/clone_repo.png)

### 2. Di Terminal
Buka terminal di laptop anda, jika menggunakan windows search cmd dan klik command prompt. Langkah ini bertujuan mendownload remote repository yang baru anda buat di Github ke device anda dan akan saling sinkron.
* Buat folder baru di device

  Sebelum menggunakan terminal terlebih dulu buatlah folder baru yang akan digunakan menaruh file-file blog anda nanti. Misal buat folder baru di dalam folder C:/Documents bernama personal-blog.
* Buka terminal
Di terminal arahkan ke folder baru yang sudah dibuat dengan cara ketik : cd "/Documents/personal-blog/" enter. Skrg terminal anda aktif di dalam folder personal-blog. Setelah itu ketik git clone [Paste link repo yang diperoleh di step 1] ke dalam terminal. 
```{}
git clone https://github.com/yourusername/repo-name.git
```
klik enter, maka file-file di repo github akan terdownload ke dalam folder yang sudah anda buat.


Selesai urusan perterminalan.

### 3. Di R Studio
Sampai di bagian yang mungkin paling familiar dengan anda yaitu R Studio. Di Rstudio ada 3 step yang harus dilakukan.

#### 3.1 Install `blogdown`



<!--more-->
