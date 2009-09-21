
Here we have a little box of tricks that will acquire the voltage and current waveforms from a mains power circuit and blow them into an
ADC - or the on-board ADC on a microcontroller, Arduino or similar, whatever you like. From there, the waveforms can be digitised and
communicated out to the omnipresent computing cloud, or the Internet of Things - to a PC, over USB, to Ethernet, to an XBee radio network, to
an OpenWRT device via either USB or the internal UART, to the Internet via any of the above interfaces, to your giant LED message sign, to
wherever and whatever you like, really.

But first we need a little analog electronics to grab those waveforms and manipulate them a little so that they can be aquired by a 0-5V ADC
operating from a unipolar 5 V supply rail.

First, we take a transformer with a 240 VAC primary and a 12 V secondary. In my prototype, I used the Jaycar MM-2006 transformer, with a
center-tapped 12.6 V secondary. We don't need much current out of the secondary, so a small transformer is fine. We connect the primary of the
transformer across the 240 VAC line - of course, this wiring needs to be insulated and packaged appropriately for safety. But once you've done
that, you can bring out the low-voltage wires from the secondary and play with them on a breadboard safely, if you like.

If your transformer has a centre tap, you can ignore that. We take one side of the secondary, and connect it to ground.

With minimal load on the secondary, the peak-to-peak voltage across the secondary will be approximately 2 * sqrt(2) * 12 V, where 12 V is the
specified RMS voltage from the transformer's secondary, or about 34 V.

You can measure this voltage waveform with an oscilloscope, if you like, but you shouldn't be surprised to see something that is certainly
not a beautiful mathematically pure sinusoidal waveform. Some distortion of the sinusoidal waveform, clipping or distortion of the top of the
peaks, etc, is all quite normal when looking at the voltage waveform off the domestic grid.

Anyway, if we've got a 0-5 V ADC, and a waveform with a peak-to-peak magnitude of perhaps about 34 V, clearly we need to attenuate this
waveform down a bit more - and we do this with resistors R8 and R9. This 10:1 voltage divider uses nice common resistor values, and will give
us an output voltage with a magnitude scaled down by a magnitude of 1/11 - or a bit over 3 V. This will do the job quite nicely for a 5 V
ADC, and will also allow us to see if there is any significant under-voltage or over-voltage condition of the mains supply voltage.

Now, we can take this scaled-down voltage signal, and work with it a little more. But back to that in a minute. Now that we've sampled off a
sample of the mains voltage waveform, we can take the AC output voltage from the transformer and rectify it, using a 1N4004 diode. Since we
have one side of the transformer secondary connected to ground, there's no point in full-wave rectification using a bridge rectifier.

Now, we use a fairly large electrolytic capacitor to smooth this rectified voltage. I used a 470 uF 25 V capacitor to build the prototype,
since that's what I had laying around, but I would suggest making it a little bigger. 1000 uF, 25 V, should be a good choice. As is always the
case with the selection of such capacitors, if you're not hard pressed for physical space, it never hurts to use a capacitor that's a bit
larger. Remember to wire the capacitor with respect for its polarity and do not use a capacitor with an inadequate voltage rating, or else
the capacitor will explode, and you don't want that.

Now, we have a DC power supply of approximately 12 V. We now connect this power supply to a standard 7809 voltage regulator. I've then
connected this 9 V supply to the voltage input on an Arduino Duemilanove, where the 9 V supplies the Arduino's on-board 5 V regulator, but
that was just what I implemented for prototype purposes. You might wish to implement your own independant 5 V voltage regulator in the circuit.

Now we've got a transformer which is both giving us access to the AC voltage waveform and a self-contained regulated low voltage power supply
which is derived from the mains power supply - so our microcontroller and other electronics have their own self-contained power supply
available.

The function of the 1:1 voltage divider comprised of R5 and R6 is to generate a 2.5 V reference voltage. I took the regulated 5 V off the
Arduino board to supply the regulated 5 V for this, but if you implemented your own external 5 V regulator, again, this would not be needed.

The 10 uF capacitor, C4, is essential to filter this 2.5 V reference voltage and remove 50 Hz sinusoidal noise from it, since this reference
voltage is shared with the current measurement circuit and it will wreak havok with it if not filtered.

The voltage at the junction of R8 and R9 should simply be a scaled-down version of the voltage at the transformer secondary, with an amplitude
of a bit over 3 V, approximately. approximately. But the median level of the voltage waveform is ground, so we can't acquire this waveform
with an 0-5 V ADC. To sample this waveform using such an ADC, we need to shift the waveform up by a DC offset bias of 2.5 V. This is the
function of C3 and R4 - in conjunction with the 2.5 V bias voltage mentioned above.

We then take the output waveform from the junction of C3 and R4 - which should be a sinusoidal waveform around 3 V in peak-to-peak magnitude,
well within the range of 0 to 5 V, with a median value of approximately 2.5 V - perfectly suitable to be connected to the microcontroller or
ADC.

To measure the current in the mains circuit, we use a ratiometric Hall effect sensor IC to measure the magnetic flux around a current-carrying
wire.

* more to come later, this is incomplete *
























