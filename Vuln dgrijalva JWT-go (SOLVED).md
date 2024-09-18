
## Deskripsi
Dependabot mendeteksi kerentanan di library `github.com/dgrijalva/jwt-go`, yang memungkinkan **Authorization Bypass**. Kerentanan ini terjadi jika token JWT berisi nilai `aud` yang kosong dalam format `[]string{}`. Karena validasi `aud` gagal, penyerang bisa mem-bypass akses yang seharusnya terbatas. Tidak ada patch yang disediakan untuk versi ini. Oleh karena itu, pengguna **jwt-go** disarankan untuk bermigrasi ke library yang lebih aman, yaitu `github.com/golang-jwt/jwt` versi **3.2.1** atau yang lebih baru.

![image](https://github.com/user-attachments/assets/cf31c52f-0e2b-4e82-9beb-8ccf5c31ed06)

### Kerentanan yang Terdeteksi:
- **Repository**: `github.com/dgrijalva/jwt-go`
- **Versi Terdampak**: >= 0.0.0-20150717181359-44718f8a89b0, <= 3.2.0
- **Versi yang Tersedia untuk Perbaikan**: **Tidak Ada**
- **Solusi yang Direkomendasikan**: Migrasi ke `github.com/golang-jwt/jwt` versi **3.2.1** atau lebih baru.

Kerentanan ini bisa menyebabkan masalah keamanan jika layanan yang menggunakan token JWT tidak memiliki validasi audien sendiri.

## Langkah-langkah Migrasi dari `jwt-go` ke `golang-jwt`

### 1. **Hapus Dependensi Lama (`github.com/dgrijalva/jwt-go`)**

Sebelum migrasi, hapus library lama `jwt-go` yang mengandung kerentanan.

#### Langkah-langkah:
- Buka file `go.mod` di root proyek Anda.
- Hapus baris yang mengimpor `github.com/dgrijalva/jwt-go`.

Contoh sebelum dihapus:
```go
require (
    github.com/dgrijalva/jwt-go v3.2.0
)
```

### 2. **Install Library Baru (`github.com/golang-jwt/jwt/v4`)**

Setelah menghapus dependensi lama, install versi yang lebih aman dari library JWT, yaitu `github.com/golang-jwt/jwt/v4`.

#### Langkah-langkah:

-   Jalankan perintah ini untuk menambahkan library baru:

```go
go get github.com/golang-jwt/jwt/v4` 
```

-   File `go.mod` akan diperbarui secara otomatis dengan dependensi baru:

```go
require (
    github.com/golang-jwt/jwt/v4 v4.0.0
)
```

### 3. **Ganti Import Library Lama di Kode**

Setelah library baru diinstall, lakukan perubahan pada seluruh kode yang menggunakan `github.com/dgrijalva/jwt-go` ke `github.com/golang-jwt/jwt/v4`.

#### Contoh Sebelum Migrasi:
```go
import "github.com/dgrijalva/jwt-go"
```

#### Setelah Migrasi:
```go
import "github.com/golang-jwt/jwt/v4"
```

### 4. **Sesuaikan Penggunaan JWT di Kode**

Beberapa API mungkin sedikit berubah dalam versi baru. Sesuaikan kode sesuai dengan API baru.

#### Contoh Penggunaan Baru:

-   **Membuat Token JWT:**

```go
token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
	"aud": "your-audience",
	"exp": time.Now().Add(time.Hour * 72).Unix(),
	"iss": "your-issuer",
})

tokenString, err := token.SignedString([]byte("your-256-bit-secret"))
if err != nil {
	fmt.Println("Error signing token:", err)
}
```

-   **Memverifikasi Token JWT:**



```go
token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
	// Validasi metode tanda tangan
	if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
		return nil, fmt.Errorf("unexpected signing method: %v", token.Header["alg"])
	}
	return []byte("your-256-bit-secret"), nil
})

if err != nil {
	fmt.Println("Error parsing token:", err)
	return
}

if claims, ok := token.Claims.(jwt.MapClaims); ok && token.Valid {
	fmt.Printf("Token valid, claims: %v\n", claims)
} else {
	fmt.Println("Invalid token")
}
```

### 5. **Bersihkan Dependensi yang Tidak Digunakan**

Setelah semua kode diperbarui, jalankan perintah berikut untuk membersihkan dependensi yang tidak digunakan, termasuk `jwt-go` yang lama:

```go
go mod tidy
```

### 6. **Uji Aplikasi**

Setelah melakukan migrasi, jalankan pengujian untuk memastikan bahwa token JWT berfungsi dengan baik dan aplikasi tetap berjalan seperti yang diharapkan. Pastikan implementasi token aman dan tidak ada kerentanan yang tersisa.

## Kesimpulan

Dengan bermigrasi dari `github.com/dgrijalva/jwt-go` ke `github.com/golang-jwt/jwt/v4`, Anda telah mengatasi kerentanan keamanan yang memungkinkan bypass otorisasi. Proses ini memastikan aplikasi Anda tetap aman dari potensi eksploitasi yang terkait dengan JWT. Pastikan untuk selalu menjaga dependensi proyek tetap mutakhir dan mengikuti best practice keamanan.

![image](https://github.com/user-attachments/assets/fa49bbb4-dda5-4dfc-a5b3-3c32bbc5e20a)

