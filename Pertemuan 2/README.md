# Pertemuan 2

## 2.5.4 Pertanyaan Praktikum

1. Gambarkan rangkaian schematic yang digunakan pada percobaan! 
2. Apa yang terjadi jika nilai num lebih dari 15?
3. Apakah program ini menggunakan common cathode atau common anode? Jelaskan alasanya!
4. Modifikasi program agar tampilan berjalan dari F ke 0 dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md

Jawab   :
1. https://www.tinkercad.com/things/a8w9dzbQf1t-7-segment-dengan-loop?sharecode=dFgNptx6zdfYy8iXQEtkbpaAeKHNpVpVWE3dMYYBpfs

2. Program akan tetap berjalan normal tetapi saat program dijalankan akan membaca data di luar array karena data pada array hanya ada dari 0 - 15 saja.Tampilan pada seven segment pun akan menampilkan data sampah atau data acak karena membaca data di luar array.

3. Program dirancang menggunakan common anode karena pada eksekusi program menggunakan operator "!" yang berarti logika terbalik. Pada common anode, logika terbalik digunakan untuk menyalakan LED. Jika menggunakan common cathode, logika terbalik tidak digunakan untuk menyalakan LED.

4.  kode :
```
#include <Arduino.h>

// 7-Segment Common Anode

// Pin mapping segment: a b c d e f g dp
const int segmentPins[8] = {7, 6, 5, 11, 10, 8, 9, 4};

byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, //0
  {0,1,1,0,0,0,0,0}, //1
  {1,1,0,1,1,0,1,0}, //2
  {1,1,1,1,0,0,1,0}, //3 
  {0,1,1,0,0,1,1,0}, //4
  {1,0,1,1,0,1,1,0}, //5
  {1,0,1,1,1,1,1,0}, //6
  {1,1,1,0,0,0,0,0}, //7
  {1,1,1,1,1,1,1,0}, //8
  {1,1,1,1,0,1,1,0}, //9
  {1,1,1,0,1,1,1,0}, //A
  {0,0,1,1,1,1,1,0}, //b
  {1,0,0,1,1,1,0,0}, //C
  {0,1,1,1,1,0,1,0}, //d
  {1,0,0,1,1,1,1,0}, //E
  {1,0,0,0,1,1,1,0}  //F
};

// Fungsi tampil digit (dibalik untuk CA)
void displayDigit(int num)
{
  for(int i=0; i<8; i++)
  {
    digitalWrite(segmentPins[i], !digitPattern[num][i]); // <-- dibalik
  }
}

void setup()
{
  for(int i=0; i<8; i++)
  {
    pinMode(segmentPins[i], OUTPUT);
  }
}

void loop()
{
  for(int i=15; i >= 0; i--)
  {
    displayDigit(i);
    delay(1000);
  }
}
```

## 2.6.4 Pertanyaan Praktikum

1. Gambarkan rangkaian schematic yang digunakan pada percobaan

2. Mengapa pada push button digunakan mode INPUT_PULLUP pada Arduino Uno? Apa keuntungannya dibandingkan rangkaian biasa?

3. Jika salah satu LED segmen tidak menyala, apa saja kemungkinan penyebabnya dari sisi hardware maupun software?

4. Modifikasi rangkaian dan program dengan dua push button yang berfungsi sebagai penambahan (increment) dan pengurangan (decrement) pada sistem counter dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

Jawab   :

1. https://www.tinkercad.com/things/hvRydhFMAKi-push-button-7-segment?sharecode=DtekXoD9eg0xFlhC2cjuxwnNruDPqdSRHDGoFB_ZWVw

2. Jika menggunakan mode INPUT biasa tanpa menambahkan resistor eksternal, pin Arduino akan berada dalam kondisi floating (mengambang) saat tombol tidak ditekan.Saat dalam kondisi mengambang,pin sangat sensitif terhadap gangguan elektrik.

3. dari sisi hardware : kabel jumper yang menghubungkan pin digital dan seven segment tidak terpasang dengan sempurna atau kabel jumper rusak.Bisa juga karena salah satu LED pada seven segment mengalami kerusakan.

dari sisi software  : Lupa mengantur pin yang akan di gunakan menggunakan pinmode(). atau bisa juga urutan deklarasi pin pada segmentPins[] tidak sesuai urutan kabel yang terpasang.

4.  Kode    : ini belum bener
```
// Pin segmen 7-segment: urutan a, b, c, d, e, f, g, dp
const int segmentPins[8] = {7, 6, 5, 10, 11, 8, 9, 4};
// Array berisi 8 pin digital yang terhubung ke setiap segmen + titik desimal

const int buttonIncPin = 3;   // Pin push button INCREMENT (tambah)
const int buttonDecPin = 2;   // Pin push button DECREMENT (kurang)

int counter = 0;
// Menyimpan nilai counter saat ini, dimulai dari 0

bool lastButtonIncState = HIGH;
// Menyimpan kondisi tombol increment sebelumnya (HIGH = tidak ditekan, karena INPUT_PULLUP)

bool lastButtonDecState = HIGH;
// Menyimpan kondisi tombol decrement sebelumnya

// 1 = segmen Menyala, 0 = segmen Mati
byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, // 0
  {0,1,1,0,0,0,0,0}, // 1
  {1,1,0,1,1,0,1,0}, // 2
  {1,1,1,1,0,0,1,0}, // 3
  {0,1,1,0,0,1,1,0}, // 4
  {1,0,1,1,0,1,1,0}, // 5
  {1,0,1,1,1,1,1,0}, // 6
  {1,1,1,0,0,0,0,0}, // 7
  {1,1,1,1,1,1,1,0}, // 8
  {1,1,1,1,0,1,1,0}, // 9
  {1,1,1,0,1,1,1,0}, // A
  {0,0,1,1,1,1,1,0}, // b
  {1,0,0,1,1,1,0,0}, // C
  {0,1,1,1,1,0,1,0}, // d
  {1,0,0,1,1,1,1,0}, // E
  {1,0,0,0,1,1,1,0}  // F
};

void displayDigit(int num)
// Fungsi untuk menampilkan angka 'num' pada 7-segment display
{
  for(int i = 0; i < 8; i++)
  // Loop melalui semua 8 pin segmen (a sampai dp)
  {
    digitalWrite(segmentPins[i], !digitPattern[num][i]);
    // Tulis ke pin: tanda '!' membalik nilai karena display common anode
    // (common anode = LOW = menyala, HIGH = mati)
  }
}

void setup()
{
  for(int i = 0; i < 8; i++)
  // Loop mengatur semua 8 pin segmen sebagai OUTPUT
  {
    pinMode(segmentPins[i], OUTPUT);
    // Jadikan setiap pin segmen sebagai pin output
  }

  pinMode(buttonIncPin, INPUT_PULLUP);
  // Tombol increment: INPUT_PULLUP = aktif LOW, tidak perlu resistor eksternal
  // Pin akan HIGH saat tombol tidak ditekan, LOW saat ditekan

  pinMode(buttonDecPin, INPUT_PULLUP);
  // Tombol decrement: sama seperti di atas

  displayDigit(counter);
  // Tampilkan nilai awal counter (0) di 7-segment saat program pertama kali berjalan
}

void loop()
{
  bool currentIncState = digitalRead(buttonIncPin);
  // Baca kondisi tombol increment saat ini (HIGH atau LOW)

  bool currentDecState = digitalRead(buttonDecPin);
  // Baca kondisi tombol decrement saat ini

  //DETEKSI TOMBOL INCREMENT 
  if (lastButtonIncState == HIGH && currentIncState == LOW)
  // Kondisi TRUE hanya saat tombol BARU ditekan (transisi HIGH ke LOW)
  // Mencegah counter terus bertambah selama tombol ditahan
  {
    counter++;
    // Tambah nilai counter sebesar 1

    if (counter > 15) counter = 0;
    // Jika melebihi F (15), kembali ke 0 (wrap-around / counter hex)

    displayDigit(counter);
    // Perbarui tampilan 7-segment dengan nilai counter terbaru

    delay(200);
    // Tunda 200ms sebagai debounce sederhana (mencegah pembacaan ganda akibat bouncing mekanis)
  }

  //DETEKSI TOMBOL DECREMENT 
  if (lastButtonDecState == HIGH && currentDecState == LOW)
  // Kondisi TRUE hanya saat tombol decrement BARU ditekan
  {
    counter--;
    // Kurangi nilai counter sebesar 1

    if (counter < 0) counter = 15;
    // Jika kurang dari 0, wrap-around ke F (15) — counter berjalan mundur melingkar

    displayDigit(counter);
    // Perbarui tampilan 7-segment dengan nilai counter terbaru

    delay(200);
    // Tunda 200ms sebagai debounce untuk tombol decrement
  }

  lastButtonIncState = currentIncState;
  // Simpan kondisi tombol increment saat ini untuk dibandingkan pada iterasi berikutnya

  lastButtonDecState = currentDecState;
  // Simpan kondisi tombol decrement saat ini untuk dibandingkan pada iterasi berikutnya
}
```
