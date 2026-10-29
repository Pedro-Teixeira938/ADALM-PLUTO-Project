#Bandwidth:
The protocoll operates at a 20Mhz frequency bandwidth, with it's operating frequency being centered at 5GHz. The protocoll features 64 subcarriers, with 52 being for data (4 of which are pilot subcarriers) and the remaining 12 being unused.

#Why are there unused subcarriers?

  In the 802.11a standard, we have **12 unused subcarriers** to act as overhead protection for hardware constraints:

*   **Guard Subcarriers (6 Lower, 5 Upper):** Real-world analog filters cannot cut off a signal perfectly at the channel edge; they require a gradual roll-off zone known as a **filter transition band**. These 11 empty subcarriers provide a safety buffer. They prevent the outermost active subcarriers from being distorted by the transmitter's own filters and stop signal energy from bleeding into neighboring channels, thereby eliminating **Adjacent Channel Interference (ACI)**.
*   **DC Subcarrier (Index Zero):** Positioned exactly in the center of the channel, this subcarrier is set to a "NULL" value to mitigate **DC Offset leakage**. Radio frequency architecture creates a spike of direct current (DC) energy at the center frequency due to local oscillator leakage. Leaving index zero empty ensures this hardware-induced noise does not corrupt actual data.


  Regarding section 2.1:
  Stream tags are packets of metadata (composed of a key, a value and a sample offsett) that are attached to specific samples in GRC. The only tag present in Pluto_FD is the packet_len tags from plutoSDR source.

  Regarding Pluto_FD

  Pluto_FD is mostly identical to the Frame detection code provided, however instead of using a simulated source we use ADALM-Pluto. 
  The blocks that calculate the signal's power are the ones in the lower ramification (Complex to Mag^2 and Decimating FIR Filter). Mag^2 gives us the instantaneous power of the recived signal (\(\vert{}r(t)\vert{}^2\)) and the Decimating filter performs the cumulative sum of said instantaneous powers in acordance to a sliding window defined in the variable block "window_size".
  In comunication sistems, autocorrelation is needed to identify periodicities, especialy when trying to perform frame detection withought knowing the exact channel. The mathematical formula for autocorrelation is,$M(d) = \frac{|P(d)|^2}{(R(d))^2}$
 , and the Delay, Complex Conjugate, Multiply, Decimating FIR and Complex to mag blocks all perform a part of this equation.
 \(r(d+m)\) is our live signal, it is then delayed, which very direcly gives us \(r(d+m-D)\). The folowing step is to perform complex conjugation \(r^*(\cdot)\) and then multiply the live signal by this conjugated block. Summation then follows, which involves and addition of the multiplies signals from m=0 to L-1, where L is our window size, this is handled by the Decimating FIR. Finally we must perform the absolute of this operation, which is done by the complex to mag block.
 

  
