# Pertemuan 5

## 5.5.4 Pertanyaan Praktikum

1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!

    jawab : 

    Ketiga task berjalan secara bergantian,tetapi pergantiannya sangat cepat yang membuat 3 task tersebut terasa berjalan secara bersamaan.Ketiga task tersebut diatur jadwal eksekusinya menggunakan scheduler.Mekanismenya adalah ketika task masuk ke delay, maka sisa waktu eksekusinya akan dipindahkan ke task lain yang sudah siap berjalan.

2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!

    Jawab : 

    1. Mendeklasarikan fungsi task diawal program 
        ```
        void Taskfour(void *pvParameters);
        ```

    2. Daftarkan task yang sudah dibuat ke dalam setup
        ```
        xTaskCreate(
            Taskfour,       // nama fungsi task
            "task4",       // nama task (string)
            128,           // ukuran stack (bytes)
            NULL,          // parameter yang diteruskan ke task
            1,             // prioritas task
            NULL           // handle task (opsional)
        );
        ```
    
    3. Buat definisi fungsi task (contoh : menyalakan dan mematikan LED)
        ```
        void Taskfour(void *pvParameters) {
            pinMode(6, OUTPUT); 
            while(1) {
                Serial.println("Task4");
                digitalWrite(6, HIGH);
                vTaskDelay(400 / portTICK_PERIOD_MS);
                digitalWrite(6, LOW);
                vTaskDelay(400 / portTICK_PERIOD_MS);
                }
            }
        ```

3. Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya? Jelaskan program pada file README.md.
    
    Jawab  :

    ```
    #include <Arduino_FreeRTOS.h>

    // Variabel global untuk menyimpan nilai delay dari potensiometer
    volatile int delaySpeed = 200;

    void TaskReadPot(void *pvParameters);
    void TaskBlink1(void *pvParameters);
    void TaskBlink2(void *pvParameters);

    void setup() {
        Serial.begin(9600);

    xTaskCreate(TaskReadPot, "ReadPot", 128, NULL, 2, NULL); // prioritas lebih tinggi
    xTaskCreate(TaskBlink1,  "Blink1",  128, NULL, 1, NULL);
    xTaskCreate(TaskBlink2,  "Blink2",  128, NULL, 1, NULL);

     vTaskStartScheduler();
    }

    void loop() {}

    // Task membaca nilai potensiometer dari pin A0
    void TaskReadPot(void *pvParameters) {
    while(1) {
        int potVal = analogRead(A0);          // nilai 0–1023
        delaySpeed = map(potVal, 0, 1023, 50, 1000); // konversi ke 50–1000 ms
        Serial.print("Delay Speed: ");
        Serial.println(delaySpeed);
        vTaskDelay(100 / portTICK_PERIOD_MS);
        }
    }

    // Task LED merah menggunakan delaySpeed dari potensiometer
    void TaskBlink1(void *pvParameters) {
    pinMode(10, OUTPUT);
    while(1) {
        digitalWrite(10, HIGH);
        vTaskDelay(delaySpeed / portTICK_PERIOD_MS);
        digitalWrite(10, LOW);
        vTaskDelay(delaySpeed / portTICK_PERIOD_MS);
        }
    }

    // Task LED kuning berkedip 2x lebih lambat dari LED merah
    void TaskBlink2(void *pvParameters) {
    pinMode(8, OUTPUT);
    while(1) {
        digitalWrite(8, HIGH);
        vTaskDelay((delaySpeed * 2) / portTICK_PERIOD_MS);
        digitalWrite(8, LOW);
        vTaskDelay((delaySpeed * 2) / portTICK_PERIOD_MS);
        }
    }
    ```
![Demo soal 5A](soal3_percobaan5a.gif)

## 5.6.4 Pertanyaan Praktikum

1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya

    Jawab   :
    Kedua task berjalan secara bergantian,tetapi pergantiannya sangat cepat yang membuat 2 task tersebut terasa berjalan secara bersamaan.Kedua task berjalan secara bergantian dengan mekanisme sinkronisasi melalui Queue.

    Mekanismenya    :
    read_data bertugas menginisialisasi nilai statis suhu dan kelembapan, lalu memasukannya ke dalam antrean/Queue menggunakan instruksi xQueueSend(my_queue, &x, portMAX_DELAY).display membaca data dari queue menggunakan xQueueReceive() dengan portMAX_DELAY → jika queue kosong, task ini akan Blocked menunggu data baru.Queue berukuran 1 item, sehingga alurnya menjadi: read_data kirim data → display terima dan tampilkan → read_data kirim lagi → dan seterusnya.

2. Apakah program ini berpotensi mengalami race condition? Jelaskan!

    Jawab   :
    Tidak,karena xQueueSend dan xQueueReceive bersifat thread safe.Dimana hanya 1 task saja yang bisa mengakses data pada satu watu. read_data hanya bisa menulis data dan display hanya bisa membaca data.Seluruh komunikasi dilakukan melalui Queue

3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis.Bagaimana hasilnya? Jelaskan program pada file README.md

Jawab   :
    
```
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <DHT.h>

#define DHTPIN 2        // Pin data DHT
#define DHTTYPE DHT11   // Tipe sensor: DHT22

DHT dht(DHTPIN, DHTTYPE);

struct readings {
  float temp;
  float h;
};

QueueHandle_t my_queue;

void read_data(void *pvParameters);
void display(void *pvParameters);

void setup() {
  Serial.begin(9600);
  dht.begin();

  // Buat queue dengan kapasitas 1 item bertipe struct readings
  my_queue = xQueueCreate(1, sizeof(struct readings));

  xTaskCreate(read_data, "read sensors", 256, NULL, 1, NULL);
  xTaskCreate(display,   "display",      256, NULL, 1, NULL);
}

void loop() {}

// Task membaca suhu dan kelembaban dari sensor DHT secara nyata
void read_data(void *pvParameters) {
  struct readings x;
  for(;;) {
    x.temp = dht.readTemperature(); // baca suhu dalam Celsius
    x.h    = dht.readHumidity();    // baca kelembaban dalam %

    // Validasi: kirim hanya jika pembacaan berhasil (tidak NaN)
    if (!isnan(x.temp) && !isnan(x.h)) {
      xQueueSend(my_queue, &x, portMAX_DELAY);
    } else {
      Serial.println("Gagal membaca sensor DHT!");
    }

    vTaskDelay(2000 / portTICK_PERIOD_MS); // DHT butuh min. 2 detik antar pembacaan
  }
}

// Task menampilkan data yang diterima dari queue ke Serial Monitor
void display(void *pvParameters) {
  struct readings x;
  for(;;) {
    if (xQueueReceive(my_queue, &x, portMAX_DELAY) == pdPASS) {
      Serial.print("Suhu    : ");
      Serial.print(x.temp);
      Serial.println(" °C");
      Serial.print("Kelembaban: ");
      Serial.print(x.h);
      Serial.println(" %");
      Serial.println("-------------------");
    }
  }
}
```

