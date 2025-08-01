



# 🛰️ GNU Radio QPSK/PSK Modulation Flowgraph

This project implements a **QPSK modulation and demodulation** system using GNU Radio Companion (GRC). The system supports flexible phase shift keying by changing a single variable (`N`) — enabling **QPSK, 8-PSK, 16-PSK**, and beyond.



## 📡 Overview

- **Modulation Type**: -ary Phase Shift Keying (PSK)

- **Configurable Symbol Levels**: Change `N` to switch between QPSK (N=4), 8-PSK (N=8), etc.

- **Carrier Frequency**: 8 kHz

- **Sample Rate**: 64 kHz

- **Interpolation Factor**: 64

- **Modulation Bandwidth**: Determined by low-pass filters

## 🧠 How It Works

### 🔷 Transmitter Chain:

1. **Random Source**: Generates random symbols between 0 and .

2. **Chunks to Symbols**: Maps integers to constellation points using a user-defined symbol table.

3. **Interpolator (FIR Filter)**: Upsamples and smoothens the symbol stream.

4. **Carrier Mixing**: Multiplies modulated baseband with carrier (cosine wave) for transmission.

### 🔶 Receiver Chain:

1. **Mixer and Demodulation**:
   
   - Multiplies incoming signal with cosine and sine carriers.
   
   - Extracts in-phase (I) and quadrature (Q) components.

2. **Low Pass Filters**: Remove high-frequency components post-mixing.

3. **Reconstruction**:
   
   - I/Q signals recombined into complex stream.
   
   - Output visualized using QT GUI sinks.

## 📈 QT GUI Sinks

- **Constellation Sink**: Displays received symbols in I-Q plane.

- **Time Sink**: Shows waveform over time.

- **Delay Slider**: GUI control to introduce delay in received signal path for visual tuning.

## 🛠️ Customization

You can switch the modulation scheme by changing:

```python
Variable: N = 4  # Change to 8, 16, etc. for 8-PSK, 16-PSK...
```

And update the symbol table accordingly in `Chunks to Symbols`, for example:

```python
[1, exp(j*2π/8), exp(j*4π/8), ..., exp(j*14π/8)]  # for 8-PSK
```

## 🔧 Requirements

- GNU Radio (3.8 or later recommended)

- NumPy for custom tap definitions

- QT GUI (for visual sinks)

Absolutely — let's walk through **each block** in your GNU Radio flowgraph and **explain its purpose** and why it's needed in a typical **-PSK (e.g. QPSK/8-PSK/16-PSK)** communication system.

---

## 🔁 **Transmitter Path:**

### 🔹 1. **Random Source**

- **Purpose**: Generates a stream of **random integers** between 0 and .

- **Why it's needed**: Simulates the **digital data** (e.g., from a sensor or file) to be transmitted.

- **Configurable**: The range depends on — the modulation order.

---

### 🔹 2. **Chunks to Symbols**

- **Purpose**: Maps each integer (symbol) to a corresponding **complex value** in the PSK constellation.

- **Why it's needed**: Converts raw data bits into **PSK constellation points**, e.g. for QPSK:
  
  ```python
  [1, j, -1, -j]
  ```

- **Customizable**: The **symbol table** can be updated for 8-PSK, 16-PSK, etc.

---

### 🔹 3. **Throttle**

- **Purpose**: Limits the sample rate during simulation.

- **Why it's needed**: Prevents the flowgraph from consuming 100% CPU in real-time (especially when no hardware is involved).

- **Note**: Not needed when using SDR hardware.

---

### 🔹 4. **Interpolating FIR Filter**

- **Purpose**: **Upsamples** the signal to match the system's sample rate (e.g., from symbol rate to 64 kHz).

- **Why it's needed**: Required for digital-to-analog conversion and smooth carrier modulation.

- **Interpolation factor**: Matches `interpolating_factor` variable (e.g., 64).

---

### 🔹 5. **Virtual Sink / Source**

- **Purpose**: Acts as a **bridge** for sharing data between transmitter and receiver paths.

- **Why it's needed**: Simulates transmission over a channel, without actual RF hardware.

---

## 🧭 **Carrier Modulation Section:**

### 🔹 6. **Signal Source (Cosine Carrier)**

- **Purpose**: Generates the **carrier wave** at 8 kHz.

- **Why it's needed**: For modulating the baseband signal onto a higher-frequency carrier (AM-style).

---

### 🔹 7. **Multiply (Baseband × Carrier)**

- **Purpose**: Mixes the baseband signal with the carrier.

- **Why it's needed**: Shifts the signal from **baseband to passband**, suitable for transmission.

---

### 🔹 8. **Complex to Real**

- **Purpose**: Extracts the real part of the complex signal.

- **Why it's needed**: Simulates real-valued transmission (e.g., via analog RF hardware or DAC).

---

## 🔄 **Receiver Path:**

### 🔹 9. **Signal Sources (Cosine and Sine)**

- **Purpose**: Generate **local oscillator** signals for downconversion.

- **Why it's needed**: Mixes the incoming signal with **cosine** (for I) and **sine** (for Q).

---

### 🔹 10. **Multiply (Mixers)**

- **Purpose**: Multiply received signal with oscillator signals to extract I and Q components.

- **Why it's needed**: This is part of a **quadrature demodulator**.

---

### 🔹 11. **Low Pass Filters**

- **Purpose**: Remove high-frequency components after mixing.

- **Why it's needed**: Extracts only the **baseband I and Q signals**, eliminating double-frequency terms from mixing.

---

### 🔹 12. **Float to Complex**

- **Purpose**: Combines I and Q into a **complex stream**.

- **Why it's needed**: Reconstructs the complex baseband signal for constellation plotting or further demodulation.

---

## 📊 **GUI Visualization:**

### 🔹 13. **QT GUI Time Sink**

- **Purpose**: Shows waveform in time domain.

- **Why it's needed**: Helps visualize signal timing, transitions, or errors.

---

### 🔹 14. **QT GUI Constellation Sink**

- **Purpose**: Displays received symbols in the I-Q plane (constellation diagram).

- **Why it's needed**: Essential for debugging **modulation accuracy**, phase noise, and symbol errors.

---

### 🔹 15. **QT GUI Range (Delay Slider)**

- **Purpose**: Allows interactive delay adjustment.

- **Why it's needed**: Useful for fine-tuning symbol alignment and visual debugging.

---

## 🛠️ Summary Table:

| Block                              | Purpose                       | Why Needed                         |
| ---------------------------------- | ----------------------------- | ---------------------------------- |
| Random Source                      | Generate symbol stream        | Simulates user data                |
| Chunks to Symbols                  | Map integers to constellation | Forms PSK symbols                  |
| Throttle                           | Limit flow rate               | Prevent CPU overuse                |
| Interpolating FIR                  | Upsample signal               | Match sample rate                  |
| Virtual Source/Sink                | Simulated transmission path   | Links TX to RX                     |
| Signal Sources                     | Generate carriers             | Needed for modulation/demodulation |
| Multiply                           | Mixer for up/down conversion  | Shift frequencies                  |
| Low Pass Filter                    | Remove mixing artifacts       | Extract clean baseband             |
| Complex to Real / Float to Complex | Data type conversion          | For processing stages              |
| GUI Sinks                          | Visualization                 | Debug and analysis                 |




