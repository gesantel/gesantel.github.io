---
title: "Sobel Filter Recovery and Inductive Bias of Three Architectures"
excerpt: "One or two sentence summary of the problem this project solves."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/LearningSobel"
classes: compact-text
---


## Overview
Sobel filter recovery under added Gaussian noise. Testing inductive bias of transformer, MLP, and CNN architectures.

## Approach
- Dataset choice was MNIST dataset. Sobel filter and Gaussian noise applied to training dataset. Architectures then trained and generate prediction on unaltered dataset. Prediction is evaluated against true convolved image (sobel operator(image)) using MSE. Visualizations show differences in
learned representations
- MNIST images were chosen to avoid adding any complexity from the images themselves. This decision was made to isolate the inductive bias of each architecture. MSE was chosen as our loss function because we wanted to converge to the pixel values. As such, you want your loss function to punish predictions further away from the mean (outliers), and this is what MSE does.
  
- Tools/libraries: Python, scikit-learn, Pandas, Seaborn, Numpy, matplotlib, Docker, FastAPI, Google Cloud Run

## Summary of Results
Transformers preformed so poorly with just the 3x3 Sobel kernel with no noise (and took SO long to train) that I decided not to evaluate it on a the parameter space of varied noise and kernel size. Our Transformer used self-attention to learn the relationship between pixels. However, sequences of pixel values share no inherent relationship in the way language does. More precisely, in language, meaning is context dependent and each word in a sentence contributes to the overall meaning. For images, patterns are local and outside of these neighborhoods, there is no relationship. Therefore, this architecture is making an assumption in the data to build a model and, as a result, performs poorly.

<img src="/assets/images/transformer_recovery.jpg" alt="Transformer Recovery" width="200">

When the signal is distorted by Gaussian noise with standard deviation > .150, it becomes unrecoverable for our MLP. The Sobel kernel action being a linear transformation means the MLP learns the kernel well until we add noise. Since a linear transformation is defined by its inputs and outputs, the more noise we add to the signal, the more we distort the mapping. Our MLP also has no smoothing or denoising and so as complexity and distortion increases, its ability to recover the kernel decreases proportionally.
<img src="/assets/images/MLP_phase.jpg" alt="MLP Phase Diagram MSE" width="100">
          
Our CNN clearly is built to learn the Sobel kernel and it did just that. The core computation step of our CNN is the convolutional layer described in the image processing basics section [here](https://georgesantellano.substack.com/p/transformers-struggle-to-see-the). Its structure mirrors the convolution with the Sobel operator, and as such, it is no surprise it did well. 
<img src="/assets/images/CNN_phase.jpg" alt="CNN Phase Diagram MSE" width="100">

Read the full write-up on my [Substack](https://georgesantellano.substack.com/p/transformers-struggle-to-see-the). 




## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).

