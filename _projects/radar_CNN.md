---  
title: "Lightweight CNNs for Drone, Car, and People Recognition from FMCW Range-Doppler Maps"  
excerpt: "Extending DopplerNet (Roldan et al., IET Radar Sonar Navig., 2020) by creating a compact CNN for embedded radar."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/DopplerNet"
classes: compact-text  
mathjax: true  
--- 
  <br>
# FMCW Radar Background  
  
FMCW stands for frequency modulated continuous wave. The name comes from the chirp transmitted by FMCW radars. A chirp is a continuous wave whose frequency is linearly modulated. In particular, a chirp is a sinusoid whose frequency sweeps linearly in time. 
<p align="center">
<img src="/assets/images/fmcw_radar_diagram.png" alt="FMCW Diagram" width="800">
</p>
We will use the diagram above to understand how FMCW radars work. First, a synth/LO generates a chirp. The chirp is transmitted by the TX antenna. A chirp is then reflected by an object and received by the RX antenna(s). The received (by the RX ant.) chirp and transmitted (by the TX ant.) chirp is mixed in the mixer, resulting in an IF signal. From there the signal passes through a low pass filter which removes high frequency tones. The signal then continues to the ADC (analog-to-digital converter) for sampling. The ADC takes the IF signal (our continuous analog signal from the real world), and samples it at specific time intervals, discretizing the signal into data we can perform calculations on. From there our discretized data goes to a DSP (digital signal processor) which can apply mathematical transformations like scaling, compression, and Fourier Transform. Fast Fourier Transform (FFT) is an optimized algorithm for computing a Fourier Transform; It is commonly used in signal processing. When we say Fourier Transform below, we mean FFT. We will now expand a bit on the finer details.

# Preliminaries: Sinusoids, Frequency, and Phase


An FMCW radar analyzes sinusoidal signals. A sinusoidal signal is characterized by three values: **amplitude** $$A$$ its peak value (a measure of how strong it is), **frequency** $$f$$ the number of cycles per second in Hz  (how fast it oscillates), and its **phase** $$\phi$$ where in the cycle the signal is at at time $$t = 0$$. Imagine a point moving counterclockwise at a constant speed around a circle (on some plane centered at $$(0,0)$$ for simplicity) of radius $$A$$. The point starts at an angle $$\phi$$ measured from the positive horizontal axis, and it completes $$f$$ laps per second. As the point moves, its vertical distance above the horizontal axis traces out the sine wave $$A\sin(2\pi~f~t + \phi)$$. At $$t=0$$, the height is $$A\sin(\phi)$$. Changing the starting angle $$\phi$$ slides the wave sideways without changing its shape. A larger $$\phi$$ is a shift to the left and smaller is a shift to the right.

<p align="center">
<img src="/assets/images/phase_shift.png" alt="phase_shift" width="820">
</p>

A TX chirp is a sinusoid whose frequency increases linearly with time, and the RX chirp is a time-delayed copy. When the two are mixed, they are multipied. The product contains a component whose phase is the difference of the two phases, and one component whose phase is their sum. The sum component is removed by the low-pass filter. What remains is a sinusoid whose instantaneous frequency is the difference of the two chirps' instantaneous frequencies. We will see later on that because the the RX chirp is just a time-delayed TX chirp, the difference is constant. Next we introduce some fundamamentals on the chirp. 
# Chirp Bandwidth, Chirp Time, Chirp Slope
An FMCW radar transmits a **chirp**: a signal whose frequency increases linearly with time (a continuous wave whose frequency is linearly modulated hence where FMCW comes from). A chirp repeats many times, and the radar processes the reflections of each. Three numbers describe a chirp. 
*  **Chirp time** $$t_c$$ (or chirp duration) is how long a chirp lasts.
*  **Bandwidth** $$B$$ is the range of frequencies the chirp sweeps across.
*  **Chirp slope** $$S$$ is how fast the frequency changes, in Hz. 

Plotting frequency over time, the bandwidth is the product of slope and chirp time, 
$$(B)$$ = Chirp time $$(t_c)\times $$ Chirp Slope $$(S)$$. 

<p align="center">
<img src="/assets/images/SBF.png" alt="Frequency over time for one chirp: a line rising over time $$t_c$$ with slope S and bandwidth B" width="200">
</p>
  
Let $$f_c$$ be the starting frequency of the chirp and let $$0 \leq t \leq t_c$$ be the time elapsed since the chirp began, then the instantaneous frequency $$f(t)$$ at any point during the chirp is $$f(t) = f_c + S t$$. In particular, since frequency increases linearly over time, when $$t = t_c$$ the chirp reaches its maximum frequency $$f_{\max}= f_c + St_c = f_c +B$$. Consider the following example,

For example, suppose $$f_c = 77~\text{GHz}$$, $$t_c = 40~\mu\text{s}$$, and $$B = 1.5~\text{GHz}$$. Then the chirp slope is
$$S = \frac{B}{t_c} = \frac{1.5~\text{GHz}}{40~\mu\text{s}} = 37.5~\text{MHz}/\mu\text{s} = 3.75\times10^{13}~\text{Hz/s},$$
and
$$f_{\max} = f_c + B = 77~\text{GHz} + 1.5~\text{GHz} = 78.5~\text{GHz}.$$
So the chirp sweeps from $$77~\text{GHz}$$ to $$78.5~\text{GHz}$$. Shortly we will see the bandwidth $$B$$ sets how finely we can resolve range (tell two close objects apart).

<!--
The key observation is that frequency is proportional to range meaning that if we have sufficient range resolution, we can essentially decompose our IF signal into tones that correspond to targets, and their frequency will give us their range (distance from radar).


-->


# Beat Frequency
In the image below we see the overlap of the TX chirp and the RX chirp. The RX chirp has a **time delay** of $$\tau = \frac{2R}{c}$$ from its roundtrip to and back from an object at **range** $$R$$ (distance from the radar). The IF signal is the whole filtered mixer output, the frequency of that sinusoid is the **beat frequency**. You may notice that IF signal and beat frequency are used interchangably. We will do that too, but its important to know the nuance so you can infer what an author means from context correctly.

When the TX and RX chirps overlap, the beat frequency is constant and equals the vertical distance between chirps $$f_b = S\tau = \frac{2SR}{c} = \frac{2BR}{ct_c}$$. Outside of the overlap, the frequency is attenuated by the low pass (LP) filter and ADC. The main take away is that **the frequencies of tones in the beat signal are directly proportional to the range of objects.** As a result, our maximum beat frequency depends on the maximum range we would like our radar to see. Let $$R_{\max}$$ be this maximum range, and let $$f_{b\max}$$ be the maximum beat frequency. Then from the equations we have, $$f_{b\max}=  \frac{2SR_{max}}{c} = \frac{2BR_{\max}}{ct_c}$$. We can see the bandwidth is proportional to maximum beat frequency as well (for a fixed $$t_c$$ and $$ R_{\max}$$).

<p align="center">
<img src="/assets/images/RF_to_IF.png" alt="phase_shift" width="800">
</p>

Notice that to accurately measure the range of an object (for a range up to $$R_{\max}$$), the beat frequency of our farthest object, $$f_{b\max} = \dfrac{2SR_{\max}}{c}$$, must pass through the LP filter. So the filter's cutoff frequency, $$f_{LP}$$, must be at least $$f_{b\max}$$. 

The ADC sampling rate is another limitation. The ADC measures the analog input voltage a fixed number of times per second. This rate, denoted $$f_s$$, is called the sampling rate and is given in samples per second (SPS), equivalently Hz. To avoid distortion called aliasing, $$f_s$$ must be more than twice the largest frequency that reaches the ADC, so we need $$f_s > 2 f_{b\max}$$ (the Nyquist condition). Aliasing occurs when a frequency above $$f_s/2$$ "folds" and appears as a lower frequency due to sampling rate being too low. A farther away target can show up at a false, closer range (depending on how far above $$f_s/2$$ the frequency is). In practice, the IF signal enters the LP filter before the ADC, so good practice sets the LP filter cutoff between the two limits: $$f_{b\max}\le f_{LP}< f_s/2$$. Since $$f_{b\max} = \frac{2SR_{\max}}{c}$$, the sampling rate limits the maximum range: $$R_{max} < \frac{f_s~c}{4S}.$$ This formula assumes real-valued sampling, which is consistent with the radar in the study we care about (8192 samples per chirps give 4096 range bins and the maximum beat frequency matches $$f_s/2 = $$.



# Fourier Transform
<!--

The bandwidth $$B$$ determines the range resolution. A radar's **range resolution** is the system's ability to distinguish between two different targets located at the same angle from the radar, but at slightly different distances from it. If the distances between two objects is smaller than the range resolution of the radar, their signals overlap and appear as a single target in processing. We will explain why that is later. For now, let $$c = 3\times 10^8~\text{m/s}$$ be the **speed of light**,

the **range resolution formula is** $$\Delta R = \frac{c}{2B}$$. For our example, the range resolution is 
$$\Delta R = \frac{c}{2B} = \frac{3\times 10^8~\text{m/s}}{2(1.5\times 10^9~\text{Hz})} = .1 m$$.

This means that objects within $$.1$$ meters will have signals that overlap too closely for to be seperated in processing, and are identified as one target. 

For now, notice we can maintain the same bandwidth by increasing our chirp slope, which in turns means a shorter chirp time. These are all important considerations for trade-offs required by system limitations which we will expand on later. 
-->


# Range Resolution





<!--


Let $$f$$ be the frequency in cycles per second (Hz) and $$\omega$$ the angular frequency in radians per second. Recall that angle grows steadily with time, $$\theta = \omega t = 2\pi f t$$. By substituion, $$A\sin(\theta) = A\sin(2\pi ft + \phi)$$, which we can plot as amplitude $$A$$ as a function of time $$t$$, where the peaks go from $$-A$$ and $$A$$ as above. 
-->
  
# Our Study

## Overview

## Approach
- 
- 
- Tools/libraries: 
## Summary of Results





## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).

