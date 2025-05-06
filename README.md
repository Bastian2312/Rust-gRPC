Nama: Bastian Adiputra Siregar \
NPM: 2306245005

___
# REFLECTION

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in 2.what scenarios would each be most suitable?

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

___

1. Unary RPC adalah pola komunikasi satu request satu response, cocok untuk operasi sederhana seperti query data. Server streaming RPC memungkinkan satu request dikirim dan server merespons dengan stream data bertahap, berguna untuk laporan atau monitoring. Bidirectional streaming memungkinkan client dan server bertukar pesan terus-menerus secara asinkron, sangat cocok untuk aplikasi real-time seperti chat atau collaborative editing. Perbedaan utamanya terletak pada arah dan jumlah aliran data.

2. Dalam gRPC Rust, security mencakup authentication (seperti JWT), authorization (hak akses), dan encryption (TLS). Gunakan crate seperti `tonic`, `rustls`, dan pastikan validasi input dilakukan. Perlu perhatian pada penyimpanan credential yang aman, log yang tidak membocorkan data sensitif, serta manajemen token seperti rotasi dan expiry. TLS setup penting agar komunikasi terenkripsi end-to-end.

3. Bidirectional streaming menantang karena harus mengelola stream lifecycle, concurrency, dan error handling secara asinkron. Pada aplikasi seperti chat, harus menjaga stream tetap terbuka tanpa blocking. Race condition bisa terjadi jika pengelolaan task tidak hati-hati. Shutdown stream juga perlu dilakukan secara clean agar tidak terjadi memory leak.

4. `tokio_stream::wrappers::ReceiverStream` mempermudah membuat stream dari `tokio::mpsc::Receiver`. Ini berguna dalam server streaming karena integrasi async-nya bagus. Namun, jika producer lambat atau error tidak ditangani, stream bisa terganggu. Debugging lebih rumit, dan jika tidak hati-hati, dapat menambah overhead performa.

5. Untuk modularitas, pisahkan layer: `proto`, `handler`, dan `business logic`. Gunakan trait dan module terpisah untuk reusable logic seperti validasi atau konversi data. Gunakan workspace atau crate modular agar service dapat diskalakan. Dengan pemisahan ini, testing, maintainability, dan integrasi menjadi lebih mudah dilakukan.

6. Service pembayaran kompleks butuh validasi input, integrasi dengan payment gateway (Stripe, Midtrans), mekanisme idempotency, dan retry saat gagal. Tambahkan logging dan audit trail untuk pelacakan. Gunakan async queue untuk pemrosesan tertunda, dan pastikan error tidak menyebabkan duplikasi pembayaran.

7. Menggunakan gRPC mengubah arsitektur jadi lebih contract-driven dan efisien. Ini cocok untuk komunikasi antarmicroservice dengan performa tinggi. Namun untuk akses dari browser atau REST client, dibutuhkan HTTP gateway. Schema enforcement dari gRPC membuat komunikasi lebih stabil tapi menambah kompleksitas integrasi dengan sistem lain.

8. HTTP/2 lebih efisien dibanding HTTP/1.1 karena mendukung multiplexing dan streaming. gRPC memanfaatkan ini untuk performa tinggi dan pengiriman data kontinu. WebSocket bisa jadi alternatif untuk event-driven, tapi tidak seketat gRPC dalam hal struktur dan validasi. HTTP/2 kurang didukung oleh beberapa tool lama, dan debugging bisa lebih sulit.

9. REST berbasis request-response stateless, cocok untuk API publik dan sederhana. gRPC mendukung long-lived stream yang lebih efisien untuk real-time communication seperti notifikasi atau chat. REST lebih mudah digunakan dan fleksibel untuk prototyping, tapi gRPC unggul untuk efisiensi dan komunikasi intensif antar layanan.

10. gRPC menggunakan Protocol Buffers yang schema-based, menjamin konsistensi dan efisiensi serialisasi. JSON lebih fleksibel dan human-readable, tapi lebih rentan terhadap kesalahan struktur. Perubahan data di gRPC lebih terkontrol tapi butuh backward compatibility. gRPC unggul di sistem skala besar dengan strict API contract, sedangkan REST/JSON lebih cocok untuk kebutuhan fleksibel dan akses publik.
