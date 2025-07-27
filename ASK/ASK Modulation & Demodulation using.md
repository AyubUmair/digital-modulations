# **ASK Modulation \& Demodulation using GNU Radio📡**





**This GNU Radio project demonstrates the Amplitude Shift Keying (ASK) modulation scheme. It takes a random digital signal, modulates it using ASK, and demodulates it back to recover the original information.**





#### **📋 Overview**



* **Modulation type: ASK (Amplitude Shift Keying)**



* **Sample Rate: 64k samples/sec**



* **Carrier Frequency: 8kHz**



* **Interpolation Factor: 64 (upsampling for smooth signal)**



* **Demodulation Technique: Coherent detection using cosine mixing and low-pass filtering**





####  **Block-by-Block Explanation**



**🔹 1. Random Source**



                           **Generates random integers \[0, 4)**



                            **Num Samples = 1024, repeated for continuous streaming**



**🔹 2. Chunks to Symbols**



                           **Maps the integers 0,1,2,3 to corresponding amplitudes using the symbol table: \[0, 1, 2, 3]**



                            **This forms the baseband signal before modulation**



**🔹 3. Throttle**



                            **Limits the rate to 64k samples/sec for real-time simulation**



**🔹 4. Interpolating FIR Filter**



                            **Interpolation factor = 64**



                           **Taps = numpy.full(interpolation\_factor, 1)**



                           **Purpose: Up sample the signal to match the carrier and avoid aliasing (acts like a "Repeat" block)**



**🔹 5. Virtual Sink / Source**



                           **Connects interpolated baseband signal to modulation stage via a virtual stream**



**🔹 6. Signal Source (Carrier Generator for Modulation)**



                           **Cosine wave at 8kHz, 64k sample rate**



                           **Acts as carrier signal for ASK modulation**



**🔹 7. Multiply (Modulator)**



                          **Multiplies baseband signal with carrier → this creates ASK modulated signal**



**🔹 8. QT GUI Time Sink (Modulated Signal Viewer)**



                         **Visualizes the modulated signal in time domain**





#### **🔻 Demodulation Section**







**🔸 9. Signal Source (for Demodulation)**



                         **Two carriers: cosine and sine at 8kHz, for coherent demodulation (IQ)**



                         **Multiply with received signal to extract I (cos) and Q (sin) components**



**🔸 10. Multiply Blocks**



                         **One multiplies modulated signal × cosine (I)**



                        **Another multiplies modulated signal × sine (Q)**



**🔸 11. Low Pass Filters**



                       **Filters out high-frequency components (after mixing)**



                       **Cutoff frequency = 5kHz, Transition = 1kHz**



                       **Outputs: baseband I and Q signals**



**🔸 12. QT GUI Time and Frequency Sinks**



                       **Show demodulated signals in time domain and frequency domain**



#### 

#### **🔄 Signal Flow Summary**





1. **Random bits → Symbols → Interpolated baseband**

2. **Baseband × Carrier (cosine) → ASK signal**

3. **ASK signal × Carrier (cos/sin) → Mixed down**

4. **Low Pass Filter → Extract baseband I/Q**

5. **Visualize → Time/Frequency sinks**





#### **🧪 Technical Notes**



1. **Why interpolate? To up sample the baseband for smooth modulation at higher sample rates.**

2. **Why multiply by cos/sin in demodulation? Coherent detection requires mixing with original carrier.**

3. **Why Low Pass Filter? Removes double-frequency components, leaving baseband info**





#### **🚀 How to Run**



1. **Open the .grc file in GNU Radio Companion.**

2. **Run the flowgraph.**

3. **Observe:

        Modulated ASK signal (QT Time Sink)

        Demodulated baseband signals (QT Time and Frequency Sinks)**

4. **Adjust parameters like carrier frequency or interpolation factor for experimentation.**



#### 

#### **📋Reasonings**





#####  **Multiply by √2 · cos(w₀t) and √2 · sin(w₀t) + LPF**



* **This is IQ demodulation or complex baseband conversion.**



* **What happens:**



                   **You create I (In-phase) and Q (Quadrature) components:**



                   **Multiply by √2·cos(w₀t) → I**



                   **Multiply by √2·sin(w₀t) → Q**



* **Then apply low-pass filters to both.**



* **Result: a complex baseband signal (I + jQ), which retains both amplitude and phase.**



**✅ This is the correct method for general demodulation, especially if:**



                      **our signal is not purely real-valued modulation.**



                      **We’re working with FM, PSK, QAM, SSB, etc.**







##### **🔹 Why Multiply by √2?**





**This is for power normalization:**



                      **When you multiply a cosine by another cosine, the power scales down by half.**



                       **Multiplying by √2 ensures the original signal power is preserved after demodulation.**



**But in GNU Radio, you often don’t need to multiply by √2 manually, because many blocks (like Multiply, Quadrature Demod, etc.) take care of scaling implicitly.**



**(You can see that I have multiplied the amplitude of cosine and sine signals by √2 at signal source. If you wish you can keep the amplitude as one and increase the gain to two.)**





#### **ADDITIONAL NOTE 🗒** 



#####  **Multiply by cosine only + low-pass filter**



**This is called coherent detection or real demodulation.**



**What happens:**



                      **You shift the bandpass signal down in frequency, but you only get part of the baseband signal (real part).**



                      **If your modulation is real-valued, like AM (Amplitude Modulation), this can be OK.**



                     **But if your modulation uses phase or quadrature info (e.g., QPSK, FM, SSB), this loses information.**

                        

                     **Suits only for REAL signals.**









