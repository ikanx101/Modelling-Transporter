# Modelling Transporter


*Repository* untuk permasalahan optimisasi *vehicle routing*.

# PENDAHULUAN

## Latar Belakang

Setiap hari, tim **DTA** membuat rute untuk *transporter*
mendistribusikan produk jadi ke konsumen-konsumen **PT XYZ** yang telah
melakukan *order*. Proses ini masih dilakukan secara manual. Akibatnya
proses ini memakan waktu yang cukup lama dan tidak ada kejaminan bahwa
rute yang dipilih sudah optimal atau belum. Oleh karena itu, tim **DTA**
berusaha untuk membuat model optimisasi dari permasalahan ini.

## Tujuan

Membuat model optimisasi rute *transporter* yang meminimalkan *total
cost*, yakni dengan membuat konfigurasi:

> **Order Customer mana yang harus dikirim pada tanggal berapa dan
> menggunakan armada apa.**

## Ruang Lingkup

*Business process* yang terjadi selama ini sangat kompleks, oleh karena
itu penelitian ini dibatasi pada lingkup sebagai berikut saja:

### *Business Process* yang Hendak Dikerjakan

Untuk mengirimkan produk jadi dari Gudang Ciawi dan Cibitung, tim DTA
menyewa *transporter* dengan berbagai jenis armada kendaraan.
Masing-masing kendaraan tersebut memiliki spesifikasi yang berbeda-beda,
seperti:

1.  Kapasitas maksimal kubikasi yang bisa diangkut,
2.  Kapasitas maksimal tonase yang bisa diangkut,
3.  Biaya sewa (per km). Diasumsikan biaya sewa ini nilainya tetap
    (tidak dipengaruhi oleh faktor lain seperti *habit* supir dan
    perbedaan rute yang ditempuh), dan
4.  *Loading time*.

Masing-masing armada tersebut juga memiliki keterbatasan dari segi
jumlah armada yang bisa disewa dan berapa banyak titik konsumen yang
bisa dilalui.

Konsumen memesan (melalui proses *purchase order* - PO) sejumlah produk
jadi kepada **PT XYZ**. Pada PO tersebut, kita memiliki informasi
sebagai berikut:

1.  Berapa total kubik dan tonase produk yang harus dikirim.
2.  *Range* tanggal pengiriman produk.

**PT XYZ** harus memenuhi pembelian tersebut secara langsung (tidak
boleh memecah pengiriman produk dalam satu PO menjadi beberapa kali
pengiriman). Masing-masing konsumen akan dilayani oleh gudang Ciawi atau
Cibitung sesuai dengan pembagian yang telah ditetapkan sebelumnya. Tidak
ada konsumen yang dilayani oleh keduanya.

Masing-masing konsumen memiliki keterbatas lain terkait armada yang bisa
dilalui karena lokasi mereka berbeda-beda. Ada konsumen yang berlokasi
di jalan besar sehingga armada ukuran besar bisa melewatinya dengan
aman. Namun ada beberapa konsumen yang lokasinya hanya bisa dilalui oleh
armada kecil.

# DATA TERKAIT

Berikut adalah data-data yang sudah dilakukan *pre-processing*:

## Sampel Data Terkait Order Customer

| id_customer | sales_order | order_kubikasi | order_kg | tanggal_kirim_min | tanggal_kirim_max |
|:---|:---|---:|---:|:---|:---|
| 1001 | 110407120 | 0.0455640 | 19.44608 | 2022-12-05 | 2022-12-07 |
| 1001 | 110407121 | 0.0963040 | 19.54010 | 2022-12-05 | 2022-12-07 |
| 1001 | 110407119 | 0.0451868 | 12.77226 | 2022-12-05 | 2022-12-07 |
| 1001 | 110407118 | 0.0388000 | 2.71996 | 2022-12-05 | 2022-12-07 |
| 1002 | 110407122 | 0.1327040 | 28.24724 | 2022-12-05 | 2022-12-07 |
| 1002 | 110407123 | 0.0757325 | 33.00501 | 2022-12-05 | 2022-12-07 |
| 1002 | 110407226 | 0.0714514 | 32.11601 | 2022-12-06 | 2022-12-06 |
| 1002 | 110407225 | 0.0608000 | 6.62800 | 2022-12-06 | 2022-12-06 |
| 1002 | 110407224 | 0.1621919 | 29.02057 | 2022-12-06 | 2022-12-06 |
| 1003 | 110407128 | 0.0093600 | 6.21600 | 2022-12-06 | 2022-12-06 |
| 1003 | 110407462 | 0.0515420 | 0.00000 | 2022-12-10 | 2022-12-10 |
| 1003 | 110407227 | 0.0076800 | 5.44608 | 2022-12-06 | 2022-12-06 |
| 1003 | 110407127 | 0.0639600 | 13.04376 | 2022-12-06 | 2022-12-06 |
| 1004 | 193800766 | 0.0837000 | 21.18006 | 2022-12-02 | 2022-12-03 |
| 1004 | 938600826 | 1.3281000 | 194.95710 | 2022-12-02 | 2022-12-03 |
| 1004 | 111172474 | 1.2041309 | 218.95800 | 2022-12-02 | 2022-12-03 |
| 1004 | 111172473 | 12.0024092 | 1823.70368 | 2022-12-02 | 2022-12-03 |
| 1004 | 111172774 | 0.7341184 | 133.37616 | 2022-12-08 | 2022-12-09 |
| 1004 | 938600827 | 0.6990000 | 102.60900 | 2022-12-08 | 2022-12-09 |
| 1005 | 110407249 | 1.4551366 | 699.20512 | 2022-12-06 | 2022-12-08 |

## Sampel Data Informasi Detail Customer

| id_customer |      long |      lat | supplied | max_armada |
|:------------|----------:|---------:|:---------|-----------:|
| 1001        | -6.327437 | 106.5900 | ciawi    |          2 |
| 1002        | -6.520842 | 106.8610 | ciawi    |          2 |
| 1003        | -6.330330 | 106.7904 | ciawi    |          7 |
| 1004        | -7.332009 | 112.6511 | ciawi    |          7 |
| 1005        | -6.289716 | 106.7553 | ciawi    |          2 |
| 1006        | -6.079036 | 106.6466 | ciawi    |          7 |
| 1007        | -6.185380 | 106.9011 | ciawi    |          7 |
| 1008        | -6.571628 | 106.7836 | ciawi    |          5 |
| 1009        | -6.571628 | 106.7836 | ciawi    |          5 |
| 10010       | -6.571628 | 106.7836 | ciawi    |          5 |
| 10011       | -6.803916 | 107.1294 | ciawi    |          2 |
| 10012       | -6.154793 | 106.7030 | ciawi    |          7 |
| 10013       | -6.154793 | 106.7030 | ciawi    |          7 |
| 10014       | -6.154793 | 106.7030 | ciawi    |          7 |
| 10015       | -6.700324 | 110.6684 | ciawi    |          7 |
| 10016       | -6.833826 | 110.8518 | ciawi    |          7 |
| 10017       | -6.747277 | 111.0279 | ciawi    |          7 |
| 10018       | -6.894784 | 109.6431 | ciawi    |          7 |
| 10019       | -7.022863 | 110.8928 | ciawi    |          7 |
| 10020       | -7.452775 | 109.2084 | ciawi    |          7 |

## Data Gudang

| site     | week_day_hour | week_end_hour |
|:---------|--------------:|--------------:|
| ciawi    |          67.5 |            50 |
| cibitung |          81.0 |            60 |

## Data Armada

| armada | jenis_armada | max_cap_kubikasi | max_cap_kg | cost_per_km | max_titik | loading_time |
|---:|:---|---:|---:|---:|---:|---:|
| 1 | Carry | 5 | 1500 | 0.76 | 7 | 0.6 |
| 2 | CDE | 7 | 2000 | 1.00 | 6 | 0.7 |
| 3 | CDD | 12 | 5000 | 1.27 | 5 | 1.2 |
| 4 | CDD Jumbo | 17 | 5000 | 1.56 | 5 | 1.2 |
| 5 | FUSO | 24 | 9000 | 2.23 | 5 | 1.4 |
| 6 | TRONTON | 33 | 11000 | 2.56 | 5 | 1.9 |
| 7 | BU | 45 | 20000 | 3.23 | 5 | 1.9 |
| 8 | CONT 20 | 30 | 30000 | 50.00 | 2 | 1.9 |
| 9 | CONT 40 | 65 | 40000 | 70.00 | 2 | 3.4 |

# TUJUAN OPTIMISASI

## *Decision Variables*

Model optimisasi ini menghasilkan dua *decision variables*, yakni:

1.  Tanggal kirim, dan
2.  Armada yang digunakan.

Untuk masing-masing *customer sales order*.

## *Objective Function*

Fungsi objektif pada model ini adalah:

> Meminimumkan total biaya yang timbul dari setiap keputusan *customer*
> ![i](https://latex.codecogs.com/svg.latex?i "i") yang dikirim dengan
> armada ![j](https://latex.codecogs.com/svg.latex?j "j") pada tanggal
> ![k](https://latex.codecogs.com/svg.latex?k "k") tanpa melanggar
> *constraints* yang ada.
