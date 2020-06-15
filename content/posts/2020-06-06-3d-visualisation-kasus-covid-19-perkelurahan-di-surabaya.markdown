---
title: 3D Visualisation Kasus Covid-19 Perkelurahan di Surabaya
author: idrusfachr
date: '2020-06-06'
slug: 3d-visualisation-kasus-covid-19-perkelurahan-di-surabaya
categories:
  - R
tags:
  - R
  - rayshader
  - map
lastmod: '2020-06-15T06:52:35+07:00'
description: '3D map visualsation di R menggunakan rayshader package'
show_in_homepage: yes
show_description: no
license: ''
featured_image: ''
featured_image_preview: ''
comment: yes
toc: no
autoCollapseToc: yes
math: no
---
<!--more-->
Jadi kali ini saya ingin mencoba package `rayshader` untuk 3D visualisation, berhubung kasus Covid-19 di Surabaya saat ini sangat mengkhawatirkan (zona hitam) per hari ini, maka saya coba kasus Covid-19 di Surabaya ini sebagai use case, mungkin ada manfaat yang bisa didapat nanti.

## Workflow
- Download data, download data shp Indonesia hingga level 4 (kelurahan/desa) dan data jumlah kasus terkonfirmasi covid di Surabaya level kelurahan.
- Preprocess data .shp dengan data Covid-19 Surabaya
- Membuat 2D map dengan `ggplot2` sebagai background untuk 3D graph
- Membuat grafik 3D dengan `rayshader`

## Download Data
Data shapefile atau shp Indonesia bisa didownload di [website GADM](https://gadm.org/download_country_v2.html), download hingga level 4 (kelurahan/desa). Untuk data kasus Covid-19 Surabaya busa dilihat di [website resmi pemkot](https://lawancovid-19.surabaya.go.id/).

## Preprocess data
Tahap ini agak tricky karena nama kelurahan yang terdaftar di shapefile data penulisannya beda dengan di website Covid-19 Surabaya misal `Pacarkembang` dan `Pacar Kembang`, padahal nama kelurahan ini akan digunakan untuk join id 2 data tersebut sehingga harus identik. Akhirnya, terpaksa menyamanakan penulisan nama kelurahan di data Covid-19 Surabaya dengan penulisan di shapefile secara *manual*.

Steps :

* Import .rds (shapefile) level 4 data ke dalam R
  * Filter ambil kota Surabaya saja
  * Buat data frame berisi ID dan nama kelurahan dara data .rds yang sudah diimport
* Mencari centroid data dari tiap-tiap kelurahan
* Import data kasus covid Surabaya (yang sudah diedit manual penamaan nama Kelurahannya)
* Merge data .rds dan kasus covid-19
  * Ubah .rds yang sudah diimport menjadi data frame dengan `fortify`
  * Merge data frame yang sudah didapat dengan data ID dan nama kelurahan di step 1
  * Merge data map dengan data kasus covid Surabaya (border dan centroid)
  * Replace `NA` dengan 0


### Import Data



```r
#import .rds data
library(sp)
idn_map = readRDS("/YOUR_PATH/IDN_4_sp.rds")
#filter Surabaya area only
idn_map <- idn_map[idn_map$NAME_2 == "Surabaya",] 
#create data frame of ID and Kelurahan name
idn_map_dict <- data.frame(id=sapply(slot(idn_map, "polygons"), function(x) slot(x, "ID")), 
                           Kelurahan=idn_map$NAME_4)
head(idn_map_dict)
```

### Get Centroid
Ini tahap yang critical dalam membuat visualisasi 3D. kegunaan centroid point adalah untuk 3D bar akan berada nantinya. Data shapefile adalah data batas-batas antar wilayah, sehingga titik-titiknya berada di setiap batas wilayah (border), sedangkan centroid adalah titik tengah dari wilayah tersebut. Data centroid sudah tersedia saat kita import .rds file. Cara mendapatkannya adalah:


```r
library(rgeos)
library(dplyr)
centroid <- SpatialPointsDataFrame(gCentroid(idn_map, byid=TRUE), 
                                      idn_map@data, match.ID=FALSE)
centroid_df <- data.frame(centroid)
centroid_df <- data.frame(Kelurahan=centroid_df$NAME_4, lat=centroid_df$x, long=centroid_df$y)
centroid_id_map <- left_join(centroid_df, idn_map_dict, by="Kelurahan")
centroid_id_map$Kelurahan <- as.character(centroid_id_map$Kelurahan)
head(centroid_id_map)
```

```
##       Kelurahan      lat      long     id
## 1      Asemrowo 112.7038 -7.244670 320532
## 2       Genting 112.7085 -7.238667 320531
## 3        Greges 112.6850 -7.239921 320411
## 4      Kalianak 112.6969 -7.227247 320417
## 5 Tambak Langon 112.6694 -7.230947 320407
## 6     Kandangan 112.6507 -7.246314 320560
```

### Import data kasus Covid-19 Surabaya



```r
#import data covid-19 cases at Surabaya
covid_sby <- read.csv('/YOUR_PATH/covid_sby.csv')
covid_sby <- covid_sby[,c("Kelurahan","KUMULATIF.KONFIRMASI")]
covid_sby$Kelurahan <- gsub("Kelurahan ", "", covid_sby$Kelurahan)
head(covid_sby)
```

### Merge data
Ubah `idn_map` menjadi data frame dengan function `fortify` dari `ggplot2`. Merge dengan `idn_map_dict` yang dibuat di step 1. Guananya untuk menambahkan nama kelurahan ke data hasil `fortify`-ing. 

```r
library(ggplot2)
idn_map_df <- fortify(idn_map)
idn_map_df <- merge(idn_map_df,idn_map_dict, by = "id")
idn_map_df$Kelurahan <- as.character(idn_map_df$Kelurahan)
head(idn_map_df)
```

```
##       id     long       lat order  hole piece    group    Kelurahan
## 1 320022 112.6267 -7.219698     1 FALSE     1 320022.1 Romokalisari
## 2 320022 112.6268 -7.219243     2 FALSE     1 320022.1 Romokalisari
## 3 320022 112.6269 -7.218642     3 FALSE     1 320022.1 Romokalisari
## 4 320022 112.6270 -7.218376     4 FALSE     1 320022.1 Romokalisari
## 5 320022 112.6270 -7.218278     5 FALSE     1 320022.1 Romokalisari
## 6 320022 112.6271 -7.218069     6 FALSE     1 320022.1 Romokalisari
```

Merge data map dengan data covid_sby (border map)

```r
library(dplyr)
map_data <- left_join(idn_map_df, covid_sby, by = "Kelurahan")
map_data[is.na(map_data)] <- 0
```

Merge data centroid dengan data covid_sby (centroid map)

```r
centroid_map_data <- inner_join(centroid_id_map, covid_sby, by = "Kelurahan")
centroid_map_data[is.na(centroid_map_data)] <- 0
```

## Membuat 2D Map
Membuat 2D map dengan 1ggplot2` yang akan digunakan sebagai background dari 3D map.

```r
library(ggplot2)
# Map
map_bg = ggplot(map_data, aes(long, lat, group=group, fill = KUMULATIF.KONFIRMASI)) +
  geom_polygon() + # Shape
  scale_fill_gradient(limits=range(map_data$KUMULATIF.KONFIRMASI), 
                      low="#FFF3B0", high="#E09F3E") + 
  layer(geom="path", stat="identity", position="identity", 
       mapping=aes(x=long, y=lat, group=group, 
                   color=I('#FFFFFF'))) + 
  theme(legend.position = "none", 
        axis.line=element_blank(), 
        axis.text.x=element_blank(), axis.title.x=element_blank(),
        axis.text.y=element_blank(), axis.title.y=element_blank(),
        axis.ticks=element_blank(), 
        panel.background = element_blank()) # Clean Everything
map_bg
```

<img src="/posts/2020-06-06-3d-visualisation-kasus-covid-19-perkelurahan-di-surabaya_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Save map as PNG



```r
# 2D Plot
library(grid)
library(png)
sby_map_bg <- readPNG('map_2D.png')
covid_sby_map = ggplot(centroid_map_data) + 
  annotation_custom(rasterGrob(sby_map_bg, width=unit(1,"npc"), 
                               height=unit(1,"npc")), 
                    -Inf, Inf, -Inf, Inf) + 
  xlim(xlim[1],xlim[2]) + # x-axis Mapping
  ylim(ylim[1],ylim[2]) + # y-axis Mapping
  geom_point(aes(x=lat, y=long, color=KUMULATIF.KONFIRMASI),size=2) + 
  scale_colour_gradient(name = 'Confirmed Cases', 
                        limits=range(centroid_map_data$KUMULATIF.KONFIRMASI), 
                        low="#FCB9B2", high="#B23A48") + 
  theme(axis.line=element_blank(), 
        axis.text.x=element_blank(), axis.title.x=element_blank(),
        axis.text.y=element_blank(), axis.title.y=element_blank(),
        axis.ticks=element_blank(), 
        panel.background = element_blank()) # Clean Everything
covid_sby_map
```

<img src="/posts/2020-06-06-3d-visualisation-kasus-covid-19-perkelurahan-di-surabaya_files/figure-html/unnamed-chunk-11-1.png" width="672" />

Kegunaan data centroid yang dibuat sebelumnya adalah untuk menampilkan point merah seperti di map diatas.

## Membuat 3D map

```r
# 3D Plot
library(rayshader)
filename_stl <- tempfile()
plot_gg(covid_sby_map, multicore = TRUE, width=5,height=7,scale=300,windowsize=c(1600,1000),
       zoom = 0.55, phi = 30)

render_snapshot(title_text = "Kasus Covid-19 Perkelurahan di Surabaya per 6 Juni 2020 \nidrusfachr.netlify.com")
```

<img src="/posts/2020-06-06-3d-visualisation-kasus-covid-19-perkelurahan-di-surabaya_files/figure-html/unnamed-chunk-12-1.png" width="672" />

Render Video

```r
library(rgl)
par3d(windowRect = c(0, 0, diff(xlim) * 2500, diff(ylim) * 2500))

# Render Image
#render_camera(fov = 70, zoom = 0.2, theta = 30, phi = 20)
#render_depth(focus = 0.8, focallength = 600)

# Render Video
phivechalf = 30 + 60 * 1/(1 + exp(seq(-7, 20, length.out = 180)/2))
phivecfull = c(phivechalf, rev(phivechalf))
thetavec = 0 + 45 * sin(seq(0,359,length.out = 360) * pi/180)
zoomvec = 0.45 + 0.2 * 1/(1 + exp(seq(-5, 20, length.out = 180)))
zoomvecfull = c(zoomvec, rev(zoomvec))
render_movie(filename = 'map_video', type = "custom", frames = 360,  phi = phivecfull, zoom = zoomvecfull, theta = thetavec)

rgl.close()
```
![](/images/map_video.mp4)



Refferences :
- https://www.rayshader.com/index.html
- https://towardsdatascience.com/introducing-3d-ggplots-with-rayshader-r-c61e27c6f0e9
