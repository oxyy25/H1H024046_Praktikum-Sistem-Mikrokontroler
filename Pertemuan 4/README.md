# Pertemuan 4

## 4.5.4 Pertanyaan Praktikum

1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?

    jawab : 

   analogRead() berfungsi untuk membaca tengangan yang masuk dari potensiometer pada pin A0,nilai analog yang diterima dari potensiometer akan dikonversi ke nilai digital menggunakan ADC pada Arduino UNO

2. Mengapa diperlukan fungsi map() dalam program tersebut?

    Jawab : 
     
    Fungsi Map() digunakan untuk mengonversikan nilai dari potensiometer (0 - 1023) menjadi nilai sudut  0°–180° agar servo dapat bergerak dengan benar. Keduanya punya skala yang berbeda jadi harus dikonversikan terlebih dahulu

3. Modifikasi program berikut agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun potensiometer tetap memiliki rentang ADC 0–1023. Jelaskan program pada file README.md
    Jawab  :

```
#include <Servo.h> // library untuk servo motor

Servo myservo; // membuat objek servo

// Pin Setup
const int potensioPin = A0;   // pin analog input
const int servoPin = 9;       // pin PWM servo

// Inisiasi Variabel
int pos = 0; // sudut servo
int val = 0; // nilai ADC

void setup() {

  // Hubungkan servo ke pin
  myservo.attach(servoPin);

  // Serial monitor
  Serial.begin(9600);

}

void loop() {

  //Pembacaan ADC 
  val = analogRead(potensioPin);

  //Konversi Data
  pos = map(val,
            0,    // min ADC
            1023, // max ADC
            30,    // sudut min 
            150   // sudut max
            ); // megubah sudut minimal ke 30 ° dan maksimal ke 150°

  // Output Servo
  myservo.write(pos);

  //MONITORING 
  Serial.print("ADC Potensio: ");
  Serial.print(val);

  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  delay(100);

}
```
![Demo soal 3A](soal3_percobaan4a.gif)

## 4.6.4 Pertanyaan Praktikum

1. Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!

    Jawab   :
    
    analogWrite() sebenarnya hanya bisa digunakan untuk menyalakan LED dan mematikan LED saja.Tetapi dengan bantuan PWM  LED dapat dinyalakan dan dimatikan dengan sangat cepat yang menghasilkan efek redup 

2. Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?

    Jawab   :
    
    keduanya punya resolusi yang berbeda.ADC punya resolusi 10 bit (0 - 1023) sedangkan PWM punya resolusi 8 bit (0 - 255).Karena nilai max dari ADC 4x lebih besar dari nilai max PWM,maka untuk mengonversikan nilai input dari potensiometer (ADC) ke nilai keceraharan LED (PWM) maka nilai ADC harus dibagi dengan 4 agar LED dapat berjalan dengan benar.

3. Modifikasilah program berikut agar LED hanya menyala pada rentang kecerahan sedang, yaitu hanya ketika nilai PWM berada pada rentang 50 sampai 200. Jelaskan program pada file README.md.

Jawab   :
    
```
#//  Pin Setup 
const int potPin = A0;
const int ledPin = 9;

// Inisiasi Variabel
int nilaiADC = 0;
int pwm = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {

  //  Pembacaaan Nilai Potensiometer 
  nilaiADC = analogRead(potPin);

  // Pemrosesan Data
  pwm = map(nilaiADC,
            0,
            1023,
            0,
            255
            );

  // Output PWM (Dengan Batasan)
  if (pwm >= 50 && pwm <= 200) {
    analogWrite(ledPin, pwm);   // Nyalakan LED hanya di rentang 50–200
  } else {
    analogWrite(ledPin, 0);     // Di luar rentang PWM = LED mati
  }

  // Monitoring
  Serial.print("ADC: ");
  Serial.print(nilaiADC);

  Serial.print(" | PWM: ");
  Serial.print(pwm);

  Serial.print(" | LED: ");
  Serial.println((pwm >= 50 && pwm <= 200) ? "NYALA" : "MATI");

  delay(50);
}
```
![Demo soal 3A](soal3_percobaan4b.gif)

