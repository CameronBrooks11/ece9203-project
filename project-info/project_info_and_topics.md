# Project Information and Sample Project Topics

**ECE 9203/ECE 9023, Winter 2026**

The following is a sample list of topics that are suitable for the course project. Your job is to find at least one paper related to the chosen topic, get it approved by me, and implement the algorithm in MATLAB or on a DSP chip. If some other flavour of adaptive filtering whets your appetite, feel free to discuss it with me.

**NOTE:** You have to demonstrate your work prior to the submission of your report! If you use Generative AI for code generation and implementation, be prepared to explain every line of code. Projects will be graded for scope, complexity, and the amount of independent work performed.

---

## Subband adaptive filtering algorithms

Subband adaptive algorithms are useful for those applications which require adaptive filters with longer impulse responses. One popular application of subband adaptive filters is in echo cancellation algorithms in telecommunications field. Here the desired and reference inputs are split into different frequency regions and each region is independently tackled by an adaptive filter. A related topic is frequency-domain or transform-domain adaptive filters, where the adaptation rules are applied in an alternate domain as opposed to the time domain that we have been concentrating on.

## Adaptive filtering algorithms using higher order statistics

The algorithms that we will be discussing in the class are based on second-order statistics of the inputs. There are adaptive algorithms based on higher order statistics (third and fourth order) and they may perform better in certain situations.

## Multichannel adaptive filtering algorithms

In some adaptive filter applications, we have access to multiple inputs coming from multiple sensors. For example, in microphone array processing, there are multiple microphones acquiring the speech and noise data. We can utilize these multiple inputs in an adaptive filtering structure and enhance the performance of a single channel adaptive filter.

## Other adaptive filtering algorithms

Examples include FFT-based algorithms and the adaptive Laguerre filter.

## Nonlinear adaptive filtering algorithms

Once again, the lectures deal with linear adaptive filtering algorithms. However, in some practical applications, there is a nonlinear relationship between the desired and reference inputs. This necessitates the application of nonlinear adaptive filtering algorithms, which try to model both nonlinear and linear relationship between the inputs.

## Kalman filter applications

In the class, we covered the theory behind Kalman filter. The goal under this project topic is to look at practical applications of Kalman filter and demonstrate them to the class.

## Unscented Kalman filter and particle filters

For nonlinear and non-Gaussian applications, we need to look at solutions beyond Kalman filter. Unscented Kalman filter and particle filter are systems designed for these problems.
