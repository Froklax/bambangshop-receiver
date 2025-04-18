# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. Kita menggunakan `RwLock<>` pada tutorial ini untuk menyinkronkan akses ke `Vec` yang menyimpan `Notification` karena `RwLock<>` memungkinkan beberapa thread untuk membaca data secara bersamaan tanpa harus saling menunggu, asalkan tidak ada thread yang sedang menulis. Hal ini sangat berguna ketika data tersebut lebih sering dibaca daripada diubah. Jika kita menggunakan `Mutex<>`, setiap operasi membaca maupun menulis harus mengunci data secara eksklusif sehingga hanya satu thread yang dapat mengakses data pada satu waktu. Kondisi ini dapat menyebabkan bottleneck dan menurunkan performa aplikasi apabila terdapat banyak thread yang mencoba membaca data secara bersamaan.

2. Rust tidak mengizinkan mutasi langsung pada variabel static seperti yang bisa dilakukan di Java karena alasan keamanan dalam lingkungan multi-threaded. Di Java, variabel static yang dapat dimutasi melalui fungsi static berisiko menyebabkan race condition jika tidak didampingi mekanisme sinkronisasi yang tepat. Oleh karena itu, Rust menerapkan pendekatan yang lebih ketat dengan mewajibkan penggunaan crate seperti `lazy_static` untuk menginisialisasi variabel static dengan cara yang aman. Dengan `lazy_static`, variabel static dapat dibungkus dalam struktur sinkronisasi seperti `RwLock` atau `Mutex` sehingga memastikan bahwa setiap akses dan modifikasi data dilakukan dengan cara yang aman.

#### Reflection Subscriber-2

1. Saya sempat menjelajahi bagian lain di luar langkah-langkah tutorial, seperti file `src/lib.rs`. Dari yang saya lihat, file tersebut berfungsi untuk memuat file `.env` jika ada dan mengubah pengaturan default sesuai konfigurasi environment. File ini juga mendefinisikan type `Result` dengan error handling khusus dan menyediakan fungsi helper untuk membuat error response. Hal ini membantu agar seluruh aplikasi memiliki standar penanganan error dan konfigurasi yang konsisten.

2. Dengan menggunakan Observer pattern, penambahan subscriber baru menjadi lebih mudah. Setiap instance `Receiver` hanya perlu melakukan konfigurasi port dan melakukan subscribe ke `product_type` yang diinginkan tanpa perlu mengubah logika di sisi `Publisher`. Jika nantinya terdapat lebih dari satu instance `Main` app, masing-masing `Publisher` akan tetap mengirim notifikasi kepada subscriber yang telah mendaftar melalui mekanisme subscribe. Jika ada banyak `Publisher`, `Receiver` mungkin perlu penyesuaian agar bisa menangani notifikasi dari beberapa sumber sekaligus, seperti dengan menyesuaikan mekanisme subscribe dan unsubscribe untuk mendukung banyak domain.

3. Saya belum banyak bereksperimen dengan membuat test sendiri di `Postman` atau menambahkan dokumentasi lebih mendalam pada collection `Postman`. Tetapi dari apa yang saya lihat sampai sekarang, saya menyadari bahwa fitur testing di `Postman` sangat berguna untuk memverifikasi fungsi endpoint secara otomatis. Fitur tersebut dapat membantu dalam proyek kelompok maupun pengembangan aplikasi di masa depan karena memungkinkan testing yang cepat dan terstruktur tanpa harus menulis script testing secara manual.