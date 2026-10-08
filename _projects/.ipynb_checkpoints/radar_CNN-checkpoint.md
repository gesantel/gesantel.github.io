---
title: "Lightweight CNNs for Drone, Car, and People Recognition from FMCW Range-Doppler Maps"
excerpt: "Extending DopplerNet (Roldan et al., IET Radar Sonar Navig., 2020) by creating a compact CNN for embedded radar."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/DopplerNet"
classes: compact-text
---

## FMCW Radar Background
FMCW stands for frequency modulated continuous wave. The name comes from the chirp transmitted by FMCW radars. A chrip is a continuous wave whose frequency is linearly modulated. In particular, a chirp is a sinusoidal wave. 
<p align="center">
<img src="/assets/images/FMCW_radar_diagram.drawio.png" alt="FMCW Diagram" width="600">
</p>
We will use the diagram above to understand how FMCW radars work. First, a synth/LO generates a chirp. The chirp is transmitted by the TX antenna. A chirp is then reflected by an object and received by the RX antenna(s). The received (by the RX ant.) chirp and transmitted (by the TX ant.) chirp is mixed in the mixer, resulting in an IF signal. From there the signal passes through a low pass filter which removes high frequency tones. The signal then continues to the ADC (analog-to-digital converter) for sampling. The ADC takes the IF signal (our continous analog signal from the real world), and samples it at specific time intervals, discretizing the signal into data we can perform calculations on. From there our discretized data goes to a DSP (digital signal processor) which can apply mathematical transformations like scaling, compression, and Fourier Transform. Fast Fourier Transform (FFT) is an optimized algorithm for computing a Fourier Transform; It is commonly used in signal processing. When we say Fourier Transfomr below, we mean FFT. We will now expand a bit on the finer details.

The chirp duration is also referred to as chirp time and we will denote it by $t_c$. The Bandwidth of an FMCW radar chirp we will denote by $B$. Plotting frequency over time, we have a linear relationship between chirp slope $S$, $B$, and $t_c$. 
<p align="center">
<img src="/assets/images/SBF.png" alt="Slope Diagram" width="200">
</p>
$Bandwidth (B) = Chirp time (t_c) \times Chirp Slope (S)$. Moreover, let $f_c$ be the starting (or carrier) frequency of the chirp, and $0\leq t \leq t_c$ be the elapsed time since start of chirp, then the instantaneous frequency $f(t)$ at any point during the chirp is $f(t) = f_c + S\cdot t$. At time $t = t_c$, $f(t_c) = f_max$ our maximum frequency (by linear increasing relationship). For example, if $f_c = 77 GHz$, $t_c = 40~\mu$s, then $f(t_c) = f_c + S\cdot t_c = f_c +B = f_max$. 


## Overview

## Approach
- 
- 
- Tools/libraries: 
## Summary of Results





## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).

