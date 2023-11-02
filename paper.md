---
title: 'Quantitative T1 MRI'
tags:
  - MRI
  - qMRI
  - Quantitative
  - T1
  - Relaxometry
authors:
  - name: Mathieu Boudreau
    orcid: 0000-0002-7726-4456
    affiliation: "1, 2"
  - name: Kathryn E. Keenan
    orcid: 0000-0001-9070-5255
    affiliation: "3"
  - name: Nikola Stikov
    orcid: 0000-0002-8480-5230
    affiliation: "1, 2"
affiliations:
- name: Montreal Heart Institute, Montreal, Quebec, Canada
  index: 1
- name: NeuroPoly, Polytechnique Montreal, Quebec, Canada
  index: 2
- name: Physical Measurement Laboratory, National Institute of Standards and Technology, Boulder, Colorado
  index: 3
date: 20 July 2021
bibliography: paper.bib
---


# Summary

This NeuroLibre Reproducible preprint is an interactive tutorial on quantitative T1 mapping MRI. It is an interactive version of two subsections of the chapter "Quantitative T1 and T1r Mapping" in the book Quantitative Magnetic Resonance Imaging [@Boudreau2019].

 \awesomebox[red]{2pt}{\faExclamationCircle}{red}{\textbf{NOTE}}

 > The following section in this document repeats the narrative content exactly as                     found in the [corresponding NeuroLibre Reproducible Preprint (NRP)](https://preprint.neurolibre.org/10.55458/neurolibre.00019). The content was                     automatically incorporated into this PDF using the NeuroLibre publication workflow [@Karakuzu2022-nlwf] to                     credit the referenced resources. The submitting author of the preprint has verified and approved the                     inclusion of this section through a GitHub pull request made to the [source repository](https://github.com/qMRLab/t1-book-neurolibre) from which this document was built.                     Please note that the figures and tables have been excluded from this (static) document. **To interactively explore such outputs and re-generate them, please visit the corresponding [NRP](https://preprint.neurolibre.org/10.55458/neurolibre.00019).**                     For more information on integrated research objects (e.g., NRPs) that bundle narrative and executable content for reproducible and transparent publications,                     please refer to @Dupre2022-iro. NeuroLibre is sponsored by the Canadian Open Neuroscience Platform (CONP) [@Harding2023-conp].


About
======



This Jupyter Book is a series of interactive tutorials about quantitative T1 mapping, powered by [qMRLab](https://qmrlab.org) [@Karakuzu2020-ul].



Acknowledgements
-----------------



This work was supported by the Canadian Open Neuroscience Platform [(CONP)](http://conp.ca), the Quebec Bio-Imaging Network [(QBIN)](https://www.rbiq-qbin.qc.ca/), and the Montreal Heart Institute Foundation [(MHI)](https://www.icm-mhi.org/en/foundation).



Inversion Recovery T1 Mapping
==============================



Widely considered the gold standard for T1 mapping, the inversion recovery technique estimates T1 values by fitting the signal recovery curve acquired at different delays after an inversion pulse (180°). In a typical inversion recovery experiment ([Figure 1](https://preprint.neurolibre.org/10.55458/neurolibre.00019)), the magnetization at thermal equilibrium is inverted using a 180° RF pulse. After the longitudinal magnetization recovers through spin-lattice relaxation for predetermined delay (“inversion time”, TI), a 90° excitation pulse is applied, followed by a readout imaging sequence (typically a spin-echo or gradient-echo readout) to create a snapshot of the longitudinal magnetization state at that TI.



Inversion recovery was first developed for NMR in the 1940s [@Hahn1949; @Drain1949], and the first T1 map was acquired using a saturation-recovery technique (90° as a preparation pulse instead of 180°) by [@Pykett1978]. Some distinct advantages of inversion recovery are its large dynamic range of signal change and an insensitivity to pulse sequence parameter imperfections [@Stikov2015]. Despite its proven robustness at measuring T1, inversion recovery is scarcely used in practice, because conventional implementations require repetition times (TRs) on the order of 2 to 5 T1 [@Steen1994], making it challenging to acquire whole-organ T1 maps in a clinically feasible time. Nonetheless, it is continuously used as a reference measurement during the development of new techniques, or when comparing different T1 mapping techniques, and several variations of the inversion recovery technique have been developed, making it practical for some applications [@Messroghli2004; @Piechnik2010].



**[Figure 1](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Pulse sequence of an inversion recovery experiment.**

  


Signal Modelling
-----------------



The steady-state longitudinal magnetization of an inversion recovery experiment can be derived from the Bloch equations for the pulse sequence {θ180 – TI – θ90 – (TR-TI)}, and is given by:



\begin{equation}\label{eq:1}
M\_{z}(TI) = M\_0 \frac{1-\text{cos}(\theta\_{180})e^{- \frac{TR}{T\_1}} -[1-\text{cos}(\theta\_{180})]e^{- \frac{TI}{T\_1}}}{1 - \text{cos}(\theta\_{180}) \text{cos}(\theta\_{90}) e^{- \frac{TR}{T\_1}}}
\end{equation}



where Mz is the longitudinal magnetization prior to the θ90 pulse. If the in-phase real signal is desired, it can be calculated by multiplying Eq. 1 by *k*sin(θ90)e-TE/T2, where *k* is a constant. This general equation can be simplified by grouping together the constants for each measurements regardless of their values (i.e. at each TI, same TE and θ90 are used) and assuming an ideal inversion pulse:



\begin{equation}\label{eq:2}
M\_z(TI) = C(1-2e^{- \frac{TI}{T\_1}} + e^{- \frac{TR}{T\_1}})
\end{equation}



where the first three terms and the denominator of Eq. 1 have been grouped together into the constant C. If the experiment is designed such that TR is long enough to allow for full relaxation of the magnetization (TR > 5T1), we can do an additional approximation by dropping the last term in Eq. 2:



\begin{equation}\label{eq:3}
M\_z(TI) = C(1-2e^{- \frac{TI}{T\_1}})
\end{equation}



The simplicity of the signal model described by Eq. 3, both in its equation and experimental implementation, has made it the most widely used equation to describe the signal evolution in an inversion recovery T1 mapping experiment. The magnetization curves are plotted in [Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019) for approximate T1 values of three different tissues in the brain. Note that in many practical implementations, magnitude-only images are acquired, so the signal measured would be proportional to the absolute value of Eq. 3.



**[Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Inversion recovery curves (Eq. 2) for three different T1 values, approximating the main types of tissue in the brain.**



Practically, Eq. 1 is the better choice for simulating the signal of an inversion recovery experiment, as the TRs are often chosen to be greater than 5T1 of the tissue-of-interest, which rarely coincides with the longest T1 present (e.g. TR may be sufficiently long for white matter, but not for CSF which could also be present in the volume). Equation 3 also assumes ideal inversion pulses, which is rarely the case due to slice profile effects. [Figure 3](https://preprint.neurolibre.org/10.55458/neurolibre.00019) displays the inversion recovery signal magnitude (complete relaxation normalized to 1) of an experiment with TR = 5 s and T1 values ranging between 250 ms to 5 s, calculated using both equations.

**[Figure 3](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Signal recovery curves simulated using Eq. 3 (solid) and Eq. 1 (dotted) with a TR = 5 s for T1 values ranging between 0.25 to 5 s.**




Data Fitting
-------------



Several factors impact the choice of the inversion recovery fitting algorithm. If only magnitude images are available, then a polarity-inversion is often implemented to restore the non-exponential magnitude curves ([Figure 3](https://preprint.neurolibre.org/10.55458/neurolibre.00019)) into the exponential form ([Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019)). This process is sensitive to noise due to the Rician noise creating a non-zero level at the signal null. If phase data is also available, then a phase term must be added to the fitting equation [@Barral2010-qm]. Equation 3 must only be used to fit data for the long TR regime (TR > 5T1), which in practice is rarely satisfied for all tissues in subjects.



Early implementations of inversion recovery fitting algorithms were designed around the computational power available at the time. These included the “null method” [@Pykett1983], assuming that each T1 value has unique zero-crossings (see [Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019)), and linear fitting of a rearranged version of Eq. 3 on a semi-log plot [@Fukushima1981]. Nowadays, a non-linear least-squares fitting algorithm (e.g. Levenberg-Marquardt) is more appropriate, and can be applied to either approximate or general forms of the signal model (Eq. 3 or Eq. 1). More recent work [@Barral2010-qm] demonstrated that T1 maps can also be fitted much faster (up to 75 times compared to Levenberg-Marquardt) to fit Eq. 1 – without a precision penalty – by using a reduced-dimension non-linear least squares (RD-NLS) algorithm. It was demonstrated that the following simplified 5-parameter equation can be sufficient for accurate T1 mapping:



\begin{equation}\label{eq:4}
S(TI) = a + be^{- \frac{TI}{T\_1}}
\end{equation}



where *a* and *b* are complex values. If magnitude-only data is available, a 3-parameter model can be sufficient by taking the absolute value of Eq. 4. While the RD-NLS algorithms are too complex to be presented here (the reader is referred to the paper, (Barral et al. 2010)), the code for these algorithms [was released open-source](http://www-mrsrl.stanford.edu/~jbarral/t1map.html) along with the original publication, and is also available as a [qMRLab](https://github.com/qMRLab/qMRLab) T1 mapping model. One important thing to note about Eq. 4 is that it is general – no assumption is made about TR – and is thus as robust as Eq. 1 as long as all pulse sequence parameters other than TI are kept constant between each measurement. [Figure 4](https://preprint.neurolibre.org/10.55458/neurolibre.00019) compares simulated data (Eq. 1) using a range of TRs (1.5T1 to 5T1) fitted using either RD-NLS & Eq. 4 or a Levenberg-Marquardt fit of Eq. 2.



**[Figure 4](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Fitting comparison of simulated data (blue markers) with T1 = 1 s and TR = 1.5 to 5 s, using fitted using RD-NLS & Eq. 4 (green) and Levenberg-Marquardt & Eq. 2 (orange, long TR approximation).**




[Figure 5](https://preprint.neurolibre.org/10.55458/neurolibre.00019) displays an example brain dataset from an inversion recovery experiment, along with the T1 map fitted using the RD-NLS technique.




**[Figure 5](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Example inversion recovery dataset of a healthy adult brain (left). Inversion times used to acquire this magnitude image dataset were 30 ms, 530 ms, 1030 ms, and 1530 ms, and the TR used was 1550 ms. The T1 map (right) was fitted using a RD-NLS algorithm.**




Benefits and Pitfalls
----------------------



The conventional inversion recovery experiment is considered the gold standard T1 mapping technique for several reasons:


* A typical protocol has a long TR value and a sufficient number of inversion times for stable fitting (typically 5 or more) covering the range [0, TR].
* It offers a wide dynamic range of signals (up to [-*k*M0, *k*M0]), allowing a number of inversion times where high SNR is available to sample the signal recovery curve [@Fukushima1981].
* T1 maps produced by inversion recovery are largely insensitive to inaccuracies in excitation flip angles and imperfect spoiling [@Stikov2015], as all parameters except TI are constant for each measurement and only a single acquisition is performed (at TI) during each TR.



One important protocol design consideration is to avoid acquiring at inversion times where the signal for T1 values of the tissue-of-interest is nulled, as the magnitude images at this TI time will be dominated by Rician noise which can negatively impact the fit under low SNR circumstances ([Figure 6](https://preprint.neurolibre.org/10.55458/neurolibre.00019)). Inversion recovery can also often be acquired using commonly available standard pulse sequences available on most MRI scanners by setting up a customized acquisition protocol, and does not require any additional calibration measurements. For an example, please visit the interactive preprint of the ISMRM Reproducible Research Group 2020 Challenge on inversion recovery T1 mapping [@Boudreau2023].



**[Figure 6](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Monte Carlo simulations (mean and standard deviation (STD), blue markers) and fitted T1 values (mean and STD, red and green respectively) generated for a T1 value of 900 ms and 5 TI values linearly spaced across the TR (ranging from 1 to 5 s). A bump in T1 STD occurs near TR = 3000 ms, which coincides with the TR where the second TI is located near a null point for this T1 value.**




Despite a widely acknowledged robustness for measuring accurate T1 maps, inversion recovery is not often used in studies. An important drawback of this technique is the need for long TR values, generally on the order of a few T1 for general models (e.g. Eqs. 1 and 4), and up to 5T1 for long TR approximated models (Eq. 3). It takes about to 10-25 minutes to acquire a single-slice T1 map using the inversion recovery technique, as only one TI is acquired per TR (2-5 s) and conventional cartesian gradient readout imaging acquires one phase encode line per excitation (for a total of ~100-200 phase encode lines). The long acquisition time makes it challenging to acquire whole-organ T1 maps in clinically feasible protocol times. Nonetheless, it is useful as a reference measurement for comparisons against other T1 mapping methods, or to acquire a single-slice T1 map of a tissue to get T1 estimates for optimization of other pulse sequences.




Other Saturation-Recovery T1 Mapping techniques
------------------------------------------------



Several variations of the inversion recovery pulse sequence were developed to overcome challenges like those specified above. Amongst them, the Look-Locker technique [@Look1970] stands out as one of the most widely used in practice. Instead of a single 90° acquisition per TR, a periodic train of small excitation pulses θ are applied after the inversion pulse, {θ180 – 𝛕 – θ – 𝛕 – θ – ...}, where 𝛕 = TR/n and n is the number of sampling acquisitions. This pulse sequence samples the inversion time relaxation curve much more efficiently than conventional inversion recovery, but at a cost of lower SNR. However, because the magnetization state of each TI measurement depends on the previous series of θ excitation, it has higher sensitivity to B1-inhomogeneities and imperfect spoiling compared to inversion recovery [@Gai2013; @Stikov2015]. Nonetheless, Look-Locker is widely used for rapid T1 mapping applications, and variants like MOLLI (Modified Look-Locker Inversion recovery) and ShMOLLI (Shortened MOLLI) are widely used for cardiac T1 mapping [@Messroghli2004; @Piechnik2010].



Another inversion recovery variant that’s worth mentioning is saturation recovery, in which the inversion pulse is replaced with a saturation pulse: {θ90 – TI – θ90}. This technique was used to acquire the very first T1 map [@Pykett1978]. Unlike inversion recovery, this pulse sequence does not need a long TR to recover to its initial condition; every θ90 pulse resets the longitudinal magnetization to the same initial state. However, to properly sample the recovery curve, TIs still need to reach the order of ~T1, the dynamic range of signal potential is cut in half ([0, M0]), and the short TIs (which have the fastest acquisition times) have the lowest SNRs.



Variable Flip Angle T1 Mapping
===============================



Variable flip angle (VFA) T1 mapping [@Christensen1974; @Fram1987; @Gupta1977], also known as Driven Equilibrium Single Pulse Observation of T1 (DESPOT1) [@Homer1985; @Deoni2003], is a rapid quantitative T1 measurement technique that is widely used to acquire 3D T1 maps (e.g. whole-brain) in a clinically feasible time. VFA estimates T1 values by acquiring multiple spoiled gradient echo acquisitions, each with different excitation flip angles (*θn* for n = 1, 2, .., N and *θi* ≠ *θj*). The steady-state signal of this pulse sequence ([Figure 1](https://preprint.neurolibre.org/10.55458/neurolibre.00019)) uses very short TRs (on the order of magnitude of 10 ms) and is very sensitive to T1 for a wide range of flip angles.



VFA is a technique that originates from the NMR field, and was adopted because of its time efficiency and the ability to acquire accurate T1 values simultaneously for a wide range of values [@Christensen1974; @Gupta1977]. For imaging applications, VFA also benefits from an increase in SNR because it can be acquired using a 3D acquisition instead of multislice, which also helps to reduce slice profile effects. One important drawback of VFA for T1 mapping is that the signal is very sensitive to inaccuracies in the flip angle value, thus impacting the T1 estimates. In practice, the nominal flip angle (i.e. the value set at the scanner) is different than the actual flip angle experienced by the spins (e.g. at 3.0 T, variations of up to ±30%), an issue that increases with field strength. VFA typically requires the acquisition of another quantitative map, the transmit RF amplitude (B1+, or B1 for short), to calibrate the nominal flip angle to its actual value because of B1 inhomogeneities that occur in most loaded MRI coils [@Sled1998]. The need to acquire an additional B1 map reduces the time savings offered by VFA over saturation-recovery techniques, and inaccuracies/imprecisions of the B1 map are also propagated into the VFA T1 map [@Boudreau2017; @Lee2017].


**[Figure 1](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Simplified pulse sequence diagram of a variable flip angle (VFA) pulse sequence with a gradient echo readout. TR: repetition time, *θn*: excitation flip angle for the nth measurement, IMG: image acquisition (k-space readout), SPOIL: spoiler gradient.**
  


Signal Modelling
-----------------



The steady-state longitudinal magnetization of an ideal variable flip angle experiment can be analytically solved from the Bloch equations for the spoiled gradient echo pulse sequence {*θn*–TR}:



\begin{equation}\tag{1}
M\_{z}(\theta\_n) = M\_0 \frac{1-e^{- \frac{TR}{T\_1}}}{1-\text{cos}(\theta\_n) e^{- \frac{TR}{T\_1}}} \text{sin}(\theta\_n)
\end{equation}



where *Mz* is the longitudinal magnetization, *M*0 is the magnetization at thermal equilibrium, TR is the pulse sequence repetition time ([Figure 1](https://preprint.neurolibre.org/10.55458/neurolibre.00019)), and *θn* is the excitation flip angle. The *Mz* curves of different T1 values for a range of *θn* and TR values are shown in [Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019).



**[Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Variable flip angle technique signal curves (Eq. 1) for three different T1 values, approximating the main types of tissue in the brain at 3T.**



From [Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019), it is clearly seen that the flip angle at which the steady-state signal is maximized is dependent on the T1 and TR values. This flip angle is a well known quantity, called the Ernst angle [@Ernst1966], which can be solved analytically from Equation 1 using properties of calculus:

\begin{equation}\tag{2}
\theta\_{Ernst} = \text{acos}(e^{- \frac{TR}{T\_1}})
\end{equation}



The closed-form solution (Equation 1) makes several assumptions which in practice may not always hold true if care is not taken. Mainly, it is assumed that the longitudinal magnetization has reached a steady state after a large number of TRs, and that the transverse magnetization is perfectly spoiled at the end of each TR. Bloch simulations – a numerical approach at solving the Bloch *equations* for a set of spins at each time point – provide a more realistic estimate of the signal if the number of repetition times is small (i.e. a steady-state is not achieved). As can be seen from [Figure 3](https://preprint.neurolibre.org/10.55458/neurolibre.00019), the number of repetitions required to reach a steady state not only depends on T1, but also on the flip angle; flip angles near the Ernst angle need more TRs to reach a steady state. Preparation pulses or an outward-in k-space acquisition pattern are typically sufficient to reach a steady state by the time that the center of k-space is acquired, which is where most of the image contrast resides.



**[Figure 3](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Signal curves simulated using Bloch simulations (orange) for a number of repetitions ranging from 1 to 150, plotted against the ideal case (Equation 1 – blue). Simulation details: TR = 25 ms, T1 = 900 ms, 100 spins. Ideal spoiling was used for this set of Bloch simulations (transverse magnetization was set to 0 at the end of each TR).**



Sufficient spoiling is likely the most challenging parameter to control for in a VFA experiment. A combination of both gradient spoiling and RF phase spoiling [@Handbook2004; @Zur1991] are typically recommended ([Figure 4](https://preprint.neurolibre.org/10.55458/neurolibre.00019)). It has also been shown that the use of very strong gradients, introduces diffusion effects (not considered in [Figure 4](https://preprint.neurolibre.org/10.55458/neurolibre.00019)), further improving the spoiling efficacy in the VFA pulse sequence [@Yarnykh2010].

**[Figure 4](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Signal curves estimated using Bloch simulations for three categories of signal spoiling: (1) ideal spoiling (blue), gradient & RF Spoiling (orange), and no spoiling (green). Simulations details: TR = 25 ms, T1 = 900 ms, Te = 100 ms, TE = 5 ms, 100 spins. For the ideal spoiling case, the transverse magnetization is set to zero at the end of each TR. For the gradient & RF spoiling case, each spin is rotated by different increments of phase (2𝜋 / # of spins) to simulate complete decoherence from gradient spoiling, and the RF phase of the excitation pulse is ɸ*n* = ɸ*n*-1 + *n*ɸ0 = ½ ɸ0(*n*2 + *n* + 2) [@Handbook2004] with ɸ0 = 117° [@Zur1991] after each TR.**



Data Fitting
-------------



At first glance, one could be tempted to fit VFA data using a non-linear least squares fitting algorithm such as Levenberg-Marquardt with Eq. 1, which typically only has two free fitting variables (T1 and *M*0). Although this is a valid way of estimating T1 from VFA data, it is rarely done in practice because a simple refactoring of Equation 1 allows T1 values to be estimated with a linear least square fitting algorithm, which substantially reduces the processing time. Without any approximations, Equation 1 can be rearranged into the form **y** = m**x**+b [@Gupta1977]:



\begin{equation}\tag{3}
\frac{S\_n}{ \text{sin}(\theta\_n)} = e^{- \frac{TR}{T\_1}} \frac{S\_n}{ \text{tan}(\theta\_n)} + C (1-e^{- \frac{TR}{T\_1}})
\end{equation}



As the third term does not change between measurements (it is constant for each *θn*), it can be grouped into the constant for a simpler representation:



\begin{equation}\tag{4}
\frac{S\_n}{ \text{sin}(\theta\_n)} = e^{- \frac{TR}{T\_1}} \frac{S\_n}{ \text{tan}(\theta\_n)} + C
\end{equation}



With this rearranged form of Equation 1, T1 can be simply estimated from the slope of a linear regression calculated from *Sn*/sin(*θn*) and *Sn*/tan(*θn*) values:



\begin{equation}\tag{5}
T\_1 = - \frac{TR}{ \text{ln}(slope)}
\end{equation}



If data were acquired using only two flip angles – a very common VFA acquisition protocol – then the slope can be calculated using the elementary slope equation. [Figure 5](https://preprint.neurolibre.org/10.55458/neurolibre.00019) displays both Equation 1 and 4 plotted for a noisy dataset.



**[Figure 5](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Mean and standard deviation of the VFA signal plotted using the nonlinear form (Equation 1 – blue) and linear form (Equation 4 – red). Monte Carlo simulation details: SNR = 25, N = 1000. VFA simulation details: TR = 25 ms, T1 = 900 ms.**



There are two important imaging protocol design considerations that should be taken into account when planning to use VFA: (1) how many and which flip angles to use to acquire VFA data, and (2) correcting inaccurate flip angles due to transmit RF field inhomogeneity. Most VFA experiments use the minimum number of required flip angles (two) to minimize acquisition time. For this case, it has been shown that the flip angle choice resulting in the best precision for VFA T1 estimates for a sample with a single T1 value (i.e. single tissue) are the two flip angles that result in 71% of the maximum possible steady-state signal (i.e. at the Ernst angle) [@Deoni2003; @Schabel2008].

Time allowing, additional flip angles are often acquired at higher values and in between the two above, because greater signal differences between tissue T1 values are present there (e.g. [Figure 2](https://preprint.neurolibre.org/10.55458/neurolibre.00019)). Also, for more than two flip angles, Equations 1 and 4 do not have the same noise weighting for each fitting point, which may bias linear least-square T1 estimates at lower SNRs. Thus, it has been recommended that low SNR data should be fitted with either Equation 1 using non-linear least-squares (slower fitting) or with a weighted linear least-squares form of Equation 4 [@Chang2008].



Accurate knowledge of the flip angle values is very important to produce accurate T1 maps. Because of how the RF field interacts with matter [@Sled1998], the excitation RF field (B1+, or B1 for short) of a loaded RF coil results in spatial variations in intensity/amplitude, unless RF shimming is available to counteract this effect (not common at clinical field strengths). For quantitative measurements like VFA which are sensitive to this parameter, the flip angle can be corrected (voxelwise) relative to the nominal value by multiplying it with a scaling factor (B1) from a B1 map that is acquired during the same session:



\begin{equation}\tag{6}
\theta\_{corrected} = B\_1 \theta\_{nominal}
\end{equation}



B1 in this context is normalized, meaning that it is unitless and has a value of 1 in voxels where the RF field has the expected amplitude (i.e. where the nominal flip angle is the actual flip angle). [Figure 6](https://preprint.neurolibre.org/10.55458/neurolibre.00019) displays fitted VFA T1 values from a Monte Carlo dataset simulated using biased flip angle values, and fitted without/with B1 correction.



**[Figure 6](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Mean and standard deviations of fitted VFA T1 values for a set of Monte Carlo simulations (SNR = 100, N = 1000), simulated using a wide range of biased flip angles and fitted without (blue) or with (red) B1 correction. Simulation parameters: TR = 25 ms, T1 = 900 ms, *θnominal* = 6° and 32° (optimized values for this TR/T1 combination). Notice how even after B1 correction, fitted T1 values at B1 values far from the nominal case (B1 = 1) exhibit larger variance, as the actual flip angles of the simulated signal deviate from the optimal values for this TR/T1 (Deoni et al. 2003).**




[Figure 7](https://preprint.neurolibre.org/10.55458/neurolibre.00019) displays an example VFA dataset and a B1 map in a healthy brain, along with the T1 map estimated using a linear fit (Equations 4 and 5).




**[Figure 7](https://preprint.neurolibre.org/10.55458/neurolibre.00019). Example variable flip angle dataset and B1 map of a healthy adult brain (left). The relevant VFA protocol parameters used were: TR = 15 ms, *θnominal* = 3° and 20°. The T1 map (right) was fitted using a linear regression (Equations 4 and 5).**




Benefits and Pitfalls
----------------------



It has been well reported in recent years that the accuracy of VFA T1 estimates is very sensitive to pulse sequence implementations [@Baudrexel2017; @Lutti2013; @Stikov2015], and as such is less robust than the gold standard inversion recovery technique. In particular, the signal bias resulting from insufficient spoiling can result in inaccurate T1 estimates of up to 30% relative to inversion recovery estimated values [@Stikov2015]. VFA T1 map accuracy and precision is also strongly dependent on the quality of the measured B1 map [@Lee2017], which can vary substantially between implementations [@Boudreau2017]. Modern rapid B1 mapping pulse sequences are not as widely available as VFA, resulting in some groups attempting alternative ways of removing the bias from the T1 maps like generating an artificial B1 map through the use of image processing techniques [@Liberman2013] or omitting B1 correction altogether [@Yuan2012]. The latter is not recommended, because most MRI scanners have default pulse sequences that, with careful protocol settings, can provide B1 maps of sufficient quality very rapidly [@Boudreau2017; @Samson2006; @Wang2005].



Despite some drawbacks, VFA is still one of the most widely used T1 mapping methods in research. Its rapid acquisition time, rapid image processing time, and widespread availability makes it a great candidate for use in other quantitative imaging acquisition protocols like quantitative magnetization transfer imaging [@Cercignani2005; @Yarnykh2002] and dynamic contrast enhanced imaging [@Li2018; @Sung2013].




# References

