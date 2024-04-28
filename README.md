## Reflection Tutorial 9
**Nama** : **Virgillia Yeala Prabowo**
**Kelas** : **ADPRO-A**
**NPM** : **220829856**

##### What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
| Unary RPC               | Server streaming RPC                                            | Bi-directional streaming RPC                                |
|-------------------------|------------------------------------------------------------------|-------------------------------------------------------------|
| Metode ini digunakan ketika client mengirimkan satu permintaan ke server dan kemudian server mengirimkan satu respons kembali.| Metode ini digunakan ketika client mengirimkan satu permintaan ke server dan server mengirimkan respons yang berkelanjutan kembali ke client dalam bentuk stream.| Metode ini digunakan ketika client dan server saling berkomunikasi dengan mengirimkan sejumlah data dalam bentuk stream.|
|Cocok digunakan ketika client hanya perlu melakukan satu permintaan dan mendapatkan satu respons dalam satu waktu.|Cocok digunakan ketika client perlu menerima sejumlah besar data dari server, misalnya ketika client meminta data historis.|Cocok digunakan untuk aplikasi real-time seperti chat aplikasi, di mana baik client maupun server perlu mengirim dan menerima data secara simultan.|

##### What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
1. **Autentikasi**: Pastikan untuk mengimplementasikan mekanisme autentikasi seperti OAuth, JWT, atau TLS untuk memverifikasi identitas client dan server.
2. **Otorisasi**: Tentukan peran dan izin akses untuk setiap pengguna atau klien, dan pastikan hanya klien yang sah yang memiliki akses yang sesuai.
3. **Enkripsi data**: Gunakan TLS untuk mengenkripsi data yang dikirim antara client dan server, sehingga mencegah pihak ketiga untuk mengakses atau memanipulasi data yang dikirim.

##### What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?
1. **Sinkronisasi**: Penting untuk memastikan sinkronisasi antara pengiriman dan penerimaan pesan agar tidak terjadi konflik atau kehilangan pesan.
2. **Penanganan kesalahan**: Perlunya penanganan kesalahan yang baik untuk mengatasi kegagalan koneksi atau kesalahan lainnya dengan baik tanpa mengganggu aliran pesan.
3. **Manajemen memori**: Perlunya manajemen memori yang efisien, terutama dalam mengelola buffer pesan yang dikirim dan diterima.

##### What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?
| Kelebihan | Kekurangan| 
|-------------------------|--------------------------|
|Memungkinkan untuk mengubah Receiver menjadi stream gRPC dengan mudah.| Memerlukan penanganan error yang tepat untuk mengatasi kesalahan yang terjadi dalam streaming.|
|Memanfaatkan kemampuan Tokio untuk menangani operasi I/O secara efisien. | Memerlukan pemahaman yang baik tentang bagaimana Tokio bekerja untuk mencegah deadlock atau masalah lainnya.|

##### In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?
1. **Penggunaan modul**: Pisahkan kode ke dalam modul-modul yang berbeda berdasarkan fungsionalitasnya seperti autentikasi, otorisasi, dan layanan utama.
2. **Penggunaan Trait**: Gunakan trait untuk menentukan perilaku umum yang dapat diimplementasikan oleh berbagai jenis layanan.
3. **Penggunaan fungsi bantu**: Abstraksikan logika kompleks ke dalam fungsi bantu yang dapat digunakan kembali di berbagai bagian dari kode.

##### In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
```rust
// 1. Validasi Permintaan Pembayaran
let payment_request = request.into_inner();
if !is_payment_valid(&payment_request) {
        return Err(Status::invalid_argument("Invalid payment request"));
}

// 2. Otentikasi dan Otorisasi
if !is_user_authorized(&payment_request.user_id) {
        return Err(Status::permission_denied("User is not authorized"));
}

// 3. Pemrosesan Pembayaran
let payment_result = process_payment_logic(&payment_request).await;

    match payment_result {
        Ok(payment_response) => {
            // 4. Pelacakan Transaksi
            track_transaction(&payment_request, &payment_response).await;
            Ok(Response::new(payment_response))
        }
        Err(err) => Err(Status::internal(err.to_string())),
        }
```

##### What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
**Pengaruh pada Arsitektur dan Desain**
1. Perubahan arsitektur: Penggunaan gRPC mempengaruhi arsitektur sistem secara keseluruhan karena mengadopsi pola komunikasi yang berbeda, yaitu Remote Procedure Call (RPC).
2. Desain yang lebih terstruktur: gRPC mendorong penggunaan protokol yang didefinisikan dengan jelas, seperti Protocol Buffers, yang mengarah pada desain yang lebih terstruktur dan terdokumentasi dengan baik.

**Interoperabilitas dengan Teknologi dan Platform Lain**
1. Peningkatan interoperabilitas: Meskipun gRPC dirancang untuk bekerja paling baik dengan Protobuf, ia juga mendukung format pesan lain seperti JSON.
2. Keterbatasan dengan teknologi tertentu: Namun, karena gRPC memanfaatkan HTTP/2 sebagai transport layer, ada beberapa batasan dalam interoperabilitas dengan teknologi dan platform tertentu.

##### What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
| Kelebihan | Kekurangan| 
|-------------------------|--------------------------|
|Memungkinkan banyak permintaan dan respons untuk dikirimkan melalui satu koneksi TCP tunggal secara bersamaan, meningkatkan kinerja dan efisiensi.| Implementasi HTTP/2 lebih kompleks daripada HTTP/1.1 atau WebSocket.|
|Mengurangi overhead yang dihasilkan oleh header HTTP, mengurangi penggunaan bandwidth dan meningkatkan kinerja. | Meskipun dukungan untuk HTTP/2 semakin luas, beberapa browser lama mungkin tidak mendukungnya sepenuhnya.|
|Memungkinkan server untuk menginisiasi pengiriman data ke klien tanpa permintaan sebelumnya.|

##### How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?
| REST API (Permintaan-Respons) | gRPC (Streaming Bidirectional)| 
|-------------------------|--------------------------|
|REST API menggunakan model permintaan-respons, di mana klien membuat permintaan dan server memberikan respons.| gRPC mendukung streaming bidirectional, di mana klien dan server dapat saling mengirimkan sejumlah besar data secara real-time.|
|Tidak cocok untuk komunikasi real-time karena klien harus membuat permintaan baru untuk setiap informasi yang diperbarui.| Cocok untuk aplikasi yang memerlukan komunikasi real-time, seperti aplikasi chat atau sistem monitorisasi.|

##### What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

**gRPC (Protocol Buffers)**
| Pendekatan Berbasis Skema | Kelebihan| Keterbatasan|
|-------------------------|--------------------------|--------------------------|
|gRPC menggunakan Protocol Buffers, yang membutuhkan skema yang telah ditentukan sebelumnya untuk mendefinisikan format pesan.| Protocol Buffers lebih efisien dalam penggunaan bandwidth dan parsing data daripada JSON.|Protocol Buffers kurang fleksibel daripada JSON karena mengharuskan skema yang telah ditentukan sebelumnya.|
|| Protocol Buffers memungkinkan validasi otomatis data berdasarkan skema yang telah ditentukan.|

**REST API (JSON)**
| Sifat Tanpa Skema | Kelebihan| Keterbatasan|
|-------------------------|--------------------------|--------------------------|
| REST API sering menggunakan JSON, yang tidak memerlukan skema yang telah ditentukan sebelumnya.| JSON lebih fleksibel daripada Protocol Buffers karena tidak memerlukan skema yang telah ditentukan sebelumnya.|JSON cenderung menggunakan lebih banyak bandwidth daripada Protocol Buffers karena ukuran payload yang lebih besar.|
|| Data JSON lebih mudah dibaca oleh manusia.|