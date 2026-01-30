# Microphone-to-FPGA Interface Circuit
## Project Overview  
This project provides a complete hardware and firmware design for a **three-channel microphone interface circuit** that conditions, amplifies, and digitizes analog audio signals from dynamic lapel microphones. The digitized data is transmitted via SPI to an FPGA or microcontroller for real-time audio processing.

**Key Applications:**  
- Acoustic sensing & event detection  
- Speech recognition  
- Environmental monitoring  
- Multi-microphone array systems  
- Defense & surveillance systems  

---

## Hardware Overview  

### Circuit Blocks  
1. **Microphone Bias Network** – Provides DC bias for AC-coupled microphone signals.  
2. **Preamp Stage 1** – ~15× voltage gain (≈23.5 dB).  
3. **Preamp Stage 2** – ~10× voltage gain + anti-aliasing filter (≈20 dB).  
4. **ADC Stage (MAX11060GUU)** – 8-bit, 3-channel ADC with SPI interface.  

### Total Gain  
- **Overall Voltage Gain:** 150× (≈43.5 dB)  
- **Input Range:** 5–50 mV (microphone) → 0.75–7.5 V (ADC input)  

### ADC Specifications  
- **Resolution:** 8-bit  
- **Channels:** 3 (multiplexed)  
- **Interface:** SPI (SCLK, MOSI, MISO, CS)  
- **Reference Voltage:** 5 V (LSB ≈ 19.5 mV)  
- **Sampling Rate:** Programmable (typical 8 kHz)  

---

## Connector Pinout (J4 – 6-pin JST)  

| Pin | Signal | Direction | Description |  
|-----|--------|-----------|-------------|  
| 1   | SCLK   | FPGA → ADC | SPI clock |  
| 2   | MOSI   | FPGA → ADC | SPI data to ADC |  
| 3   | MISO   | ADC → FPGA | SPI data from ADC |  
| 4   | CS     | FPGA → ADC | Chip select (active low) |  
| 5   | GND    | –         | Ground |  
| 6   | 3.3V   | –         | 3.3 V power supply |  

---

## Power Requirements  
- **9 V DC** – Powers op-amp stages (via J2)  
- **3.3 V DC** – Powers MAX11060 ADC and SPI logic (via J3)  

---

## Firmware Example (Pseudocode)  

```c
// SPI initialization
SPI_Init(clock_freq = 1MHz, CPOL=0, CPHA=0);

while (true) {
    for (channel = 1 to 3) {
        CS = LOW;

        // Send config: channel, gain=4x, single conversion
        cmd_byte = (channel << 4) | (gain << 2) | 0x01;
        SPI_Write(cmd_byte);

        // Read 8-bit result
        digital_value = SPI_Read();

        CS = HIGH;

        // Process sample
        Process_Audio_Sample(channel, digital_value);
    }
    delay(125 µs); // 8 kHz sampling
}
```

---

## ✅ Can This PCB Be Used with Microcontrollers?  

**Yes.** The board is **not limited to FPGAs** and can be used with many microcontroller boards, provided the following conditions are met:

### Electrical & Interface Requirements  
- The ADC (**MAX11060**) uses a **3.3 V SPI interface** and requires:  
  - **3.0–3.6 V analog supply**  
  - **2.7–3.6 V digital supply**  
- The microcontroller must:  
  - Provide **3.3 V-level SPI signals** (or use level shifters)  
  - Supply **SPI lines**: SCLK, MOSI, MISO, CS  
  - Share a **common ground** with the PCB  
  - Provide **both 3.3 V and 9 V rails** (or external supplies)  

### Compatible Boards  
- STM32 Nucleo (3.3 V logic)  
- Arduino variants with 3.3 V SPI (e.g., Due, Zero)  
- ESP32  
- Raspberry Pi Pico (with level shifting if needed)  
- Any MCU with 3.3 V SPI and sufficient power outputs  

### Firmware Notes  
- The **SPI protocol is identical** whether used with an FPGA or MCU.  
- You must implement the MAX11060 configuration and read sequence as described in the datasheet.  
- No FPGA-specific logic is required—the board is **FPGA-agnostic**.  

---

## Advantages  
- ✅ Universal SPI compatibility  
- ✅ Scalable to multiple channels via daisy-chaining  
- ✅ Low-noise, two-stage amplification  
- ✅ Programmable ADC gain  
- ✅ Real-time processing capable  
- ✅ Low-cost, COTS components  

---

## Limitations & Future Improvements  

### Current Limitations  
- **8-bit resolution** (~48 dB SNR) – suitable for speech, not hi-fi audio  
- **Fixed anti-aliasing filter** at ~650 Hz  
- **Single ADC** with 8 kHz max sampling rate  

### Suggested Upgrades  
1. **Higher-resolution ADC** (12-bit or 16-bit)  
2. **Programmable filter** for adjustable cutoff  
3. **Multiple ADCs** for parallel sampling  
4. **Integrated power management**  

---

## References  
See the main document for full references to datasheets, textbooks, and application notes.

---

## License & Attribution  
This project is licensed under the CERN Open Hardware Licence Version 2-S License - see the [LICENSE](LICENSE) file for details.

---

## Support & Contributions  
For questions, suggestions, or contributions, please refer to the project repository or contact the maintainers.

---
*Document Version: 1.0 | Date: December 2025*
