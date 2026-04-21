# Pertemuan 3

## 3.5.4 Pertanyaan Praktikum

1. Jelaskan proses dari input keyboard hingga LED menyala/mati!

    jawab : 

    ketika mengetik '1' atau '0' lalu menekan enter. karakter tsb akan di kirim ke arduino lewat usb menggunakan komunikasi serial UART.Fungsi loop() yg terus berjalan akan memdeteksi adanya data masuk melalui Serial.available() > 0 dan akan dibaca melalui Serial.read() lalu tersimpan di variabel data.Jika data == '1' maka lampu LED akan hidup dan ketika data == '0' maka lampu LED akan mati.Sedangkan karakter lain seperti '\n' dan '\r' akan diabaikan.Input yang tidak dikenal akan mencetak pesan peringatan kembali ke serial monitor.

2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika
baris tersebut dihilangkan?

    Jawab : 
     
    Serial.availble() digunakan untuk melakukan pengecekan apakah ada data yang masuk ke buffer serial sebelum mencoba membaca.jika Serial.available() dihapus maka program akan terus mencetak perintah tak dikenal karena tidak dilakukan pengecekan diawal.

3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi
jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan
berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

    Jawab  :

```
const int PIN_LED = 12;
bool blinkMode = false;
unsigned long previousMillis = 0;
const long interval = 500; // interval berkedip 500ms
bool ledState = false;

void setup()
{
  Serial.begin(9600);
  Serial.println("ketik '1' untuk menyalakan LED");
  Serial.println("ketik '0' untuk mematikan LED");
  Serial.println("ketik '2' untuk mode berkedip");
  pinMode(PIN_LED, OUTPUT);
}

void loop()
{
  // Cek input serial
  if(Serial.available() > 0){
    char data = Serial.read();
    // Menyalakan LED
    if(data == '1') {
      blinkMode = false;
      digitalWrite(PIN_LED, HIGH);
      Serial.println("LED ON");
    } // Mematikan LED
    else if(data == '0') {
      blinkMode = false;
      digitalWrite(PIN_LED, LOW);
      Serial.println("LED OFF");
    } // LED berikedip
    else if(data == '2') {
      blinkMode = true;
      Serial.println("LED BLINK MODE ON");
    }
    else if(data != '\n' && data != '\r') {
      Serial.println("Perintah tidak dikenal");
    }
  }

  // Jika mode berkedip aktif, jalankan blink
  if(blinkMode){
    unsigned long currentMillis = millis();
    if(currentMillis - previousMillis >= interval){
      previousMillis = currentMillis;
      ledState = !ledState; // toggle LED
      digitalWrite(PIN_LED, ledState);
    }
  }
}
```

4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap
sistem

    Jawab   :

    Lebih baik menggukana milis(),karena jika menggunakan delay() arduino tidak dapat membaca input dari serial saat jeda delay.Jika menggunakan milis() arduino tetap dapat membaca input serial kapanpun.

## 3.6.4 Pertanyaan Praktikum

1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian
tersebut!

    Jawab   :
    
    Komunikasi antara I2C dan LCD  melalui 2 kabel data,yaitu SDA (data) dan SCL (clock).Arduino sebagai master dan I2C LCD sebagai slave dengan alamat 0x27.Ketika lcd.init() dipanggil, Arduino mengirim sinyal melalui library Wire.h ke alamat 0x27 untuk menginisialisasi LCD, lalu setiap perintah seperti lcd.print() atau lcd.setCursor() diterjemahkan menjadi paket data serial yang dikirim melalui pin SDA dan SCL

2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan
pin kanan tertukar!

    Jawab   :
    
    Penukaran pin kiri dan pin kanan tidak merusak rangkaian hanya membuat arah pembaca nilai ADC terbaik(cukup ubah logika pemerograman jika ingin hasilnya sama dengan saat pin tidak dibalik)

3. Modifikasi program dengan menggabungkan antara UART dan I2C (keduanya sebagai
output) sehingga:
    - Data tidak hanya ditampilkan di LCD tetapi juga di Serial Monitor
    - Adapun data yang ditampilkan pada Serial Monitor sesuai dengan table berikut:
    ADC: 0 Volt: 0.00 V Persen: 0%
    Tampilan jika potensiometer dalam kondisi diputar paling kiri
    - ADC: 0 0% | setCursor(0, 0) dan Bar (level) | setCursor(0, 1)
    - Berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

Jawab   :
    
```
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Inisialisasi LCD I2C dengan alamat 0x27, ukuran 16 kolom 2 baris
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pin potensiometer terhubung ke A0
const int pinpot = A0;

void setup() {
  Serial.begin(9600);       // Inisialisasi komunikasi UART dengan baud rate 9600
  lcd.init();               // Inisialisasi LCD via I2C
  lcd.backlight();          // Nyalakan backlight LCD
}

void loop() {
  // Baca nilai ADC dari potensiometer (0 - 1023)
  int nilai = analogRead(pinpot);

  // Konversi ADC ke tegangan (0.00 - 5.00 V)
  float volt = nilai * (5.0 / 1023.0);

  // Konversi ADC ke persen (0 - 100%)
  int persen = map(nilai, 0, 1023, 0, 100);

  // Konversi ADC ke panjang bar LCD (0 - 16 karakter)
  int panjangBar = map(nilai, 0, 1023, 0, 16);

  // Tampilkan data ke Serial Monitor dalam format tabel 3 kolom
  Serial.print("ADC: ");
  Serial.print(nilai);
  Serial.print("\t");       // Tab sebagai pemisah kolom
  Serial.print("Volt: ");
  Serial.print(volt, 2);    // Tampilkan 2 angka desimal
  Serial.print(" V\t");
  Serial.print("Persen: ");
  Serial.print(persen);
  Serial.println("%");

  // Baris 0: Tampilkan nilai ADC dan tegangan
  lcd.setCursor(0, 0);      // Pindah cursor ke kolom 0, baris 0
  lcd.print("ADC:");
  lcd.print(nilai);
  lcd.print(" ");
  lcd.print(persen);
  lcd.print("%  ");         // Spasi untuk hapus sisa karakter lama

  // Baris 1: Tampilkan bar level
  lcd.setCursor(0, 1);      // Pindah cursor ke kolom 0, baris 1
  for (int i = 0; i < 16; i++) {
    if (i < panjangBar) {
      lcd.print((char)255); // Karakter blok penuh sebagai bar
    } else {
      lcd.print(" ");       // Spasi untuk hapus sisa bar lama
    }
  }

  delay(200); // Tunda 200ms sebelum pembacaan berikutnya
}
```

4. Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor
ADC Volt (V) Persen (%)

    Jawab   :
    
    | ADC | Volt (V) | Persen (%) |
    | :--- | :---: | ---: |
    | 1 | 0.00 V | 0 % |
    | 21 | 0.10 V | 2 % |
    | 49 | 0.24 V | 5 % |
    | 74 | 0.36 V | 7 % |
    | 96 | 0.47 V | 9 % |

