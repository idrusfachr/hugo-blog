---
title: What is Machine Learning?
author: idrusfachr
date: '2016-12-31'
slug: what-is-machine-learning
categories:
  - Opinion
tags:
  - machine learning
lastmod: '2020-02-10T00:10:21+07:00'
description: 'apa itu machine learning dan contoh penggunaannya'
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
![](/images/dilbert_ml.png)

Machine learning (ML) menjadi salah satu _hot topic_ dalam dunia riset bidang teknologi dan sains dalam satu dekade terakhir. Lalu apa sebenarnya ML itu?

Tanpa kita sadari, sebenarnya setiap hari kita telah bersentuhan atau menggunakan aplikasi dari ML dalam aktivitas sehari-hari. Saat kita membuka facebook, news feed di krolonologi kita telah difilter untuk menampilkan _news feed_ yang mungkin kita suka saja, saat membuka youtube kita akan melihat suggestion video yang sesuai dengan kegemaran kita, dan saat membeli barang di _e-commerce_ atau membaca artikel di sebuah situs berita, kita akan di berikan rekomendasi barang/artikel yang berdekatan dengan interest kita, juga folder spam di email kita yang terisi sendiri tanpa perlu kita filter. Itu adalah beberapa contoh sederhana penerapan ML yang memudahkan kehidupan kita sehari-hari.

## Definition
Sama halnya dengan data scientist, definisi ML sendiri ada banyak versi karena belum ada definisi baku yang di disepakati. Tetapi, definisi yang terkenal adalah pendapat Arthur Samuel yang mengatakan bahwa.

> _**Machine learning is field of study that gives computer ability to learn from data without being  explicitly programmed.**_ (Arthur Samuel, 1959)

ya, perbedaan ML dengan progamming biasa adalah kita memberikan kemampuan kepada komputer untuk belajar sendiri dari data sehingga dia akan tahu tindakan apa yang seharusnya dilakukan berdasarkan data yang kita berikan tersebut, tanpa perlu menghandle segala kemungkinan sebagaimana programming biasa/tradisional lakukan.

> _**Software apps are programmed, intelligent apps are trained (with data).**_
(Carlos Guestrin)

Kevin P. Murphy dalam bukunya [Machine learning : A Probabilistic Perspective](https://www.amazon.com/dp/0262018020/ref=rdr_ext_tmb) memberikan definisi yang lebih "statistika" soal ML. Dia mengatakan bahwa ML adalah
> _**A set of method that can automatically detects pattern in data, then use the uncovered pattern to predict future data, or perform other kinds of decision making under uncertainty (such as planning how to make collect to data).**_ (Kevin P. Murphy)

## Task
ML adalah subfield dari artifficial intelligence yang memadukan pendekatan computing dan juga matematics/statistics (liniar algebra, probabilistics, etc) sehingga jika ingin belajar ML harus punya bekal pengetahuan programming dan juga statistik untuk bisa menguasainya. Dalam hal permasalahan/kaskusnya, ML dibagi menjadi 3 bagian, yaitu:

### Supervised learning
Pada supervised learning kita memberikan input data yang output atau responnya diketahui. Kemudian algoritma ML akan belajar dari data tersebut sehingga mampu memrediksi output atau respon dari data baru yang diberikan. Berdasarkan responnya supervised learning dibagi menjadi 2, yaitu:

* **Classification**

  Jika responnya adalah kategorik, maka kasusnya dinamakan classification. Misalnya adalah kita ingin memprediksi seseorang terkena kanker atau tidak, responnya adalah ya dan tidak. Algoritma yang digunakan untuk classification adalah logistic regression, support vector machine, decision tree, random forest, naive bayes, neural network, dsb

* **Regression** 

  Kasus regresi adalah jika output atau responnya bersifat continue. Contoh kasusnya adalah prediksi harga saham, prediksi harga rumah, prediksi curah hujan dsb. Contoh algoritmanya adalah linear regression, polynomial regression, classification and regression tree (CART), random forest regression dsb.

### Unsupervised Learning
Jika supervised learning memetakan input x ke output y, unsupervised learning tidak memiliki output sehingga yang dilakukan oleh algo-algo unsupervised learning adalah menemukan interesting pattern dari data yang ada. Contoh kasusnya adalah segmentasi customer dari e-commerce berdasarkan behaviour mereka sehingga tim marketing bisa memberikan treatment yang tepat sasaran sesuai dengan behaviour user.
Contoh algoritmanya adalah k-means clustering, hierarchical clustering, mixture models dll. Selain clustering, pendekatan lain yang termasuk unsupervised adalah anomali detection dan dimensional reduction.

### Reinforcement Learning
Dibandingkan dua kasus sebelumnya, RL bisa dikatakan lebih rumit tetapi lebih mendekati kasus yang terjadi dalam kehidupan nyata di dunia ini. Definisi RL adalah permasalahan untuk menemukan rule yang mengoptimumkan reward dalam suatu kondisi yang mana di kondisi itu terdapat reward dan punishment saat melakukan hal-hal tertentu.

Agak susah sebenarnya menjelaskan apa itu RL, untuk lebih mudahnya analogikan saja seperti ini, kita ingin melatih kucing peliharaan kita agar tidak buang air sembarangan, kita tidak mungkin memberi tahu kepada kucing tersebut dengan bahasa kita karena dia tidak akan mengerti, maka cara yang bisa kita lakukan adalah memberikan reward misal makanan jika kucing kita buang air ditempat yang seharusnya dan juga punishment jika dia melakukan hal sebaliknya. Dengan sendirinya lama-kelamaan kucing kita akan mengerti mana yang harus dilakukan dan mana yang tidak boleh dilakukan berdasarkan reward dan punishment yang ada. Kita juga bisa menerapkan hal tersebut ke komputer. Contoh algoritma RL adalah _markov decision process_ (MDP), _multi arm bandit_ (MAB) dan A/B _testing_.

## Future
Sebagai bagian dari rumpun _ilmu artificial intellegence_ (AI), ML masih akan banyak dikembangkan baik di dunia riset maupun industri seiring dengan berkembangnya riset dan aplikasi AI saat ini. Topic yang sedang hot saat ini diantaranya adalah _self driving car_, _deep learning_, _computer vision_ dan NLP.




Referensi :
- http://reinforcementlearning.ai-depot.com/
- https://medium.com/@ageitgey/machine-learning-is-fun-80ea3ec3c471#.xb48zwq8w
- www.dilbert.com (meme)
<!--more-->
