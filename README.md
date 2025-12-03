# Arsitektur Digital Quantum Mapping: Smart Greenhouse Automation 🌱🤖

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-ESP32%20%7C%20FPGA-blue)](https://www.espressif.com/)
[![Language](https://img.shields.io/badge/Language-Verilog%20%7C%20C%23-green)](https://dotnet.microsoft.com/)
[![Status](https://img.shields.io/badge/Status-Research%20Prototype-orange)]()

## 👥 Tim Penyusun
- Disusun Oleh:
[Muhammad Rif’al Faiz Arivito (2042231067)]
- Program Studi:
[D4 Teknologi Rekayasa Instrumentasi]
[Departemen Teknik Instrumentasi]
[Fakultas Vokasi]
[Institut Teknologi Sepuluh Nopember (ITS)]
- Dosen Pengampu:
[Dwi Oktavianto Wahyu Nugroho, S.T., M.T.]

> **Integrasi Sensor Lingkungan Presisi dan Logika Kendali Diskrit pada Sistem Ventilasi Otonom Greenhouse Cerdas.**

Repositori ini berisi implementasi kode, simulasi, dan dokumentasi teknis untuk tugas mata kuliah **Penerapan Digital Quantum**. Proyek ini mengusulkan arsitektur sistem tertanam yang memetakan fenomena fisik terkuantisasi (foton, getaran, pulsa) langsung ke dalam logika kendali gerak diskrit menggunakan pendekatan **Logic Solve** dan notasi **Bra-Ket**.

---

## 📑 Daftar Isi
- [Latar Belakang](#-latar-belakang)
- [Konsep Digital Quantum Mapping](#-konsep-digital-quantum-mapping)
- [Arsitektur Perangkat Keras](#-arsitektur-perangkat-keras)
- [Metodologi Logika (FSM & Matriks)](#-metodologi-logika-fsm--matriks)
- [Simulasi & Implementasi Kode](#-simulasi--implementasi-kode)
- [Instalasi dan Penggunaan](#-instalasi-dan-penggunaan)
- [Tim Penyusun](#-tim-penyusun)

---

## 📖 Latar Belakang

Stabilitas mikroklimat pada *greenhouse* cerdas menuntut pengendalian yang responsif terhadap fluktuasi lingkungan yang dinamis. Sistem konvensional seringkali kurang fleksibel dalam merespons parameter kritis secara *real-time*.

Proyek ini menggunakan pendekatan **"Digital Quantum"**, di mana:
1.  Fenomena fisik dideteksi oleh sensor berbasis efek kuantum (seperti efek fotolistrik pada sensor UV).
2.  Data dikonversi menjadi sinyal diskrit (pulsa/frekuensi).
3.  Sinyal dipetakan untuk menggerakkan aktuator presisi (Stepper Motor) dengan langkah sudut yang pasti.

---

## ⚛️ Konsep Digital Quantum Mapping

Sistem ini tidak menggunakan komputer kuantum (qubit), melainkan menerapkan prinsip **kuantisasi sinyal** dan **representasi status vektor**.

### Alur Pemetaan:
1.  **Kuantisasi Input:** Sensor mengubah besaran analog menjadi paket data diskrit.
    * *Contoh:* Sensor UVTRON menghitung densitas pulsa foton per detik.
2.  **Logic Solve:** Pemetaan keputusan menggunakan gerbang logika (NOT, AND, OR, NAND).
3.  **Notasi Bra-Ket:** Representasi status sistem saat ini (*Current State*) sebagai Ket $|\psi\rangle$ dan logika transisi sebagai Operator $\hat{U}$.

**Contoh Persamaan Evolusi Status untuk Ventilasi:**
$$|\Psi_{next}\rangle = \hat{U}_{logic} (|B\rangle \otimes |A\rangle)$$

*Dimana:*
* $|B\rangle$: Vektor getaran/badai (1 = Badai, 0 = Aman).
* $|A\rangle$: Vektor suhu (1 = Panas, 0 = Normal).
* Jika $|B\rangle = |1\rangle$, sistem "runtuh" ke state $|10\rangle$ (Lockdown).

---

## 🛠 Arsitektur Perangkat Keras

Sistem terdiri dari **Input Layer** (Sensor) dan **Output Layer** (Aktuator) yang dijembatani oleh *Embedded System*.

### Sensor (Input Layer)
| Sensor | Fungsi | Kondisi Digital | Antarmuka |
| :--- | :--- | :--- | :--- |
| **UVTRON** | Monitoring Radiasi UV | Kepadatan Pulsa ($N_{pulses}/sec$) | GPIO Interrupt |
| **BME280** | Mikroklimat (Suhu/Hum) | Register Data 20-bit | I2C |
| **ADXL345** | Deteksi Badai/Getaran | Vektor Digital 3-Sumbu | SPI/I2C |
| **Kapasitif Array** | Kelembaban Tanah | Frekuensi Gelombang Kotak | Timer/Counter |
| **MAX30100** | Keamanan Pekerja (K3) | Data FIFO (Absorpsi Foton) | I2C |
| **CMOS Image** | Visual Tanaman | Matriks Piksel Digital | Parallel/CSI |

### Aktuator (Output Layer)
| Aktuator | Fungsi | Metode Kendali | Karakteristik Diskrit |
| :--- | :--- | :--- | :--- |
| **Stepper Motor** | Ventilasi Atap | Pulse Sequence | Langkah Diskrit (1.8°/step) |
| **Servo Motor** | Arah Sirkulasi/Kamera | PWM | Duty Cycle (Posisi Absolut) |
| **Relay** | Pompa & Kipas | Logika Biner | ON/OFF (Histeresis) |
| **LED Stick** | Grow Light | Serial Data | Paket RGB 24-bit |
| **DC Motor** | Dosing Nutrisi | H-Bridge Logic | Durasi Waktu (Time-based) |
| **Buzzer** | Alarm Peringatan | GPIO Toggle | Gelombang Kotak Audio |

---

## 🧠 Metodologi Logika (FSM & Matriks)

Sistem menggunakan **Concurrent Finite State Machine (FSM)** yang berjalan secara multitasking untuk menangani tiga subsistem utama:

1.  **Subsistem Ventilasi (Prioritas Tinggi):** * Mengatur buka-tutup atap.
    * Memiliki fitur *Safety Override* (Lockdown) saat badai terdeteksi ($|B\rangle = |1\rangle$).
2.  **Subsistem Pertumbuhan:**
    * Mengatur irigasi dan pencahayaan suplemen secara paralel berdasarkan input tanah dan cahaya.
3.  **Subsistem Keamanan (K3):**
    * Memantau detak jantung pekerja dan menghentikan mesin nutrisi jika ada bahaya.

### Matriks Transisi (Contoh Ventilasi)
Logika direpresentasikan dalam matriks untuk efisiensi komputasi:

| Current State | Input 00 (Aman, Dingin) | Input 01 (Aman, Panas) | Input 10 (Badai) | Input 11 (Badai) |
| :--- | :--- | :--- | :--- | :--- |
| **S0 (Idle)** | 00 (Hold) | 01 (Open) | 10 (Lock) | 10 (Lock) |
| **S1 (Venting)** | 00 (Hold) | 01 (Open) | 10 (Lock) | 10 (Lock) |
| **S2 (Lockdown)**| 00 (Reset)| 00 (Reset)| 10 (Lock) | 10 (Lock) |

---

## 💻 Simulasi & Implementasi Kode

Repositori ini menyediakan dua jenis simulasi kode sesuai dengan jurnal referensi.

### 1. Simulasi FPGA (Verilog HDL)
Implementasi logika gerbang digital fisik dan FSM untuk kecepatan tinggi (<20ms).

```verilog
// Lokasi: /src/verilog/DigitalQuantumMapping.v
module DigitalQuantumMapping (
    input wire clk, rst,
    input wire A_Temp, B_Vib, 
    output reg [1:0] Step_Mode
);
    // SUBSISTEM 1: VENTILASI (Priority Logic)
    always @(posedge clk or posedge rst) begin
        if (rst) begin
            Step_Mode <= 2'b00;
        end else begin
            // Jika Badai (B=1), Paksa Lockdown (10)
            if (B_Vib == 1'b1) begin
                Step_Mode <= 2'b10; 
            end else if (A_Temp == 1'b1) begin
                Step_Mode <= 2'b01; // Venting
            end else begin
                Step_Mode <= 2'b00; // Hold
            end
        end
    end
endmodule

### 2. Simulasi Mikrokontroler (C# / .NET IoT)
Implementasi *Application Controller* untuk MCU modern (seperti ESP32 dengan nanoFramework).

```csharp
// Lokasi: /src/dotnet/GreenhouseController.cs
public static void Main() {
    GpioController gpio = new GpioController();
    // Setup Pin...
    
    while (true) {
        bool isHot = gpio.Read(PIN_TEMP_FLAG);
        bool isStorm = gpio.Read(PIN_VIB_FLAG);

        // LOGIC SOLVE
        if (isStorm) {
            // State: LOCKDOWN -> Gerak CCW
            MoveStepper(gpio, PinValue.Low, 100); 
            Console.WriteLine("EMERGENCY LOCKDOWN");
        } else if (isHot) {
            // State: VENTING -> Gerak CW
            MoveStepper(gpio, PinValue.High, 10); 
        }
        Thread.Sleep(100);
    }
}


