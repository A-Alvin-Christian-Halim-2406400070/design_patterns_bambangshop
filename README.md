# BambangShop Publisher App
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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
> In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber
is defined as an interface. Explain based on your understanding of Observer design patterns,
do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model
struct is enough?

Untuk kasus sekarang, interface atau trait tidak dibutuhkan agar program bisa berjalan dengan baik karena kita hanya ada 1 observer yaitu `Subscriber`. Namun sebagai best practice sebaiknya tetap kita gunakan interface/trait agar kita mengikuti SOLID principles, terutama OCP karena dengan adanya interface/trait kita bisa menambahkan observer baru tanpa mengubah kode yang sudah ada.

> id in Program and url in Subscriber is intended to be unique. Explain based on your
understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently
use is necessary for this case?

Dalam kasus ini penggunaan DashMap diperlukan. Pertama, DashMap membantu kita untuk mengurangi kompleksitas waktu untuk opreasi read/insert/delete dari O(n) menjadi O(1). Selain itu DashMap juga bersifat thread-safe, sedangkan Vec tidak thread-safe, sehingga DashMap lebih cocok untuk concurrency dan juga agar mengikuti constraint dari compiler.

> When programming using Rust, we are enforced by rigorous compiler constraints to make a
thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we
used the DashMap external library for thread safe HashMap. Explain based on your
understanding of design patterns, do we still need DashMap or we can implement Singleton
pattern instead?

Dalam kasus ini Singleton pattern tidak cukup karena singleton pattern masih dapat mengalami permasalahan dalam konteks multithreading. Singleton pattern hanya memastikan bahwa dalam satu waktu hanya ada 1 instance yang digunakan namun singleton tidak menangani apabila banyak thread mengakses instance tersebut secara bersamaan sehingga dapat menyebabkan race condition.

#### Reflection Publisher-2

> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”.
Model in MVC covers both data storage and business logic. Explain based on your
understanding of design principles, why we need to separate “Service” and “Repository” from
a Model?

Dengan memisahkan service dan repository dari model, kita akan menerapkan SOLID principle, khususnya SRP. Apabila kita menggabungkan semuanya ke dalam model, maka model akan menghandle data storage, business logic, dan juga data representation. Hal ini melanggar SRP yang menyatakan bahwa satu class hanya boleh melakukan satu fungsi, sedangkan class model melakukan 3. Selain itu dengan memisahkan service, repository, dan model, kita akan lebih mudah untuk melakukan testing, development, atau refactoring tanpa menggangu bagian lain dari kode kita. 

> What happens if we only use the Model? Explain your imagination on how the interactions
between each model (Program, Subscriber, Notification) affect the code complexity for
each model?

Jika kita hanya menggunakan model, maka dalam Model akan ada 3 models dan juga impelementasi repository dan service yang berbeda-beda untuk masing-masing model tersebut. Tentu saja kodenya dapat tetap jalan namun code complexitynya akan sangat tinggi karena kita memasukkan banyak fungsionalitas yang berbeda-beda ke dalam model yang akan membuat maintainability codebase kita menjadi jelek. Ketika kita ingin mengubah satu bagian, misalkan repository, maka perubahan tersebut juga akan mempengaruhi models dan service sehingga dapat meng-introduce bug.

> Have you explored more about Postman? Tell us how this tool helps you to test your current
work. You might want to also list which features in Postman you are interested in or feel like it
is helpful to help your Group Project or any of your future software engineering projects.

Saya sudah melakukan eksplorasi aplikasi Postman. Postman adalah aplikasi yang membantu kita untuk menguji API kita tanpa kita perlu mengimplementasi frontend dan membuka browser atau membuat kode. Kita cukup membuat sebuah http request dengan body contents yang sesuai dan kirim ke endpoint kita. Salah satu keunggulan dari postman, dibandingkan dengan program lain seperti curl, adalah user interfacenya yang user friendly. Kita tidak perlu menghafal tags dan argumen untuk memodifikasi http request kita, kita cukup pilih section yang ingin kita ubah lalu masukkan perubahannya. Selain itu postman juga memiliki fitur untuk membagikan postman collection kita yang akan memudahkan anggota-anggota lain untuk melakukan pengujian kepada api kita karena mereka tidak perlu membaca kode kita untuk mengetahui apa saja yang endpoint tersebut perlukan dari http request kita, seperti apakah request teresebut perlu body yang memiliki content json, dan apa saja keys yang harus ada di json tersebut. Dengan membagikan collection kita, anggota tim yang lain hanya perlu mengganti value yang ingin mereka ganti. Terakhir postman juga memudahkan kita untuk membuat api docs dimana hal tersebut sangat penting agar tim kita tidak perlu mencari dan membaca kode kita untuk mengetahui hal-hal seperti apa yang di return oleh api tersebut, apa kegunaan api tersebut, dll.

#### Reflection Publisher-3

>  Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and
Pull model (subscribers pull data from publisher). In this tutorial case, which variation of
Observer Pattern that we use?

Kita menggunakan observer pattern dengan push model. Dalam model ini, apabila publisher melakukan perubahan data, maka subscriber akan mendapatkan notifikasi. Fungsionalitas tersebut diimplementasikan dengan service `notification`

```rs
use std::thread;

use bambangshop::{Result, compose_error_response};
use rocket::http::Status;
use crate::model::notification::Notification;
use crate::model::product::Product;
use crate::model::subscriber::Subscriber;
use crate::repository::subscriber::SubscriberRepository;

pub struct NotificationService;

impl NotificationService {
    pub fn subscribe(product_type: &str, subscriber: Subscriber) -> Result<Subscriber> {
        let product_type_upper: String = product_type.to_uppercase();
        let product_type_str: &str = product_type_upper.as_str();
        let subscriber_result: Subscriber = SubscriberRepository::add(product_type_str, subscriber);
        return Ok(subscriber_result);
    }

    pub fn unsubscribe(product_type: &str, url: &str) -> Result<Subscriber> {
        let product_type_upper: String = product_type.to_uppercase();
        let product_type_str: &str = product_type_upper.as_str();
        let result: Option<Subscriber> = SubscriberRepository::delete(product_type_str, url);

        if result.is_none() {
            return Err(compose_error_response(
                Status::NotFound,
                String::from("Subscriber not found.")
            ));
        }

        return Ok(result.unwrap());
    }

    pub fn notify(&self, product_type: &str, status: &str, product: Product) {
        let mut payload: Notification = Notification {
            product_title: product.clone().title,
            product_type: String::from(product_type),
            product_url: product.clone().get_url(),
            subscriber_name: String::from(""),
            status: String::from(status)
        };

        let subscribers: Vec<Subscriber> = SubscriberRepository::list_all(product_type);
        for subscriber in subscribers {
            payload.subscriber_name = subscriber.clone().name;
            let subscriber_clone = subscriber.clone();
            let payload_clone = payload.clone();
            thread::spawn(move || subscriber_clone.update(payload_clone));
        }
    }

}
```
Contoh penggunaannya dapat dilihat dalam fungsi `create` di `product` service
```rs
    pub fn create(mut product: Product) -> Result<Product> {
        product.product_type = product.product_type.to_uppercase();
        let product_result: Product = ProductRepository::add(product);

        NotificationService.notify(&product_result.product_type, "CREATED", product_result.clone());

        return Ok(product_result);
    }
```

disini dapat dilihat bahwa setiap pemangillan create akan memanggil `NotificationService` yang akan mengirimkan notifikasi "CREATED" ke subscribers, dimana flow ini sesuai dengan flow push model

> What are the advantages and disadvantages of using the other variation of Observer Pattern
for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Keuntungan dari penggunaan pull model adalah subscriber/observer memiliki kontrol atas apa yang ingin dia query. Hal tersebut juga berkaitan langsung dengan code complexity dari observable terutama jika kita memiliki banyak jenis observer yang masing-masing menginginkan data yang berbeda. Misalkan kita mempunyai 2 observer, 1 yang ingin di notify hanya ketika sebuah produk dibuat (`create`) dan satu lagi hanya ketika sebuah produk dihapus (`delete`), maka apabila kita menggunakan push model, kita perlu menyimpan 2 list untuk kedua observer tersebut, memodifikasi implementasi `notify` kita agar kita mengirim notifikasi yang benar ke observer yang benar. Tentu jika hanya 2 observer hal tersebut masih lumayan mudah namun semakin banyak jenis observer maka semakin susah. Jika kita menggunakan pull model, maka masing-masing jenis observer tinggal menquery data yang mereka inginkan. Namun kekurangan dari pull model adalah observer harus mengetahui informasi tentang subject. Misalkan bagaimana mengquery subject tersebut, apa api/method yang harus dipanggill, dll. Hal ini berarti apabila ada perubahan api untuk query, maka kita harus ubah modifikasi di semua jenis observer yang menggunakan api tersebut. Selain itu apabila ada banyak overlap data yang diquery oleh observer, dapat memiliki dampak ke performance karena kita akan mengirimkan data yang sama berulang-ulang.

> Explain what will happen to the program if we decide to not use multi-threading in the
notification process.

Apabila kita tidak menggunakan multi-threading, maka akan terjadi queue ketika kita ingin notify. Hal tersebut akan memperlambat proses notification ke semua observer terutama jika jumblahnya banyak. Misalkan kita memiliki 1 juta observer, maka observer ke-1 juta harus menunggu waktu yang lumayan lama untuk mendapatkan notifikasinya. Selain itu juga dapat terjadi inkonsistensi antara notifikasi dan data sebenarnya. Misalkan kita mengirimkan notifikasi bahwa sebuah produk sudah dibuat ke 1 juta observers. Namun ditengah-tengah pengiriman ternyata produk tersebut dihapus, maka observer ke-1 juta akan mendapatkan notifikasi bahwa produk tersebut sudah dibuat namun ketika dia cek, ternyata produknya tidak ada.



