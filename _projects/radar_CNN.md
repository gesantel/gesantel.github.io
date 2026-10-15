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

To skip straight to my project, head down to "Our Study."
  
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

The ADC sampling rate is another limitation. The ADC measures the analog input voltage a fixed number of times per second. This rate, denoted $$f_s$$, is called the sampling rate and is given in samples per second (SPS), equivalently Hz. To avoid distortion called aliasing, $$f_s$$ must be more than twice the largest frequency that reaches the ADC, so we need $$f_s > 2 f_{b\max}$$ (the Nyquist condition). Aliasing occurs when a frequency above $$f_s/2$$ "folds" and appears as a lower frequency due to sampling rate being too low. A farther away target can show up at a false, closer range (depending on how far above $$f_s/2$$ the frequency is). In practice, the IF signal enters the LP filter before the ADC, so good practice sets the LP filter cutoff between the two limits: $$f_{b\max}\le f_{LP}< f_s/2$$. Since $$f_{b\max} = \frac{2SR_{\max}}{c}$$, the sampling rate limits the maximum range: $$R_{max} < \frac{f_s~c}{4S}.$$ This formula assumes real-valued sampling, which is consistent with the radar in the study we care about (8192 samples per chirps give 4096 range bins and the maximum beat frequency matches $$f_s/2$$.



# Range/Range FFT
The beat tone of an object at range $$R$$ exists only when the TX and RX chirps overlap. Precisely, for some time $$\tau \le t \le t_c$$ where $$\tau $$ is the time delay between TX and RX chirp. The ADC samples a window inside that overlap, taking $$N$$ samples at rate $$f_s$$. We define the observation window length $$T$$ as 

$$T = \frac{N}{f_s}.$$

To capture objects up to a range of $$\text{R}_{\max}$$, the observation window must occur after the longest delay $$\tau_{\max} = \frac{2R_{\max}}{c}$$, and end by $$t_c$$. During this window, the beat frequency is exactly $$f_b = S\tau$$, and the beat frequency changes by $$ST$$. We call $$B_T := ST$$ the **effective bandwidth** and it approximates $$B$$ for $$\tau_{\max} \ll t_c.$$

A time domain sequence of voltage measurements is sampled from the beat frequency by the ADC. A Fourier transform converts this time domain sequence to the frequency domain. Since every object at range $$\text{R}$$ contributes a tone at beat frequency $$f_b = \frac{2SR}{C}$$, finding the frequencies present in the beat signal tells us the ranges present in the scene. 

Suppose for simplicity there is only one object reflecting a chirp (at range $$R$$). In practice, a single target like a car is comprised of multiple objects that all contribute tones to the beat frequency. If the distance between target components is less than the range resolution, their peaks merge into one in the range FFT; if the components are further apart than the range resolution, they show up as individual peaks in the range FFT. We will talk about that soon. 

Let $$t_0$$ be the ADC start time. The samples are taken at times $$t_n = t_0 + n/f_s$$ for $$n = 0,1,\dots, N-1$$. For the one object case, the sampled tone measured at time $$t_n$$ is given by

$$x[n] = A\cos(2\pi f_b \frac{n}{f_s} + \phi_0),$$  

where the constant phase absorbs the start time $$t_0$$.

The FFT computes the discrete Fourier transform 

$$X[k] = \sum_{n=0}^{N-1}x[n]e^\frac{-2\pi j k n}{N},$$

for $$k = 0,1,\dots, N-1$$. Its magnitude, $$ \lvert X[k]\rvert$$,
measures how much of the signal oscillates at frequency $$f_k = \frac{kf_s}{N}= \frac{k}{T}$$.


<p align="center">
<img src="/assets/images/RFFT.png" alt="image showing beat frequency to RFFT" width="900">
</p>

A range-FFT is plotted with frequency bins (equivalently range bins) on the horizontal axis, and magnitude on the vertical axis. Since the samples are real-valued, their signal contains positive and negative frequencies with identical magnitudes, the positive falling in the first $$N/2$$ bins (frequencies up to $$f_s/2$$), and the other in the last $$N/2$$. This means that for real-valued samples the data is symmetric and as a result, only the first $$N/2$$ bins are plotted (to prevent false or "phantom" object detections). Another type of sampling is called I/Q sampling which stands for In-phase Quadrature. In IQ sampling, a dual-channel (or two) ADCs take in two streams of data: the in-phase or real part of the signal mixed with a standard cosine reference wave, and the quadrature, or the imaginary part of the signal mixed with a 90 degree phase-shifted sine reference wave. I/Q sampled data is complex and does not have the same symmetric or mirrored potential source of error. 

Think of the bins as follows. There are $$N$$ bins, bin $$k$$ represents frequency $$ f_k = k/T$$. Adjacent bins are $$\frac{1}{T}$$ apart in frequency, which corresponds to $$\dfrac{c}{2ST}$$ in range. The latter value $$\dfrac{c}{2ST} = \dfrac{c}{2B_T}$$ is the radar's **range resolution**, the minimum distance of seperation required between objects along the same line of sight for the radar to distinguish between them. A tone at $$f_b$$ produces a peak centered near $$k \approx f_bT$$, with a height proportional to the tone's amplitude $$A$$. Since $$f_bT$$ is not neccessarily an integer, the peak usually falls between two bins and has energy that radiates across neighboring bins (spectral leakage). The mixed signal contains noise which shows as the flat, low, wiggly baseline between peaks. This is sometimes called the noise floor. 


This process is extended easily for several objects. Suppose there are three objects at ranges $$R_1$$, $$R_2$$, and $$R_3.$$ Each object will reflect the TX chirp with its own delay, tone frequency, and phase, and the mixer output is the sum of the three tones. Since the FFT is linear, the spectrum of a sum is the sum of the spectra. Each tone contributes its own peak. If any two objects are less than the range resolution apart, their peaks merge to one peak. Otherwise, each peak will represent one object. In this example, three objects at ranges $$R_1$$, $$R_2$$, and $$R_3$$. 


# Doppler FFT




<!--


Let $$f$$ be the frequency in cycles per second (Hz) and $$\omega$$ the angular frequency in radians per second. Recall that angle grows steadily with time, $$\theta = \omega t = 2\pi f t$$. By substituion, $$A\sin(\theta) = A\sin(2\pi ft + \phi)$$, which we can plot as amplitude $$A$$ as a function of time $$t$$, where the peaks go from $$-A$$ and $$A$$ as above. 
-->
  
# Our Study

## Overview
DopplerNet (Roldan et al., IET Radar Sonar Navig., 2020) proposed an embedded CNN architecture for real-time target identification. Their model is structured as follows: a first layer convolutional layer with kernel size 3 and 32 filters (out-channels) with a ReLu, a flattening layer, and a stack of dense (fully-connected) linear layers (three concatenated with ReLu functions, and the last with a softmax). They performed gridsearch paramter tuning on filters and kernel size. The [data]( https://www.kaggle.com/iroldan/real-doppler-raddar-database) is organized in folders as class(drone, person, drone)/(recording)session/frames. Each frame is an 11x61 range-Doppler grid converted to dbB. Roldan et al. stacks three (they tune this number well) frames as channels. 

<p align="center">
<img src="/assets/images/stacked.png" alt="range-doppler-stacked" width="300">
</p>

They normize their data to mean 0 and standard deviation 1, and they train using a random five-fold cross validation. They evaluate their model on Accuracy, Precision, recall, and compare across industry embedded systems by number of parameters. 
<p align="center">
<img src="/assets/images/paper_model_metrics.png" alt="roldan's-model-metrics" width="600">
</p>

## Approach
* **Data:** I follow the same set-up as DolpplerNet, stacking (across channels axis) three temporally adjacent frames (windows) to form (3,11,61) tensors so that the model can learn motion (micro-Doppler) patterns over time. Each frame is an 11x61 range-Doppler grid in dBm: the 11 range cells by 61 Doppler bins, cropped using CFAR detection. 
* **Train and split:** I split train, validation, and test by (recording) session, not randomly. Since the windows overlap, a random split can leak near identical frames across across splits. Splitting at the session level is stricter and prevents this type of leakage.
* **Preprocessing** A custom PyTorch `Dataset` builds sliding windows of consecutive frames, parses and normalizes each CSV once using training set statistics. 
* **Models and Training:** I trained a 2.8M parameter CNN and a lightweight 40k parameter shallow CNN. Inputs are normalized with training statistics. I used cross entropy with class-balancing loss (Cui et al.), and used cross entropy with inverse-frequency class weighting as my baseline. I swept 12 Gaussian noise (Gaussian perturbation of dB values) and gain-shift ($$\pm 3 dB$$) augmentation parameter pairings, choosing the best by validation accuracy. I applied Adam optimizer (learning rate $$1e-3$$), batch size 64, 50 epochs, `ReduceLROnPlateau` scheduler (factor 0.5, patience 4), and fixed random seeds.

## Results
The deeper model reached 97.4% test accuracy (macro F1 .97), and the shallow one reached 97.1%, which is comparable with approximately 70x fewer parameters. A lightweight CNN model like I have proposed is valuable in a situation with limited processing resources. This improvement on training and drastic reduction on number of parameters is a good step towards lightweight embedded real-time object identification in limited processing resource situations.  
## Tools/libraries
Pytorch (torch, torch.nn, torch.optim, Dataset, and DataLoader). Numpy, Pandas, scikit-learn, Matplotlib, Jupyter Notebook. 


## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).

