# 🛒 SpringStore - Spring Boot E-Commerce API

Proyek ini adalah API RESTful backend yang dibangun dengan **Spring Boot** untuk mengelola otentikasi pengguna, daftar produk, operasi keranjang, dan pesanan. Ini dirancang untuk digunakan dalam sistem e-commerce atau manajemen inventaris.

---

## 🚀 Persyaratan Proyek

Untuk menjalankan proyek ini secara lokal, Anda memerlukan hal berikut:

* **Java 17+** (Direkomendasikan Java 21)

* **Maven 3.8+**

* **PostgreSQL** atau RDBMS yang kompatibel

* **Postman** (untuk pengujian API)

* **Docker (opsional)** – untuk database dan deployment

* **Lombok Plugin** diaktifkan di IDE Anda (IntelliJ IDEA, Eclipse, dll.)

---

## 🧰 Stack Digunakan

| Lapisan | Teknologi |
| ----- | ----- |
| Bahasa | Java 21 |
| Framework | Spring Boot 3.5.3 |
| Otentikasi | Spring Security + JWT |
| Akses Data | Spring Data JPA |
| Database | PostgreSQL (dapat dikonfigurasi) |
| Alat Build | Maven |
| Validasi | Jakarta Bean Validation |
| Unggahan | Multipart/Form-Data (unggah gambar) |
| Pengujian | JUnit 5, Mockito |
| Dokumentasi API | Swagger-UI |

---

## 🔗 Hubungan Entitas

### 1. **User**

* `id`, `name`, `email`, `password`, `role`.

* Mengimplementasikan `UserDetails` untuk Spring Security.

### 2. **Product**

* `id`, `name`, `description`, `price`, `stock`, `imageUrl`.

### 3. **Cart**

* Setiap pengguna memiliki satu keranjang.

* Keranjang memiliki banyak item keranjang (`CartItem`).

### 4. **CartItem**

* Menghubungkan `Cart` dengan `Product` dan `quantity`.

### 5. **Order**

* Terhubung ke pengguna (`User`).

* Berisi alamat pengiriman, nomor telepon, status (misalnya `PENDING`, `PREPARING`, `DELIVERING`, `DELIVERED`, `CANCELED`).

* Memiliki banyak item pesanan (`OrderItem`).

### 6. **OrderItem**

* Menghubungkan `Order` dengan `Product`, `quantity`, dan `priceAtOrder` (harga produk saat pesanan dibuat).

---

## ⚙️ Konfigurasi Database

Sebelum menjalankan aplikasi, Anda perlu mengkonfigurasi database PostgreSQL Anda.

1. **Buat Database:**
   Buat database PostgreSQL baru, misalnya `springstore_db`.

   ```sql
   CREATE DATABASE springstore_db;
   ```

2. **Buat Pengguna Database (Opsional, tetapi direkomendasikan):**
   Buat pengguna database dengan hak akses yang sesuai.

   ```sql
   CREATE USER springuser WITH PASSWORD 'springpass';
   GRANT ALL PRIVILEGES ON DATABASE springstore_db TO springuser;
   ```

3. **Perbarui `application.properties`:**
   Buka `src/main/resources/application.properties` dan perbarui detail koneksi database Anda:

   ```properties
   # Database Config
   spring.datasource.url=jdbc:postgresql://localhost:5432/springstore_db
   spring.datasource.username=springuser
   spring.datasource.password=springpass

   # JWT Config (Ganti dengan kunci rahasia yang kuat dan unik!)
   jwt.secret=yourVeryLongAndComplexSecretKeyForJWTAuthenticationThatShouldBeAtLeast256BitsLongAndStoredSecurely
   jwt.expiration=86400000 # 24 jam dalam milidetik
   ```

   **PENTING**: Ganti `yourVeryLongAndComplexSecretKeyForJWTAuthenticationThatShouldBeAtLeast256BitsLongAndStoredSecurely` dengan kunci rahasia yang kuat dan acak untuk JWT Anda. Anda dapat menggunakan generator kunci online atau alat untuk membuat string yang aman.

---

## 🚀 Menjalankan Aplikasi

### Dengan Maven

1. **Klon Repositori:**

   ```bash
   git clone [https://github.com/your-username/SpringStore.git](https://github.com/your-username/SpringStore.git)
   cd SpringStore
   ```

2. **Bangun Proyek:**

   ```bash
   mvn clean install
   ```

   Untuk melewati pengujian saat membangun:

   ```bash
   mvn clean install -DskipTests
   ```

3. **Jalankan Aplikasi:**

   ```bash
   mvn spring-boot:run
   ```

   Aplikasi akan berjalan di `http://localhost:8081`.

### Dengan Docker (Direkomendasikan untuk Pengembangan Cepat)

Pastikan Anda memiliki Docker dan Docker Compose terinstal.

1. **Bangun Gambar Docker:**

   ```bash
   docker build -t springstore-app .
   ```

2. **Jalankan dengan Docker Compose:**
   Ini akan memulai database PostgreSQL dan aplikasi Spring Boot Anda dalam kontainer terpisah.

   ```bash
   docker compose up -d
   ```

   * `-d` menjalankan kontainer dalam mode `detached` (di latar belakang).

3. **Periksa Kontainer yang Berjalan:**

   ```bash
   docker ps
   ```

4. **Lihat Log Aplikasi:**

   ```bash
   docker logs springstore-cont # Ganti 'springstore-cont' dengan nama kontainer aplikasi Anda jika berbeda
   ```

5. **Hentikan Kontainer:**

   ```bash
   docker compose down
   ```

---

## 🛠️ Dokumentasi API (Swagger UI)

Setelah aplikasi berjalan, Anda dapat mengakses dokumentasi API interaktif menggunakan Swagger UI di:

`http://localhost:8081/swagger-ui.html`

Anda dapat menguji semua endpoint API langsung dari antarmuka ini.

---

## 🔗 Endpoint API

### 🧑‍💼 Otentikasi

| Metode | Endpoint | Deskripsi |
| :----- | :----- | :----- |
| `POST` | `/api/auth/login` | Login pengguna, mengembalikan JWT |
| `POST` | `/api/auth/register` | Mendaftarkan pengguna baru |
| `POST` | `/api/auth/change-password` | Mengubah password (terotentikasi) |

### 📦 Produk

| Metode | Endpoint | Deskripsi |
| :----- | :----- | :----- |
| `GET` | `/api/products` | Daftar semua produk |
| `GET` | `/api/products/{id}` | Dapatkan produk berdasarkan ID |
| `POST` | `/api/products` | Buat produk (hanya admin) |
| `PUT` | `/api/products/{id}` | Perbarui produk (hanya admin) |
| `DELETE` | `/api/products/{id}` | Hapus produk (hanya admin) |

> ✅ `POST`/`PUT`/`DELETE` membutuhkan peran `ROLE_ADMIN`.

### 🛒 Keranjang

| Metode | Endpoint | Deskripsi |
| :----- | :----- | :----- |
| `GET` | `/api/cart` | Lihat keranjang pengguna saat ini |
| `POST` | `/api/cart/add` | Tambahkan item ke keranjang |
| `DELETE` | `/api/cart/item/{cartItemId}` | Hapus item tertentu dari keranjang |
| `DELETE` | `/api/cart` | Kosongkan seluruh keranjang |

> ✅ Membutuhkan otentikasi.

### 📬 Pesanan

| Metode | Endpoint | Deskripsi |
| :----- | :----- | :----- |
| `GET` | `/api/orders` | Dapatkan semua pesanan (hanya admin) |
| `GET` | `/api/orders/user` | Dapatkan pesanan pengguna saat ini |
| `POST` | `/api/orders` | Buat pesanan dari keranjang |
| `PUT` | `/api/orders/{orderId}/status` | Perbarui status pesanan (hanya admin) |

> ✅ Membutuhkan otentikasi.

---

## 📂 Struktur Proyek

```
SpringStore
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── springstore
│   │   │           └── springstore
│   │   │               ├── SpringstoreApplication.java   # Kelas aplikasi utama
│   │   │               ├── config                      # Konfigurasi Spring Security dan JWT
│   │   │               │   ├── ApplicationConfig.java
│   │   │               │   └── SecurityConfig.java
│   │   │               ├── controller                  # Endpoint REST API
│   │   │               │   ├── AuthController.java
│   │   │               │   ├── CartController.java
│   │   │               │   ├── ImageController.java
│   │   │               │   ├── OrderController.java
│   │   │               │   └── ProductController.java
│   │   │               ├── dto                         # Data Transfer Objects
│   │   │               │   ├── AddItemToCartRequest.java
│   │   │               │   ├── AuthRequest.java
│   │   │               │   ├── AuthResponse.java
│   │   │               │   ├── CartDTO.java
│   │   │               │   ├── CartItemDTO.java
│   │   │               │   ├── ChangePasswordRequest.java
│   │   │               │   ├── CreateOrderRequest.java
│   │   │               │   ├── CreateProductRequest.java
│   │   │               │   ├── OrderDTO.java
│   │   │               │   ├── OrderItemDTO.java
│   │   │               │   ├── ProductDTO.java
│   │   │               │   ├── UpdateOrderStatusRequest.java
│   │   │               │   └── UpdateProductRequest.java
│   │   │               ├── model                       # Entitas JPA dan Enum
│   │   │               │   ├── Cart.java
│   │   │               │   ├── CartItem.java
│   │   │               │   ├── Order.java
│   │   │               │   ├── OrderItem.java
│   │   │               │   ├── OrderStatus.java
│   │   │               │   ├── Product.java
│   │   │               │   ├── Role.java
│   │   │               │   └── User.java
│   │   │               ├── repository                  # Antarmuka Spring Data JPA Repositories
│   │   │               │   ├── CartItemRepository.java
│   │   │               │   ├── CartRepository.java
│   │   │               │   ├── OrderItemRepository.java
│   │   │               │   ├── OrderRepository.java
│   │   │               │   ├── ProductRepository.java
│   │   │               │   └── UserRepository.java
│   │   │               ├── security                    # Komponen Keamanan JWT
│   │   │               │   ├── JwtAuthenticationFilter.java
│   │   │               │   └── JwtService.java
│   │   │               └── service                     # Logika Bisnis
│   │   │                   ├── AuthService.java
│   │   │                   ├── CartService.java
│   │   │                   ├── OrderService.java
│   │   │                   ├── ProductService.java
│   │   │                   └── UserService.java
│   │   └── resources
│   │       ├── application.properties                  # File konfigurasi aplikasi
│   │       └── static                                  # Direktori untuk file statis (jika ada)
│   └── test
│       └── java
│           └── com
│               └── springstore
│                   └── springstore
│                       └── SpringstoreApplicationTests.java # Kelas pengujian utama
├── uploads                                             # Direktori untuk gambar produk yang diunggah
│   └── product-images
├── pom.xml                                             # Konfigurasi Maven
└── Dockerfile                                          # Dockerfile untuk membangun gambar aplikasi
└── docker-compose.yml                                  # Docker Compose untuk menjalankan aplikasi dan DB
```

---

## 🐳 Dockerfile

```dockerfile
# Gunakan gambar dasar OpenJDK untuk Java 21
FROM openjdk:21-jdk-slim

# Informasi pembuat
LABEL maintainer="your.email@example.com"

# Atur direktori kerja di dalam kontainer
WORKDIR /app

# Salin file JAR yang sudah dibangun ke direktori kerja kontainer
# Asumsikan file JAR Anda bernama 'springstore-0.0.1-SNAPSHOT.jar'
COPY target/springstore-0.0.1-SNAPSHOT.jar app.jar

# Buat direktori 'uploads/product-images' untuk menyimpan gambar produk
RUN mkdir -p /app/uploads/product-images

# Ekspos port yang digunakan aplikasi Spring Boot
EXPOSE 8081

# Perintah untuk menjalankan aplikasi saat kontainer dimulai
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 📝 docker-compose.yml

```yaml
version: '3.8'

services:
  db:
    image: postgres:16-alpine # Menggunakan versi PostgreSQL yang lebih baru dan ringan
    container_name: springstore-db
    environment:
      POSTGRES_DB: springstore_db
      POSTGRES_USER: springuser
      POSTGRES_PASSWORD: springpass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data # Volume persisten untuk data database
    healthcheck: # Pemeriksaan kesehatan untuk memastikan DB siap sebelum aplikasi dimulai
      test: ["CMD-SHELL", "pg_isready -U springuser -d springstore_db"]
      interval: 5s
      timeout: 5s
      retries: 5

  app:
    build: . # Membangun gambar dari Dockerfile di direktori saat ini
    container_name: springstore-cont
    ports:
      - "8081:8081"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/springstore_db
      SPRING_DATASOURCE_USERNAME: springuser
      SPRING_DATASOURCE_PASSWORD: springpass
      JWT_SECRET: yourVeryLongAndComplexSecretKeyForJWTAuthenticationThatShouldBeAtLeast256BitsLongAndStoredSecurely # Ganti ini!
      JWT_EXPIRATION: 86400000
    depends_on:
      db:
        condition: service_healthy # Pastikan DB sehat sebelum memulai aplikasi
    volumes:
      - product_images_data:/app/uploads/product-images # Volume persisten untuk gambar produk

volumes:
  postgres_data:
  product_images_data:
```

---

## 🤝 Kontribusi

Kontribusi dipersilakan! Silakan fork repositori ini dan ajukan pull request dengan fitur atau perbaikan Anda.

---

## 📄 Lisensi

Proyek ini dilisensikan di bawah Lisensi MIT. Lihat file `LICENSE` untuk detail lebih lanjut.

---
