---
layout: post
title: "Carving JPEGs from SOI to EOI"
category: essays
subcategory: 'digital-forensics'
permalink: 'caring-jpegs-from-soi-to-eoi'
---

## Table of Contents
* [Tools required](#tools-required)
* [Download a forensic image](#donwload-a-forensic-image)
* [Find possible JPEG SOI markers and their off-sets](#find-possible-jpeg-soi-markers-and-their-offsets)
* [Find a corresponding EOI marker and off-set](#find-a-corrresponding-eoi-marker-and-offset)
* [Calculate the size](#calculate-the-size)
* [Extract & verify](#extract-verify)
* [Other considerations](#other-considerations)
* [References](#references)

Data carving is the process of identifying & extracting forensic artifacts from digital evidence using file signatures. For example, files using the Joint Photographic Experts Group (JPEG) compression standard begin with the header `0xFF 0xD8` and end with the footer `0xFF 0xD9`. These are also known as Start-of-Image (SOI) and End-of-Image (EOI) markers respectively.

The following tutorial will demonstrate how these markers can be used to carve a JPEG-based file from a provided forensic image.

### Tools required
* `wget`, `unzip`, `file`,`md5sum`, `sha256sum`
* `xxd`, `grep`,`cut`,`tr`
* `echo`,`bc`,`dd`

## Download a forensic image
To start, download "image #11" provided by the National Institute of Standards and Technology (NIST) located [here](http://dftt.sourceforge.net/test11/index.html). Then, unzip it and move into its main directory. Next, rename the image, verify its file-type, and record its MD5 & SHA256 hash values. I personally recommend renaming the image for simplicity. Doing so does not have an impact on the integrity of our evidence (try hashing the image before and after renaming it to see my point).  
```bash
wget http://prdownloads.sourceforge.net/dftt/11-carve-fat.zip
unzip 11-carve-fat.zip
cd 11-carve-fat/
mv 11-carve-fat.dd raw.dd	# rename image for simplicity
file raw.dd 			# verify file-type (DOS/MBR boot sector)
md5sum raw.dd			# get MD5 hash of image
sha256sum raw.dd		# get SHA256 hash of image
```
Running both MD5 and SHA256 algorithms at the beginning of our investigation will help identify the authenticity of evidence as its processed. It also helps reduce the probability of a hash collision (where two different sources of input generate the same output). Forensically speaking, evidence should not be trusted if another image is able to produce the same hash value as the one you are analyzing. Otherwise, how would you know if it's been modified?

The downloaded image should produce the hashes below.  
```yaml
MD5: 0069813c892a462f88dc6d376624f7d9
SHA256: 83585232e908529286f1ff04c43b4d858604875c733183a9e3b44a07ff818d26
```

## Find possible JPEG SOI markers and their off-sets
We begin our search for JPEG markers by using `xxd` and `grep`. `xxd` generates a text-based hexdump from provided input. In our case, the downloaded image will be the input for `xxd` here. `grep` (Globally search for a Regular Expression and Print) will be used to find the markers we are looking for in said hexdump. By the way, `-g1` tells `xxd` to display its output in single, hexadecimal groupings (pairs).
```bash
xxd -g1 raw.dd | grep 'ff d8'
```
As you may find, using only `0xFF 0xD9` as our only search criteria will produce a lot of false positives. So, to help narrow down the possibilities, we will include `0xFF 0xE0` which represents the JPEG File Interchange Format (JFIF) standard. As our friends at StackOverflow explained [here](https://stackoverflow.com/questions/1427623/are-all-jpeg-files-jfif), JPEG is the algorithim used for to compress/encode data within a file while JFIF is one of the most commonly used JPEG *file formats*.
```bash
xxd -g1 raw.dd | grep 'ff d8 ff e0'
```
```bash
00820a00: ff d8 ff e0 00 10 4a 46 49 46 00 01 01 01 01 2c  ......JFIF.....,
009a0a00: ff d8 ff e0 00 10 4a 46 49 46 00 01 01 01 01 2c  ......JFIF.....,
00a14a00: ff d8 ff e0 00 10 4a 46 49 46 00 01 02 01 00 48  ......JFIF.....H
00a14b80: 00 00 00 01 00 00 00 48 00 00 00 01 ff d8 ff e0  .......H........
00a15b60: 5f 00 18 00 01 ff d8 ff e0 00 10 4a 46 49 46 00  _..........JFIF.
```
The output here suggests we have five artifacts (JPEGs) to recover. Yet, our focus from this point forward will only be the second finding. Said finding begins with `009a0a00` - this is called an *off-set*. Since we only need this bit of information and it must be upper-case (I'll explain a moment), we will re-run the previous command sentence while cutting-out & re-formatting the first eight characters.
```bash
xxd -g1 raw.dd | grep 'ff d8 ff e0' | cut -c 1-8 | tr a-z A-Z
```
```bash
00820A00
009A0A00
00A14A00
00A14B80
00A15B60
```
Great! Now, our next step will be to take the off-set we are focusing on and feed it into a tool called `bc`. We will use this tool to identify approximately *how many bytes deep* our off-set represents and generally where our JPEG should begin. Yet, to execute this process, we're going to use `echo` and include another parameter `ibase=16;`. To explain, we're telling `bc` the base of our input is hexadecimal. As a comparison, we would specify `ibase=10;` if our input was decimal. Lastly, `bc` requires input values (`ibase` is a variable in this context) be all upper-case letters so it does not confuse them with syscalls to other utilities.
```bash
echo 'ibase=16;009A0A00' | bc
```
```bash
10095104 # we must go 10095104 bytes deep to find our target artifact
```

## Find a corresponding EOI marker and off-set
Using the SOI we found as our *seek from* (or start) point, we will now look for an EOI. Yet, since `0xFF 0xD9` will also generate a lot of false positives like before, we will stop `grep` after the first match.
```bash
xxd -g1 -s 10095104 raw.dd | grep -m1 'ff d9'
```
```bash
009a7eb0: 9e94 d2bb 4f07 bd46 7b7d 2bff d900 0000  ....O..F{}+.....
```
As before, we can run an extended command sentence so only the needed off-set is displayed as output.
```bash
xxd -g1 -s 10095104 raw.dd | grep -m1 'ff d9' | cut -c 1-8 | tr a-z A-Z
```
```bash
009A7EB0
```
Then, we will run the value through `bc`. Although, it is important to keep in mind the off-set we currently have for our EOI is not necessarily our true EOI. The off-set represents the line where the EOI resides. Therefore, we must count the number of remaining characters in the 16 hexadecimal character line until we reach `0xFF 0xD9`. In this instance, we need to add 13 more bytes to whatever `bc` says.
```bash
echo 'ibase=16;009A7EB0' | bc
```
```bash
10124976 # + 13 bytes means our EOI is 10124989 bytes deep
```

## Calculate the size
We can now calculate how big our JPEG will be by subtracting the EOI from the SOI.
```bash
echo '10124989-10095104' | bc
```
```bash
29885 # nearly 30 kilobytes
```

## Extract & verify
Finally, using the forensic image downloaded earlier, we can feed all of the clues we collected during our analysis into `dd`. In this step, we are asking `dd` to carve out an artifact from `raw.dd`, skipping the first `10095104` bytes and ending `29885` bytes later. `bs=1` will be our block-size since we are precisely cutting out `29885` individual blocks of data.
```bash
dd if=raw.dd of=artifact.jpg bs=1 skip=10095104 count=29885
md5sum artifact.jpg
```
![artifact.jpg]({{ site.url }}{{ site.baseurl }}/_assets/artifact.jpg)<br>

This manually carved JPEG should produce the hash below (the same as provided online).
```yaml
MD5: 37a49f97ed279832cd4f7bd002c826a2
```
Again, to prove nothing was done to our original image file, we should be able to achieve the same MD5/SHA256 hash values as before.
```bash
md5sum raw.dd
sha256sum raw.dd
```
```yaml
MD5: 0069813c892a462f88dc6d376624f7d9
SHA256: 83585232e908529286f1ff04c43b4d858604875c733183a9e3b44a07ff818d26
```

## Other considerations
The steps covered within this post are only basic data carving techniques. I recommend trying more than one EOI off-set if you are unable to successfully carve an expected image the first time around. For example, I personally was only able to extract a second image after using the third or fourth off-set with `grep 'ff d9'`. Advanced data carving techniques may also be required when addressing fragmented files. Lastly, it is worth researching & including *segment markers* during analysis. These describe the portions between a file's SOI and EOI.

### References
* [SANS Institute Reading Room: Data Carving Concepts](https://www.sans.org/reading-room/whitepapers/forensics/data-carving-concepts-32969)
* [Gary Kessler: File Signature Table](https://www.garykessler.net/library/file_sigs.html)
* [DCube Software Technologies: JPEG File Layout and Format](http://vip.sugovica.hu/Sardi/kepnezo/JPEG%20File%20Layout%20and%20Format.htm)
* [ITU T.81: Table B.1 - (JPEG) Marker code assignments](https://www.w3.org/Graphics/JPEG/itu-t81.pdf)
* [DSP/IC Lab: JPEG Marker Definitions](http://lad.dsc.ufcg.edu.br/multimidia/jpegmarker.pdf)
* [NIST Digital (Computer) Forensics Tool Testing Images](http://dftt.sourceforge.net/test11/index.html)
* [SCX030C064: Carving with DD](http://scx030c064.blogspot.com/2013/02/carving-with-dd.html?m=1)
