---
layout: post
title:  " How many threads do I need for my cross-stitch?"
---

A while ago I bought a [PDF cross-stitch pattern from Etsy](https://www.etsy.com/uk/listing/684772255/chef-guy-cross-stitch-pattern-food-cross). These _usually_ include stitch counts for each color so you know how much thread you need to buy to complete the pattern. This one didn't, and the seller didn't resond to an email I sent asking for them, so I did a bit of investigative work to calculate stitch counts myself. 

Opening the pattern in a PDF reader, I can see each of the individual stitches is an image, meaning I should be able to pull out the images and use that to calculate stitch count.

Let's `pdfimages` (part of the `poppler-utils` package) to extract all the images in the PDF into a new folder:

```sh
pdfimages -all WelcomeToFlavortownPDFPatternandInstructions.pdf images/
```

On inspection, the exact same image (down to the image hash) is used each time a stitch is repeated, so let's generate the MD5sum of all the images in the folder:

```sh
md5sum images/* > sums.chk
```

With that, I can get the number of items with each hash:

```sh
$ cut -d " " -f1 sums.chk | sort | uniq -c | sort -k2 | tee counts.txt
      1 0097d321200073e1f8c6d57f131bd108
    598 065e60784131f4420438dda42fc490c8
      2 17ba976df99c5aadd8d09048b96bf891
   1525 26249a2e983019854d1a7f8e6a6bb726
      1 29a72322ead2c10c352bb327f5e43601
      3 33fa15fad3d06c18270492e2c4b73c48
      1 38373840a1b499e8678e1128916009c0
   3645 3a3c5f73ebdcdb632d3a50613114bbcd
   3140 5e66ae8244fb7911f67c62d68719ca0b
      1 5f4be987c4163df153bb366a29505378
      2 737035addba170eb594eaf8b18108638
    616 87385cb1dbbb1cece935ca3fae49c676
     60 875567de6246335b51c8deb95a80f542
    452 9902a42b508e81581d15e85faedd3e10
    296 9afdb9a8329914220e213442ba746543
     20 a2518c48cd8a36330319d2749c40967a
      1 c5dc3a9eb28454a76485ab65d734a7ca
     19 c8c0af993d6a6cd5baa825119b0e1603
      1 d272df58e9fa7976e677f55084579eb3
   1704 df571ce5ec5b972df84e64bf64919b31
     13 e1145e44e6f94ee5655c59905b2f7b01
    764 e6d6e677daaebf34e12a27ed23b361e8
     35 f8334acf776b0600708b4091c9e0274a
```

Using `awk`, I'll pull out the first image path for each hash to get an example image for that hash:

```sh
$ awk -F' ' '!seen[$1]++'  sums.chk | sort -k1 | tee examples.txt
0097d321200073e1f8c6d57f131bd108  images/-12896.jpg
065e60784131f4420438dda42fc490c8  images/-1069.jpg
17ba976df99c5aadd8d09048b96bf891  images/-002.png
26249a2e983019854d1a7f8e6a6bb726  images/-033.jpg
29a72322ead2c10c352bb327f5e43601  images/-007.png
33fa15fad3d06c18270492e2c4b73c48  images/-000.jpg
38373840a1b499e8678e1128916009c0  images/-006.png
3a3c5f73ebdcdb632d3a50613114bbcd  images/-008.jpg
5e66ae8244fb7911f67c62d68719ca0b  images/-060.jpg
5f4be987c4163df153bb366a29505378  images/-12898.jpg
737035addba170eb594eaf8b18108638  images/-001.png
87385cb1dbbb1cece935ca3fae49c676  images/-10002.jpg
875567de6246335b51c8deb95a80f542  images/-013.jpg
9902a42b508e81581d15e85faedd3e10  images/-10243.jpg
9afdb9a8329914220e213442ba746543  images/-11628.jpg
a2518c48cd8a36330319d2749c40967a  images/-1002.jpg
c5dc3a9eb28454a76485ab65d734a7ca  images/-005.jpg
c8c0af993d6a6cd5baa825119b0e1603  images/-11629.jpg
d272df58e9fa7976e677f55084579eb3  images/-12897.jpg
df571ce5ec5b972df84e64bf64919b31  images/-1000.jpg
e1145e44e6f94ee5655c59905b2f7b01  images/-11621.jpg
e6d6e677daaebf34e12a27ed23b361e8  images/-10071.jpg
f8334acf776b0600708b4091c9e0274a  images/-10860.jpg
```

Joining these two files gives us the count of each image with an example for each:

```sh
$ join -1 1 -2 2 -o 2.1,1.2 examples.txt counts.txt | sort -k2 -V | column -t
3     images/-000.jpg
2     images/-001.png
2     images/-002.png
1     images/-005.jpg
1     images/-006.png
1     images/-007.png
3645  images/-008.jpg
60    images/-013.jpg
1525  images/-033.jpg
3140  images/-060.jpg
1704  images/-1000.jpg
20    images/-1002.jpg
598   images/-1069.jpg
616   images/-10002.jpg
764   images/-10071.jpg
452   images/-10243.jpg
35    images/-10860.jpg
13    images/-11621.jpg
296   images/-11628.jpg
19    images/-11629.jpg
1     images/-12896.jpg
1     images/-12897.jpg
1     images/-12898.jpg
```

To make my life a bit easier, let's pull just those example images into a folder:

```sh
$ cp `cut -d' ' -f3 examples.txt` example_images/
```

Opening `example_images/` in a file browser and comparing to the colour reference used in the pattern, it turns out the following images are stitches: 

```
3645  images/-008.jpg
60    images/-013.jpg
1525  images/-033.jpg
3140  images/-060.jpg
1704  images/-1000.jpg
20    images/-1002.jpg
598   images/-1069.jpg
616   images/-10002.jpg
764   images/-10071.jpg
452   images/-10243.jpg
35    images/-10860.jpg
13    images/-11621.jpg
296   images/-11628.jpg
19    images/-11629.jpg
```

I can work out how many skeins I'll need of each colour using [this table](https://www.mismatch.co.uk/cross.htm#floss_amt). At 2 strands of floss on 18 count Aida, one skein can make 2295 stitches; therefore, I'll need one skein of every colour except the stitches represented by `-008.jpg` and `-060.jpg`, of which I'll need two skeins.

This was a fun little trip into applying tech to one of my analogue hobbies :) 
