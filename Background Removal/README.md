
# Background Removal

Program ini membaca gambar asli, mengkonversikannya ke dalam skala abu-abu, mencari kontur, membuat masker, dan menghapus latar belakang gambar. Hasilnya ditampilkan menggunakan matplotlib dan juga disimpan dalam file terpisah. 
Program menggunakan Python dan OpenCV untuk menghapus latar belakang dari sebuah gambar.

1. Import Library
    import cv2
    import numpy as np
    import matplotlib.pyplot as plt
   Pada baris ini, kita mengimpor library yang diperlukan untuk program ini, yaitu:
   - cv2: Library OpenCV untuk pengolahan gambar dan komputer visi.
   - numpy (np): Library untuk operasi matriks dan array multidimensi
   - matplotlib.pyplot (plt): Library untuk membuat plot dan visualisasi data. 

2. Membaca dan menampilkan gambar asli
    img = cv2.imread('Original Image.jpg').astype(np.uint8)
    print(img.shape)
    plt.imshow(img, cmap='gray')
   Pada baris ini, gambar asli dibaca menggunakan fungsi cv2.imread() dengan memasukkan path file gambar ke parameter fungsi. Kemudian, gambar tersebut ditampilkan menggunakan plt.imshow() dengan memasukkan gambar ke parameter fungsi.

3. Konversi warna gambar
    image = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    plt.imshow(image, cmap='gray')
  Pada baris ini, gambar asli dikonversi dari format warna BGR (Blue-Green-Red) menjadi RGB (Red-Green-Blue) menggunakan fungsi cv2.cvtColor(). Konversi ini diperlukan karena format warna RGB lebih umum digunakan dalam tampilan gambar. Gambar yang sudah dikonversi ditampilkan kembali.

4. Konversi ke citra keabuan (grayscale)
    gray = (cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)).astype(np.uint8)
  Pada baris ini, gambar yang sudah dikonversi warna ke RGB diubah menjadi citra keabuan (grayscale) menggunakan fungsi cv2.cvtColor() dengan mode konversi cv2.COLOR_BGR2GRAY. Kemudian, tipe data gambar diubah menjadi np.uint8.

5. Pengolahan thresholding
    ret, thresh = cv2.threshold(gray, int(image[:, :, 0].mean()), int(image[:, :, 1].mean()), 0)
  Pada baris ini, dilakukan thresholding pada citra keabuan menggunakan fungsi cv2.threshold(). Fungsi ini akan menghasilkan dua output, yaitu ret (nilai threshold yang dihitung) dan thresh (gambar hasil thresholding). Parameter-parameter yang digunakan dalam fungsi ini adalah sebagai berikut:
  - gray: Citra keabuan yang akan di-threshold.
  - int(image[:, :, 0].mean()): Nilai threshold bawah yang dihitung berdasarkan rata-rata nilai piksel pada saluran warna biru.
  - int(image[:, :, 1].mean()): Nilai threshold atas yang dihitung berdasarkan rata-rata nilai piksel pada saluran warna hijau.
  - 0: Nilai piksel yang ditetapkan jika piksel dalam citra tidak melebihi nilai threshold.

6. Deteksi kontur
    contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
  Pada baris ini, dilakukan deteksi kontur pada gambar hasil thresholding menggunakan fungsi cv2.findContours(). Fungsi ini akan menghasilkan dua output, yaitu contours (daftar kontur yang terdeteksi) dan hierarchy (hierarki kontur). Parameter-parameter yang digunakan dalam fungsi ini adalah sebagai berikut:
  - thresh: Gambar hasil thresholding yang akan dicari konturnya.
  - cv2.RETR_TREE: Metode pengambilan kontur yang digunakan. RETR_TREE digunakan untuk mengambil semua kontur dan menghasilkan hierarki lengkap.
  - cv2.CHAIN_APPROX_SIMPLE: Metode aproksimasi garis yang digunakan
  - CHAIN_APPROX_SIMPLE digunakan untuk menyimpan titik-titik yang berhubungan dengan kontur sehingga menghasilkan kontur yang lebih sederhana.

7. Pembuatan masker
    mask = np.zeros(shape=(gray.shape), dtype=np.uint8)
    cv2.drawContours(mask, contours, -1, (1,0,0), cv2.FILLED)   
    mask = (~(mask == 1) * 1).astype(np.uint8)
  Pada baris ini, dibuatlah gambar mask (masking) berdasarkan kontur yang telah terdeteksi. Langkah-langkahnya adalah sebagai berikut:
  - Pertama, dibuat array dengan ukuran yang sama dengan citra keabuan menggunakan np.zeros(). Array ini akan digunakan sebagai mask.
  - Kemudian, kontur-kontur yang telah terdeteksi digambar di atas mask menggunakan cv2.drawContours(). Dalam contoh ini, digunakan warna (1,0,0) yang merepresentasikan warna merah dan parameter cv2.FILLED digunakan untuk menggambar kontur dengan warna yang diberikan.
  - Setelah itu, dilakukan operasi logika NOT pada mask untuk membalik piksel menggunakan operator ~. Kemudian, hasilnya dikalikan dengan 1 untuk mengubah tipe data menjadi np.uint8.

8. Penerapan operasi bitwise
    output = cv2.bitwise_and(image, image, mask=mask)
  Pada baris ini, dilakukan operasi bitwise AND antara gambar asli (image) dan mask menggunakan fungsi cv2.bitwise_and(). Operasi ini menghasilkan gambar keluaran (output) yang hanya mempertahankan piksel pada gambar asli yang sesuai dengan piksel pada mask.

9. Menampilkan gambar input dan output
    fig, axes = plt.subplots(1,2, figsize=(10,10))
    ax = axes.ravel()

    ax[0].imshow(image, cmap='gray')
    ax[0].set_title("Input")

    ax[1].imshow(output, cmap='gray')
    ax[1].set_title("Output")
  Pada baris ini, digunakan plt.subplots() untuk membuat tampilan gambar input dan output secara berdampingan. Kemudian, gambar input (image) ditampilkan pada sumbu pertama (ax[0]) dan diberi judul "Input". Gambar output (output) ditampilkan pada sumbu kedua (ax[1]) dan diberi judul "Output".

10. Menyimpan gambar hasil
    cv2.imwrite('Output Image.png', output)
  Pada baris ini, gambar hasil (output) disimpan dalam format PNG dengan nama file 'Output Image.png' menggunakan fungsi cv2.imwrite().

Dengan menjalankan program ini, program akan membaca gambar asli, kemudian mengkonversinya ke format RGB. Selanjutnya, program akan mengubah gambar menjadi citra keabuan menggunakan grayscale. Setelah itu, dilakukan thresholding pada citra keabuan untuk menghasilkan gambar dengan latar belakang yang lebih tersegmentasi.

Selanjutnya, program akan mendeteksi kontur pada gambar hasil thresholding. Kontur tersebut akan digunakan untuk membuat sebuah mask (masking) yang akan digunakan untuk menghapus latar belakang pada gambar asli.

Setelah pembuatan mask, program akan menerapkan operasi bitwise AND antara gambar asli dan mask. Hal ini akan menghasilkan gambar output yang hanya mempertahankan piksel pada gambar asli yang sesuai dengan piksel pada mask. Dengan demikian, latar belakang pada gambar akan dihapus.

Terakhir, program akan menampilkan gambar asli dan gambar output secara berdampingan menggunakan matplotlib. Gambar output juga akan disimpan sebagai file PNG dengan nama 'Output Image.png'.

Dengan menjalankan program ini, Anda akan melihat gambar asli dan gambar output yang telah dihapus latar belakangnya. Gambar output akan menampilkan objek utama tanpa adanya latar belakang yang mengganggu.













