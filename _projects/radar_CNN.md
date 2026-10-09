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

# Chirp Bandwidth, Chirp Time, Chirp Slope
The chirp duration is also referred to as chirp time and we will denote it by $$t_c$$. The Bandwidth of an FMCW radar chirp we will denote by $$B$$. Plotting frequency over time, we have that Bandwidth $$(B)$$ = Chirp time $$(t_c)\times $$ Chirp Slope $$(S)$$, drawn below.


<p align="center">
<img src="/assets/images/SBF.png" alt="Slope Diagram" width="200">
</p>
  

Moreover, let $$f_c$$ be the starting frequency of the chirp, let $$0 \leq t \leq t_c$$ be the elapsed time since start of chirp, then the instantaneous frequency $$f(t)$$ at any point during the chirp is $$f(t) = f_c + S \cdot t$$. In particular, when $$t = t_c$$, we have that $$f(t_c) = f_{\text{max}}$$ our maximum frequency (by linearly increasing relationship). Consider the following example,

Suppose $$f_c = 77~\text{GHz}$$, $$t_c = 40~\mu\text{s}$$, and $$B = 1.5~\text{GHz}$$. Then the chirp slope is

$$S = \frac{B}{t_c} = \frac{1.5~\text{GHz}}{40~\mu\text{s}} = 37.5~\text{MHz}/\mu\text{s} = 3.75\times10^{13}~\text{Hz/s}.$$

Moreover, since $$B = S\,t_c$$,

$$f_{\max} = f_c + B = 77~\text{GHz} + 1.5~\text{GHz} = 78.5~\text{GHz}.$$

This means our chirp sweeps from $$77 \text{ GHz}$$ to  $$78.5 \text{ GHz}.$$

# Range Resolution 

The bandwidth $$B$$ determines the range resolution. A radar's range resolution is the system's ability to distinguish between two different targets located at the same angle from the radar, but at slightly different distances from it. If the distances between two objects is smaller than the range resolution of the radar, their signals overlap and appear as a single target in processing. We will explain why that is later. For now, let $$c = 3\times 10^8~\text{m/s}$$ be the speed of light,

the range resolution is given by $$\Delta R = \frac{c}{2B}$$ For our example, the range resolution is 

$$\Delta R = \frac{c}{2B} = \frac{3\times 10^8~\text{m/s}}{2(1.5\times 10^9~\text{Hz})} = .1 m$$.

This means that objects within $$.1$$ meters will have signals that overlap too closely for to be seperated in processing, and are identified as one target. 

For now, notice we can maintain the same bandwidth by increasing ours chirp slope, which in turns means a shorter chirp time. These are all important considerations for trade-offs required by system limitations. 

The key observation is that frequency is proportional to range meaning that if we have sufficient range resolution, we can essentially decompose our IF signal into tones that correspond to targets, and their frequency will give us their range (distance from radar). 

# IF signal
Now lets return to our IF signal. 






# Pre-Calculus Refresh 

<p align="center">
<img src="/assets/images/phase_shift.png" alt="phase_shift" width="800">
</p>




Let $$f$$ be the frequency in cycles per second (Hz) and $$\omega$$ the angular frequency in radians per second. Recall that angle grows steadily with time, $$\theta = \omega t = 2\pi f t$$. By substituion, $$A\sin(\theta) = A\sin(2\pi ft + \phi)$$, which we can plot as amplitude $$A$$ as a function of time $$t$$, where the peaks go from $$-A$$ and $$A$$ as above. 



## Overview

## Approach
- 
- 
- Tools/libraries: 
## Summary of Results





## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).

