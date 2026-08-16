# GrabFood Fraud Analytics – Capstone Project Module 3

## Ringkasan Project
Project ini memposisikan analis sebagai **Data Analyst di GrabFood / GrabUnlimited Fraud Analytics**. Tujuan utamanya adalah mendeteksi potensi kebocoran subsidi promo, kualitas data operasional, dan anomali transaksi yang dapat mengindikasikan eksploitasi program GrabUnlimited oleh merchant/akun tidak wajar.

## Dataset
Dataset terdiri dari tiga tabel:

1. `grab_food_orders.csv` — tabel transaksi utama berisi 300,000 order.
2. `grab_merchants.csv` — dimensi merchant berisi 8,000 merchant.
3. `grab_users.csv` — dimensi pengguna berisi 40,000 user.

## Pertanyaan Bisnis
1. Apakah terdapat merchant dengan transaksi sukses tinggi tetapi didominasi hanya oleh 2–3 user yang sama?
2. Berapa estimasi subsidi promo GrabUnlimited yang berisiko bocor akibat transaksi/anomali yang terindikasi tidak wajar?
3. Seberapa besar masalah kualitas data pada status order dan cancel reason?
4. Berapa persentase order batal/gagal yang tidak memiliki cancel reason?
5. Berapa banyak order berstatus selesai tetapi memiliki urutan waktu mustahil?
6. Cuisine type dan merchant mana yang perlu diprioritaskan untuk investigasi fraud?

## Metodologi Singkat
- Menggabungkan data order, merchant, dan user berdasarkan `merchant_id` dan `user_id`.
- Standarisasi `order_status` dari 7 label mentah menjadi 3 kategori: `Completed`, `Canceled`, dan `Failed`.
- Validasi tipe data waktu dan deteksi logical error: `completed_time < driver_arrived_time`.
- Deteksi outlier promo dengan rule bisnis `promo_discount > Rp30.000`, karena nilai normal terbesar berada pada Rp30.000 sementara terdapat promo ekstrem Rp9.999.999.
- Membuat merchant risk score berdasarkan jumlah promo outlier, logical error, cancel reason “Resto Tidak Ditemukan”, success ratio tinggi, dan GrabUnlimited ratio tinggi.

## Temuan Utama
- **Tidak ditemukan pola merchant yang hanya dilayani 2–3 user dominan**; minimum unique user per merchant adalah 18 user.
- Terdapat **1,488 order promo outlier** atau 0.50% dari total order, tetapi menyerap **Rp14,879,998,512** atau 82.15% dari total promo positif.
- Terdapat **9,603 logical error** pada order selesai, setara 4.00% dari completed order.
- Terdapat **90,364 baris status tidak standar** atau 30.12% dari total order.
- Pada order `Canceled`/`Failed`, **40.27%** tidak memiliki `cancel_reason`.
- Estimasi subsidi berisiko berdasarkan union promo outlier dan logical error mencapai **Rp15,168,088,512** atau 83.74% dari total promo positif.

## Rekomendasi Bisnis
1. **Promo guardrail:** hard cap promo per order dan validasi otomatis untuk nilai diskon di atas Rp30.000.
2. **Merchant risk monitoring:** merchant dengan risk score tertinggi masuk daftar investigasi harian.
3. **System patching:** order tidak boleh `Completed` jika driver belum tercatat tiba di merchant/customer berdasarkan GPS radius.
4. **Data standardization:** dropdown cancel reason wajib untuk status `Canceled` dan `Failed`, termasuk opsi khusus “Resto Tidak Ditemukan”.
5. **Driver protection:** kompensasi cancellation fee dapat diprioritaskan untuk order dengan alasan resto tidak ditemukan dan bukti GPS valid.

## File Output
- `GrabFood Fraud Analytics.ipynb` — dokumentasi analisis lengkap.
- `GrabFood Fraud Analytics Presentation.pptx` — slide presentasi insight dan rekomendasi.
- `Readme.md` — dokumentasi project ringkas.
