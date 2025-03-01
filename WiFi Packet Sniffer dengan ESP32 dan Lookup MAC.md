# **WiFi Packet Sniffer dengan Mode Promiscuousdan dan Lookup MAC Online**

## **Pendahuluan**
ESP32 merupakan mikrokontroler yang dilengkapi dengan modul WiFi, sehingga dapat digunakan untuk berbagai proyek jaringan, salah satunya adalah **WiFi Packet Sniffer**. Program ini memungkinkan ESP32 untuk menangkap paket WiFi yang beredar di sekitarnya menggunakan mode **promiscuous** dan menampilkan alamat MAC perangkat yang terdeteksi.

Selain itu, program ini juga dilengkapi fitur **lookup MAC Address online**, yang memungkinkan pengguna mendapatkan informasi produsen perangkat berdasarkan alamat MAC yang ditangkap.

## **Fitur Utama**
- Menangkap paket WiFi di sekitar menggunakan mode **promiscuous**.
- Mengekstrak alamat **MAC** dari perangkat yang terdeteksi.
- Menampilkan daftar MAC Address yang ditemukan.
- Melakukan **lookup MAC Address online** untuk mengetahui informasi produsen perangkat.
- Menyimpan dan menampilkan hasil lookup.

## **Cara Kerja Program**

1. **Menghubungkan ESP32 ke Jaringan WiFi**  
   Setelah program dijalankan, ESP32 akan terhubung ke jaringan WiFi yang telah dikonfigurasi dalam kode.
   
2. **Mengaktifkan Mode Promiscuous**  
   ESP32 akan masuk ke **mode promiscuous**, yang memungkinkan perangkat menangkap semua paket WiFi di sekitarnya tanpa harus terhubung ke perangkat tertentu.

3. **Menangkap dan Menampilkan Alamat MAC**  
   Setiap kali sebuah paket tertangkap, program akan mengambil alamat MAC sumber dari paket tersebut dan menyimpannya dalam daftar **sniffedMacs** jika belum pernah ditemukan sebelumnya.
   
4. **Lookup MAC Address Secara Online**  
   Setelah **1 menit** sniffing, mode promiscuous dimatikan, dan setiap MAC Address yang ditemukan akan dikirim ke layanan **maclookup.app** untuk mendapatkan informasi lebih lanjut mengenai perangkat tersebut.
   
5. **Menampilkan dan Menyimpan Hasil Lookup**  
   Jika sebuah MAC Address berhasil ditemukan dalam database online, informasi tersebut akan disimpan dan ditampilkan di serial monitor.

6. **Mengulangi Proses**  
   Setelah lookup selesai, program akan menghapus daftar MAC yang telah diproses dan memulai siklus sniffing berikutnya.

## **Kode Program**
```cpp
#include <Arduino.h>
#include <WiFi.h>
#include <HTTPClient.h>
#include <ArduinoJson.h>
#include <unordered_set>
#include <esp_wifi.h>

// Konfigurasi WiFi
const char* ssid = "shitmen";
const char* password = "wey12345678";

// URL untuk lookup MAC
const char* macLookupURL = "https://api.maclookup.app/v2/macs/";

// Struktur untuk menyimpan hasil lookup MAC
struct MacEntry {
    std::string mac;
    std::string data;
};

// List penyimpanan MAC yang ditemukan
std::unordered_set<std::string> sniffedMacs;
std::unordered_set<std::string> lookedUpMacs;
std::vector<MacEntry> foundMacs;
unsigned long sniffingStartTime;
const unsigned long sniffingDuration = 60000; // 1 menit dalam milidetik

// Fungsi untuk melakukan lookup MAC secara online
std::string lookupMac(const std::string& mac) {
    HTTPClient http;
    http.begin(String(macLookupURL) + mac.c_str());
    int httpResponseCode = http.GET();
    
    if (httpResponseCode > 0) {
        String response = http.getString();
        http.end();
        return std::string(response.c_str());
    } else {
        http.end();
        return "";
    }
}

// Fungsi untuk menangani paket yang masuk
void packetSniffer(void* buf, wifi_promiscuous_pkt_type_t type) {
    wifi_promiscuous_pkt_t* pkt = (wifi_promiscuous_pkt_t*)buf;
    uint8_t* macAddr = pkt->payload + 10;
    char macStr[18];
    snprintf(macStr, sizeof(macStr), "%02X:%02X:%02X:%02X:%02X:%02X", 
             macAddr[0], macAddr[1], macAddr[2], macAddr[3], macAddr[4], macAddr[5]);
    
    std::string macAddress(macStr);
    
    // Tambahkan hanya jika MAC belum ada dalam daftar
    if (sniffedMacs.find(macAddress) == sniffedMacs.end()) {
        sniffedMacs.insert(macAddress);
        Serial.printf("📡 MAC terdeteksi: %s\n", macAddress.c_str());
    }
}

void setup() {
    Serial.begin(115200);
    delay(1000);

    // Menghubungkan ke WiFi
    WiFi.begin(ssid, password);
    Serial.printf("\n🔗 Menghubungkan ke WiFi: %s\n", ssid);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }
    Serial.println("\n✅ Terhubung ke WiFi!");
    
    // Memulai mode promiscuous
    WiFi.mode(WIFI_STA);
    esp_wifi_set_promiscuous(true);
    esp_wifi_set_promiscuous_rx_cb(&packetSniffer);
    
    sniffingStartTime = millis();
}

void loop() {
    if (millis() - sniffingStartTime >= sniffingDuration) {
        // Selesai sniffing, hentikan mode promiscuous
        esp_wifi_set_promiscuous(false);
        Serial.println("\n🔎 Selesai sniffing, mulai lookup...");

        for (const auto& mac : sniffedMacs) {
            if (lookedUpMacs.find(mac) == lookedUpMacs.end()) {
                Serial.printf("🔍 Melakukan lookup MAC: %s\n", mac.c_str());
                std::string response = lookupMac(mac);
                
                if (!response.empty()) {
                    StaticJsonDocument<512> doc;
                    deserializeJson(doc, response);
                    
                    if (doc["found"].as<bool>()) {
                        foundMacs.push_back({mac, response});
                        Serial.printf("✨ MAC ditemukan dan disimpan!\n");
                    }
                }
                // Tandai MAC ini sudah pernah dilookup
                lookedUpMacs.insert(mac);
            }
        }
        
        // Menampilkan hanya MAC yang ditemukan (found: true)
        Serial.println("\n📜 Daftar MAC yang ditemukan:");
        for (const auto& entry : foundMacs) {
            Serial.printf("MAC: %s | Data: %s\n", entry.mac.c_str(), entry.data.c_str());
        }
        
        // Reset untuk siklus berikutnya
        sniffedMacs.clear();
        sniffingStartTime = millis();
        esp_wifi_set_promiscuous(true);
    }
}
```

## **Hasil Serial Monitor**
![image](https://github.com/user-attachments/assets/7ba28ffe-1984-4e97-92ee-b7b68443a214)

## **Kesimpulan**
Proyek ini memungkinkan ESP32 untuk berfungsi sebagai **WiFi Packet Sniffer**, yang dapat digunakan untuk **keamanan jaringan, troubleshooting, atau penelitian lebih lanjut**. Gunakan fitur ini dengan bijak dan sesuai dengan regulasi yang berlaku.

---
✉ Jika ada pertanyaan atau ingin berkontribusi, silakan ajukan pull request atau diskusi di issue! 🚀
