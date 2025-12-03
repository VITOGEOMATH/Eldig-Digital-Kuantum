# Eldig-Digital-Kuantum

# Arsitektur Digital Quantum Mapping: Smart Greenhouse Automation 🌱🤖

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-ESP32%20%7C%20FPGA-blue)](https://www.espressif.com/)
[![Language](https://img.shields.io/badge/Language-Verilog%20%7C%20C%23-green)](https://dotnet.microsoft.com/)
[![Status](https://img.shields.io/badge/Status-Research%20Prototype-orange)]()

> **Integrasi Sensor Lingkungan Presisi dan Logika Kendali Diskrit pada Sistem Ventilasi Otonom Greenhouse Cerdas.**

Repositori ini berisi implementasi kode, simulasi, dan dokumentasi untuk tugas mata kuliah **Penerapan Digital Quantum**. [cite_start]Proyek ini mengusulkan arsitektur sistem tertanam yang memetakan fenomena fisik terkuantisasi (foton, getaran) langsung ke dalam logika kendali gerak diskrit menggunakan notasi Bra-Ket dan matriks[cite: 1, 5].

---

## 📑 Daftar Isi
- [Latar Belakang](#-latar-belakang)
- [Konsep Digital Quantum Mapping](#-konsep-digital-quantum-mapping)
- [Arsitektur Perangkat Keras](#-arsitektur-perangkat-keras)
- [Metodologi Logika](#-metodologi-logika)
- [Implementasi Kode](#-implementasi-kode)
- [Instalasi dan Penggunaan](#-instalasi-dan-penggunaan)
- [Tim Penyusun](#-tim-penyusun)

---

## 📖 Latar Belakang

Stabilitas mikroklimat pada *greenhouse* cerdas menuntut pengendalian yang responsif. Sistem konvensional seringkali kurang fleksibel dalam merespons fluktuasi parameter kritis secara *real-time*. [cite_start]Proyek ini menggunakan pendekatan **"Digital Quantum"**, di mana fenomena fisik dideteksi oleh sensor berbasis efek kuantum (seperti efek fotolistrik pada sensor UV) dan dikonversi menjadi data diskrit untuk menggerakkan aktuator presisi (Stepper Motor) [cite: 12-15].

---

## ⚛️ Konsep Digital Quantum Mapping

Sistem ini tidak menggunakan komputer kuantum, melainkan menerapkan prinsip kuantisasi sinyal dan representasi status menggunakan notasi vektor keadaan (*State Vector*).

1.  [cite_start]**Kuantisasi Input:** Sensor mengubah besaran analog menjadi paket data diskrit (misal: Kepadatan Pulsa pada UVTRON)[cite: 93].
2.  [cite_start]**Logic Solve:** Pemetaan keputusan menggunakan gerbang logika (NOT, AND, OR, NAND)[cite: 292].
3.  [cite_start]**Notasi Bra-Ket:** Representasi status sistem saat ini (*Current State*) sebagai Ket $|\psi\rangle$ dan logika transisi sebagai Operator $\hat{U}$[cite: 284].

Contoh Persamaan Evolusi Status untuk Ventilasi:
$$|\Psi_{next}\rangle = \hat{U}_{logic} (|B\rangle \otimes |A\rangle)$$
*Dimana $|B\rangle$ adalah vektor getaran (badai) dan $|A\rangle$ adalah vektor suhu.*

---

## 🛠 Arsitektur Perangkat Keras

[cite_start]Sistem ini terdiri dari dua lapisan utama: **Input Layer** (Sensor) dan **Output Layer** (Aktuator) yang dijembatani oleh *Embedded System* [cite: 25-26].

### Sensor (Input Layer)
| Sensor | Fungsi | Kondisi Digital |
| :--- | :--- | :--- |
| **UVTRON** | Monitoring Radiasi UV | [cite_start]Kepadatan Pulsa ($N_{pulses}/sec$) [cite: 28] |
| **BME280** | Mikroklimat (Suhu/Hum) | [cite_start]Register Data 20-bit [cite: 35] |
| **ADXL345** | Deteksi Badai/Getaran | [cite_start]Vektor Digital 3-Sumbu [cite: 40] |
| **Kapasitif Array** | Kelembaban Tanah | [cite_start]Frekuensi Gelombang Kotak [cite: 45] |
| **MAX30100** | Keamanan Pekerja (K3) | [cite_start]Data FIFO (Absorpsi Foton) [cite: 55] |

### Aktuator (Output Layer)
| Aktuator | Fungsi | Metode Kendali |
| :--- | :--- | :--- |
| **Stepper Motor** | Ventilasi Atap | [cite_start]*Pulse Sequence* (Langkah Diskrit) [cite: 61] |
| **Servo Motor** | Arah Sirkulasi/Kamera | [cite_start]PWM (*Duty Cycle*) [cite: 66] |
| **Relay** | Pompa & Kipas | [cite_start]Logika Biner (0/1) [cite: 71] |
| **LED Stick** | *Grow Light* | [cite_start]Paket Data Serial RGB [cite: 76] |

---

## 🧠 Metodologi Logika

[cite_start]Sistem menggunakan **Concurrent Finite State Machine (FSM)** yang berjalan secara multitasking untuk menangani tiga subsistem utama[cite: 149]:

1.  **Subsistem Ventilasi (Prioritas Tinggi):** * Mengatur buka-tutup atap.
    * [cite_start]Memiliki fitur *Safety Override* (Lockdown) saat badai terdeteksi ($|B\rangle = |1\rangle$)[cite: 151].
2.  **Subsistem Pertumbuhan:**
    * [cite_start]Mengatur irigasi dan pencahayaan suplemen secara paralel[cite: 165].
3.  **Subsistem Keamanan (K3):**
    * [cite_start]Memantau detak jantung pekerja dan menghentikan mesin nutrisi jika ada bahaya[cite: 180].

[cite_start]Logika ini direpresentasikan dalam bentuk **Matriks Transisi** untuk efisiensi komputasi pada mikrokontroler[cite: 240].

---

## 💻 Implementasi Kode

[cite_start]Repositori ini menyediakan dua jenis simulasi kode[cite: 300]:

### 1. Simulasi FPGA (Verilog HDL)
Implementasi logika gerbang digital fisik dan FSM menggunakan *Hardware Description Language*.
* **Lokasi:** `/src/verilog/DigitalQuantumMapping.v`
* [cite_start]**Fitur:** Pemrosesan paralel dengan latensi <20ms[cite: 445].

```verilog
// Contoh Snippet Verilog untuk Logika Ventilasi
always @(posedge clk or posedge rst) begin
    if (rst) Step_Mode <= 2'b00;
    else begin
        if (B_Vib == 1'b1)      Step_Mode <= 2'b10; // LOCKDOWN
        else if (A_Temp == 1'b1) Step_Mode <= 2'b01; // VENTING
        else                    Step_Mode <= 2'b00; // HOLD
    end
end
