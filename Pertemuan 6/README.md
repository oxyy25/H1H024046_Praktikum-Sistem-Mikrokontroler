# Pertemuan 6

## 6.5.4 Pertanyaan Praktikum

1. Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan interrupt!

    jawab : 
    proses perubahan kondisi LED    :
    1. saat program brjalan dengan normal,arduino akan terus menulis nilai ledstate ke  pin 13.
    2. Ketika tombol ditekan,maka pin 2 yang menghubungkan button dengan arduino akan berubah teagangannya dari HIGH ke LOW yang menyebabkan falling edge
    3. Falling edge tersebut memicu interrupt yang membuat program utama berhenti sementara.
    4. Arduino memanggil fungsi ISR tombolInterrupt(),yang membuat nilai ledstate dari true (hidup) ke false (mati) / sebaliknya.
    5. Setelah ISR selesai berjalan, program utama akan berjalan kembali dan menulis nilai ledstate yang baru

2. Apa fungsi attachInterrupt() pada program tersebut?

    Jawab : 

    untuk menghubungkan fungsi ISR ke pin Interrupt tertentu dan juga kondisi pemicu dari fungsi ISR untuk berjalan

3. Mengapa pada ISR tidak disarankan menggunakan delay() dan Serial.print()?

    Jawab   :
    
    delay() tidak disarankan untuk digunakan karena saat ISR sedang berjalan maka semua program berhenti sementara termasuk delay() yang membuat delay() bisa menjadi tidak akurat.

4. Apa fungsi keyword volatile pada variabel ledState?

    Jawab   :

    Memberi tahu compiler jika  nilai variabel tersebut dapat berubah kapan saja  diluar alur normal program.

5. Pada percobaan digunakan mode interrupt FALLING. Modifikasikan program menggunakan mode interrupt lain (RISING, CHANGE, atau LOW) kemudian:
    • Jelaskan perbedaan cara kerja masing-masing mode interrupt tersebut
    • Analisis perubahan perilaku LED yang terjadi pada setiap mode
    • Sertakan source code dan penjelasan program dalam bentuk README.md

    Jawab   :
    | Mode | kondisi pemicu | Perilaku LED |
    | ------- | ------- | ------- |
    | FALLING | berubah dari HIGH ke LOW (saat ditekan)| Toggle saat tombol ditekan |
    | RISING | sinyal dari LOW ke HiGH (saat dilepas)| Toggle saat tombol dilepas |
    | CHANGE | Sinyal berubah ke arah manapun | Toggle saat ditekan DAN saat dilepas (2x lebih sering) |
    | LOW | Selama sinyal masih lOW (tombol ditahan) | ISR dipanggil terus-menerus selama tombol ditahan (LED berkedip sangat cepat / tidak stabil) |

```
volatile bool ledState = false; // variabel volatile yang dapat diubah ISR

void tombolInterrupt() {
  ledState = !ledState; // Toggle setiap ada perubahan sinyal
}

void setup() {
    
  pinMode(13, OUTPUT); // konfigurasi pin lED
  //konfigurasi pin button
  pinMode(2, INPUT_PULLUP);
  // konfigurasi ISR ke pin 2
  attachInterrupt(
    digitalPinToInterrupt(2),
    tombolInterrupt,
    CHANGE  //tinggal ubah ke rising,change / low
  );
}

void loop() {
  digitalWrite(13, ledState);
}
```



## 6.6.4 Pertanyaan Praktikum

1. Jelaskan bagaimana fungsi millis() bekerja pada program tersebut!


    Jawab   :
    
    Mengembalikan jumlah ms sejak arduino pertama kali menyala, yang akan terus bertambah  di background menggunakan timer0 hardwarea

2. Apa perbedaan utama antara delay() dan millis()?

    Jawab   :
    
    perbedaan dari delay() dan milis() adalah cara kerja dari delay() adalah memblokir CPU untuk melakukan tugas selama durasi delay().Sedangkan milis() cara kerjanya adalah mencatat waktu yang membuat CPU tetap bisa menjalakan tugasnya.

3. Mengapa metode millis() disebut non-blocking?

    Jawab   :

    Kareana milis() tidak menghentikan CPU untuk mengkesekusi program,program terus berjalan di loop().Berbeda dengan delay() yang menghentikan CPU untuk mengkeksekusi program sampai waktu delay habis.

4. Modifikasi program agar:
    • LED pertama berkedip setiap 1 detik
    • LED kedua berkedip setiap 500 ms
    • Tanpa menggunakan delay()
    Berikan penjelasan setiap baris program dalam bentuk README.md.

    Jawab   :
    
```
//Konfigurasi LED 1 
unsigned long previousMillis1 = 0;  // Waktu terakhir LED1 berubah
const long interval1 = 1000;        // Interval LED1: 1000 ms
bool ledState1 = false;             // Status LED1

//Konfigurasi LED 2 (Pin 12)
unsigned long previousMillis2 = 0;  // Waktu terakhir LED2 berubah
const long interval2 = 500;         // Interval LED2: 500 ms
bool ledState2 = false;             // Status LED2

void setup() {
  pinMode(13, OUTPUT); // LED1 sebagai output
  pinMode(12, OUTPUT); // LED2 sebagai output
}

void loop() {
  unsigned long currentMillis = millis(); // Ambil waktu saat ini

  //Logika LED 1 (setiap 1000 ms) 
  if (currentMillis - previousMillis1 >= interval1) {
    previousMillis1 = currentMillis;  // Simpan waktu terakhir LED1 berubah
    ledState1 = !ledState1;           // Toggle LED1
    digitalWrite(13, ledState1);      // Tulis ke pin LED1
  }

  //Logika LED 2 (setiap 500 ms) 
  if (currentMillis - previousMillis2 >= interval2) {
    previousMillis2 = currentMillis;  // Simpan waktu terakhir LED2 berubah
    ledState2 = !ledState2;           // Toggle LED2
    digitalWrite(12, ledState2);      // Tulis ke pin LED2
  }
}
```

