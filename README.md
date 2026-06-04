# ⚙️ Step-by-Step Postman 2FA E-Money App

Kita akan menguji 2FA pada E-Money APP menggunakan:

* Redis — Menyimpan OTP sementara dan session data.
* Postman — Menguji endpoint API.
* Go (Golang) — Backend service.
* Firebase Authentication — Validasi dan autentikasi pengguna.
* Firebase Cloud Messaging (FCM) — Mengirim notifikasi OTP ke perangkat pengguna.

---

# 🚀 Prasyarat

Pastikan telah menginstal:

## Docker

Cek instalasi:

```bash
docker --version
```

## Redis

Menjalankan Redis menggunakan Docker:

```bash
docker run -d \
--name redis-server \
-p 6379:6379 \
redis
```

Verifikasi:

```bash
docker ps
```

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/f5aa5885-e066-4e92-896d-e0d81530cb9c">
</p>

---

## Backend Go

Clone project:

```bash
git clone https://github.com/afnan923/golang-emoney.git
cd golang-emoney
```

Install dependency:

```bash
go mod tidy
```

Jalankan aplikasi:

```bash
go run main.go
```

---

# 🔥 Setup Firebase

* Buat project pada Firebase Console.
* Aktifkan Authentication.
* Download Service Account Key.
* Simpan file JSON pada folder project.

---

# 📧 Setup SMTP menggunakan Gmail

## Persyaratan Awal

Sebelum membuat App Password, Anda wajib mengaktifkan Verifikasi 2 Langkah (2-Step Verification) pada akun Google Anda. Jika belum aktif, Google tidak akan menampilkan menu Sandi Aplikasi.

---

## 1. Buka Akun Google

Buka browser dan masuk ke halaman Akun Google (`myaccount.google.com`). Pastikan Anda sudah login ke akun Gmail yang ingin digunakan untuk SMTP.

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/320dfb9f-2c72-4243-b215-545626dfa4ee">
</p>

---

## 2. Masuk ke Menu Keamanan

Pada panel sebelah kiri (atau menu navigasi jika menggunakan HP), klik menu Keamanan (Security).

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/ca4df094-6860-454f-bd25-2b9481ecee5f">
</p>

---

## 3. Akses Menu Verifikasi 2 Langkah

Gulir ke bawah hingga Anda menemukan bagian "Cara Anda login ke Google". Klik pada pilihan Verifikasi 2 Langkah (2-Step Verification). Anda mungkin akan diminta memasukkan password utama Anda lagi untuk konfirmasi.

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/66a85d43-188d-43c5-bbda-18a771ed65f8">
</p>

---

## 4. Buka Menu Sandi Aplikasi

Pada halaman Verifikasi 2 Langkah sampai ke bagian paling bawah. Di sana Anda akan menemukan menu Sandi Aplikasi (App Passwords). Klik menu tersebut.

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/064edbfc-a32f-47c5-83e7-7a5cb122294f">
</p>

---

## 5. Beri Nama dan Buat Sandi

Ketik nama aplikasi yang akan menggunakan SMTP ini (misalnya: Website WordPress atau Skrip Python) pada kolom yang disediakan agar Anda mudah mengingatnya di kemudian hari. Setelah itu, klik tombol Buat (Create).

<p align="center">
  <img width="500" src="https://github.com/user-attachments/assets/ca317995-527f-480c-95a9-8fab73ebcd3c">
</p>

---

## 6. Salin dan Simpan Password

Google akan menampilkan sebuah kotak berisi 16 karakter kode/huruf dalam latar belakang kuning. Salin (copy) kode tersebut tanpa spasi. Ini adalah password SMTP Anda.

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/db8cd76a-b83d-4501-aab5-bcb9e92d0e46">
</p>

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/dbd2ada2-a4df-4922-9c14-95d45dc5753d">
</p>

---

## Konfigurasi SMTP Google

Setelah mendapatkan 16 digit App Password tersebut, gunakan detail berikut pada pengaturan aplikasi Anda:

* SMTP Server: smtp.gmail.com
* Port: 465 (SSL) atau 587 (TLS/STARTTLS)
* Username: Alamat Gmail lengkap Anda
* Password: 16 digit App Password yang baru saja dibuat

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/815ed17f-b71e-4f50-a607-93d70f071d76">
</p>

---

# 🗄️ Setup MySQL Database

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/86642371-0989-416a-8065-5269c3cffe6e">
</p>

## Buat Database & User (E-Money Services)

```sql
CREATE DATABASE emoney CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE USER 'useremoney'@'%' IDENTIFIED BY 'Password#123';

GRANT ALL PRIVILEGES ON emoney.* TO 'useremoney'@'%';

FLUSH PRIVILEGES;

SHOW DATABASES;
USE emoney;
SELECT USER();
```

---

# 🌐 Setup Environment di Postman

## 1. Buka Postman

Pada menu sebelah kiri klik **Environments**.

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/a5cd8452-322b-48a7-8643-cd2111e04f64">
</p>

## 2. Buat Environment Baru

Klik icon **+** di kiri atas.

Nama environment:

```text
Firebase Auth Dev
```

Tambahkan variabel berikut:

| Variable          | Initial Value                               | Keterangan                    |
| ----------------- | ------------------------------------------- | ----------------------------- |
| FIREBASE_API_KEY  | AIzaSyB_xxx...                              | Web API Key dari Firebase     |
| FIREBASE_ID_TOKEN | *(kosong)*                                  | Otomatis terisi setelah login |
| BACKEND_BASE_URL  | http://localhost:8080/v1                    | Base URL backend              |
| BACKEND_TOKEN     | *(kosong)*                                  | JWT dari backend              |
| USER_EMAIL        | [test@example.com](mailto:test@example.com) | Email testing                 |
| USER_PASSWORD     | Test@12345                                  | Password testing              |

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/f6b77782-6bd6-4ad7-ac32-e06a890a083d">
</p>

---

# 📚 Implementation

Untuk proses Register hingga Verification refer:

https://github.com/afnan923/MobileApp2_week4_1123150074/blob/main/postman/step-step.md

---

# 1️⃣ Mengambil Data Profil User yang Sedang Login

### Endpoint

```http
GET {{BACKEND_BASE_URL}}/v1/auth/me
```

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/fc42e744-2423-4c38-b55d-101878de4a0d">
</p>

---

# 2️⃣ Update Firebase Token

### Endpoint

```http
PUT {{BACKEND_BASE_URL}}/v1/auth/fcm-token
```

### Request Body

```json
{
  "fcm_token": "cFcKwzP4S8u6IJ9k3l..."
}
```

Pastikan mempunyai Flutter App yang mempunyai Firebase Notification.

Contoh:

https://github.com/afnan923/flutter-notification-demo

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/33e391f4-90d3-412e-8900-502bca3dfeb3">
</p>

---

# 3️⃣ OTP — Firebase Notification

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/otp/send-firebase
```
### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

Mengirim kode OTP 6 digit ke device user melalui Firebase Cloud Messaging Push Notification.

OTP berlaku selama 5 menit.

Prasyarat: User harus sudah mendaftarkan FCM token.

| Postman                                                                                     | Device                                                                                                  |
| ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/752e9809-6a89-42da-9bc0-34874231118b"> | <img width="250" src="https://github.com/user-attachments/assets/e7241be4-6a7b-4c7d-9042-65bac13d57e6"> |

---

# 4️⃣ OTP Confirmation

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/otp/confirm
```

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

### Request Body Firebase

```json
{
  "code": "123456",
  "otp_type": "firebase"
}
```

| Device                                                                                                  | Postman                                                                                     |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img width="250" src="https://github.com/user-attachments/assets/d12a66a5-c180-4d5a-b458-545fb2536958"> | <img src="https://github.com/user-attachments/assets/b0fa9caf-4054-4713-a01b-1f79f612eefc"> |

### Request Body Email

```json
{
  "code": "123456",
  "otp_type": "email"
}
```

| Email OTP                                                                                   | Verification                                                                                |
| ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/25753e95-b2e4-4bdb-9cba-2441be4a115d"> | <img src="https://github.com/user-attachments/assets/1794991a-1e51-4b10-a1ae-e4f442d249fa"> |

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/6354941f-ddc9-495f-b654-d998b8ab113e">
</p>

---

# 5️⃣ OTP — Email Notification

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/otp/send-email
```

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

| Email OTP                                                                                   | Result                                                                                      |
| ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/affc193d-d2b3-4e4e-962d-af7d696408c3"> | <img src="https://github.com/user-attachments/assets/d7f442cc-d900-4788-9faa-4b0c320debcd"> |

---

# 6️⃣ OTP — TOTP Google Authenticator

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/otp/totp/register
```

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

<p align="center">
  <img width="700" src="https://github.com/user-attachments/assets/e835c841-15ee-4c97-88b5-d074c2b41aa4">
</p>

Setelah scan QR Code:

<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/5e15d76e-20c8-43e5-b722-91d0eac3bc74">
</p>

---

# 7️⃣ OTP — TOTP Verification

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/otp/totp/verify
```

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

### Request Body

```json
{
  "code": "123456"
}
```

| Google Authenticator                                                                                    | Verification                                                                                |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img width="250" src="https://github.com/user-attachments/assets/1ab20d4c-feea-42f4-bb64-ddc23d3a7b13"> | <img src="https://github.com/user-attachments/assets/ecc781f9-b3ba-4946-8e19-4c7c2d30ab5c"> |

---

# 8️⃣ Transfer Confirm

### Endpoint

```http
POST {{BACKEND_BASE_URL}}/v1/payment/transfer
```

Mendukung OTP Firebase, Email, dan TOTP.

### Headers

| Key           | Value                    |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | bearer {{BACKEND_TOKEN}} |

### OTP Firebase

```json
{
  "amount": 50000,
  "description": "Bayar makan siang",
  "otp_code": "123456",
  "otp_type": "firebase"
}
```

| Postman                                                                                     | Device                                                                                                  |
| ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/75a84247-b63a-45b3-af98-333dd55b2a67"> | <img width="250" src="https://github.com/user-attachments/assets/c67d46f8-33eb-4e62-8c05-21db48d69d59"> |

### OTP Email

```json
{
  "amount": 50000,
  "description": "Bayar makan siang",
  "otp_code": "123456",
  "otp_type": "email"
}
```

| Email OTP                                                                                   | Transfer Result                                                                             |
| ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/7e817ba3-d54b-4e40-aeb3-9716bc3522f8"> | <img src="https://github.com/user-attachments/assets/02ea1d04-3c5d-41de-aa12-abf5f8f913d9"> |

### OTP TOTP

```json
{
  "amount": 75000,
  "description": "Beli pulsa",
  "otp_code": "123456",
  "otp_type": "totp"
}
```

| Google Authenticator                                                                                    | Transfer Result                                                                             |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| <img width="250" src="https://github.com/user-attachments/assets/5ae835da-a730-4295-a87c-b2c01506d1b2"> | <img src="https://github.com/user-attachments/assets/50c6d3e3-636e-4830-9365-fe5c6f7b8263"> |
