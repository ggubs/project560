# Multiple event identification and characterization by retrospective analysis of structured data streams

## AKA Post-processing some sensor data to see what happened

### Section 1 - Intro

#### Bottom Line Up Front (BLUF) The authors take a multidimensional set of time domain data, use wavelet analysis tools to construct that data into a form that can be optimized for various parameters (event signature, event strength), and then use various ML algorithms to achieve that optimization. 

Assumptions:

System suitable for analysis have the following characteristics:
1. The system generates one or more "events" that occur during operation
2. The "event" occurs sporadically, and has some tail
3. The "event" is associated with a "small and limited" number of sensing signals. That is, a limited number of sensor are able to capture the event

Goals:
1. Characterize the event signatures in the signals AKA, develop a feature set for the signals 

Editors note: This is an area I feel comfortable embarking on a literature review, am deeply interested in the below as well

2. Identify events within the signals and estimate the strength or weight (or whatever metric makes sense) of the event

Mechanism for achieving stated Goals: 
Multiple Event Identification and Characterization (MEIC) Algorithm, which is an optimization algorithm. 

Case Study:
Methods applied to some industrial process that has to do with rolled steel.

Paper format:
Section 1: Everything above
Section 2: Literature review (potential goldmine?)
Section 3: System Model, MEIC formulation, closed form solutions (MATH HEAVY)
Section 4: Simulation Study
Section 5: Case study
Sectoin 6: Conclusions

### Section 2 - Literature Review

The author states that wavelet analysis has been used in this domain as early as the 90s. However, the case for this paper is of sparse events, and wavelets are not great for that.

The author presents the notion of Phase-I monitoring for multiple signal data, but I have no familiarity with this concept.

This paper presents a solution that solves both the monitoring problem for multiple sensed signals as well as the signature characterization problem. I.e, we can identify events in the signals, and then we can identify the specific type of event. Traditionally, these are treated as separate problems. 

The MEIC algorithm identifies events, then it estimates the signature of the events. TBD how that happens (Sect 3).

The MEIC algorithm here uses the Dictionary Learning Technique developed in Lee (2007) (NOTE: READ THIS ONE). This method "identifies events and describes their effect by directly formulating an optimization criterion." See Yan: 2017,2018 and Mou: 2021.

### Section 3 - The MEIC Method

Based on covariance matrix analysis. Some matrix of time-domain data S, where each column of S are the time-domain samples from a different sensor, is analyzed. Authors also consider Principle Component Analysis as a different method for, presumably, constructing some data matrix that has more identifiable features?

Note that the covariance of matrix S when no "event" (AKA signal energy) etc is the Identity matrix with the variance values for each row/column pair along the diagonal. This is because white noise is uncorrelated with itself, of course. The assumption is that events are correlated between the sensors. 

They use a wavelet representation for signals in this paper. I actually don't know much about wavelets so this could be cool.

SYSTEM MODEL (function of time t):
X = HBY
X -> Sensor Measurements
H -> "Collcetion of all basis"
B -> Event signatures (wavelet coefficients)
Y -> Event strengths

Optimize to solve for B and Y. 

Regularization,Lasso Penalty, Smoothing penalty all used in forming the optimization problem. Lots of assumptions made about the specific problem-space of industrial processing/manufacturing. Mainly that events are not discrete, but rather have long tails that persist throughout the process. Lot's of these details are domain specific. 

Also normalize using the Frobenius Norm. Classic Cauchy-Schwarz.

#### Section 3.2: The actual ML-based solutions algorithm: MEIC

The Problem is broken down in to 4 problems:
(1) Dictionary learning problem. Solved using Blockwise Coordinate Descent via Alternative-Direction Method of Multipliers. This sets out to iteratively update B and Y until they converge to a value. THIS IS NOT A CONVEX SPACE. SO INITIALIZATION PLAYS A LARGE ROLE. Only converges to local optima.

(2) Updating B. An optimization problem is presented, minimizing the Frobenius Norm Squared of ||X-HBY|| (plus the penalty factors). This is solved using the ADMM Consensus algorithm.

(3,4) These problems take the same form. Another optimization using Algorithm 2, the ADMM consensus algorithm. 

This is low-key hard to follow, but the bottom line is that a variety of ML optimization algorithms are used in tandem to optimize the problem briefly described in these notes. 

### Section 4 and onwards

Lots of details about simulations and demonstrations of the procedure. Pretty neat visual representations of various event signatures (output of the above Aglos). Cool stuff.

# Deep Learning Based Automatic Modulation Classification with Blind OFDM Parameter Estimation

## Section 1: Intro
In an attempt to further dynamic spectrum access (DSA) technology, the authors propose a signal classifier and parameter estimator that can be used to aid in spectrum allocation. Dynamic channels make it difficult to classify signals within a wide spectrum, and in this case conventional classification methods (cyclostationarity) will fail.

For comms signals, one can use raw IQ data without "hand crafted features" in DL based AMC schemes. OFDM presents a problem for this approach due to its multi-carrier nature, and the fact that it overlaps in frequency. 

The authors propse a CNN-AMC that uses an FFT bank to identify signals in the frequency domain as opposed to time-domain IQ. This is an example of using signal analysis tools to extract features from a dataset. 

## Section 2: Related Work

Offers a very robust survey of OFDM parameter estimation related papers. Typical approaches include the obvious: autocorrellation based synchronization, 2nd cyclostationary hypothesis testing. 

The authors then review other ML focused papers. There are so many, and they all take wildly different approaches that range from (1) using raw IQ, (2) applying various transforms to the data (3) visualizing the data as an image and running it through an image classifier (4) untold variations of every ML algorithm you can imagine. 

## Section 3: System Model

Typical OFDM system model. Highlights:
(1) OFDM symbols are transmittied in the frequency domain, neccesitating an FFT bank
(2) The mod-scheme of the underlying comms waveform is unknown.
(3) SISO and MIMO systems are considered
(4) Standard multipath fading channel model.

## Section 4: Signal Classification Models
The classification model utilizes the second-order cyclostationarity tests to estimate the OFDM symbol length (CAF function). 

The model they developed uses a CNN (study this model) with IQ samples and "Long-Short-Term-Memory". The authors go on to describe how they normalized their data, as well as other layers of the model. They used words that probably mean something: SoftMax, LSTM, AP. This model estimates the OFDM symbol lengths.

A second model, an FFT-Bank based CNN is used to classify the single carrier modulations that the OFDM signal is leveraging. 

There is a nice table showing the size of each layer of the network: RelUs, Max Pools, Softmax, etc. 

This proposed model allegedly achieves good performance for discriminating OFDM symbol lengths and classifying the underlying modulation. The time/freq domain analysis occurs simultaneously such that the estimation/classification problems are solved in tandem using the same principles as the WDMF (review citation).

## Section 5: Dataset Generation

They used an existing dataset and trained their network with it. Various mod-schemes are discussed. Typical QAM/QPSK. 

## Section 6: Results
The authors use stochastic gradient descent with momentum to optimize the results of their neural network, outlining the parameters with which they initialized their model. They discuss other parameterizations (FFT window length, Number of FFT bins, etc) and present results for various combinations of parameters. Eventually, they settle on what they consider the optimal configuration. 

Results are dicussed for different channel models.
Constraints are discussed as well, one major one being that the signal lengths have to be long compared to other methods mentioned previously. This is due the requirement of a minimum spectral resolution for good classiciation. 

Results are honestly not that great, but show promise. 

# Real-Time Monitoring of High-DimensionalFunctional Data Streams via Spatio-TemporalSmooth Sparse Decomposition

## AKA Processing real-time data to identify anomalies / events

### Section 1 Intro

There is lots of data of high:
(1) Variety
(2) Dimensionality
(3) Velocity
(4) Spatio-temportally structured -- i.e. spatial and temporal correlation of signals across sensors

Examples of data meeting these properties are given, there are many. This paper considers sensors/datas that can be visualized as an image. I.e. IR image sensors.

### Section 2 Lit Review

Other papers have proposed more traditional methods of detecting events, such as:

(1) CUSUM energy detection and thresholding, adaptively. Treats datastreams as independent and so doesn't leverage spatio-temporal correlation between signals.
(2) PCA has been used to address high dimensionality constraints. 
(3) Tensor-based PCA that can model the spatial and spectral structures of data-stream images

Note: PCA cannot be used for non-stationary signals (statistics vary with time)

In total, none of these methods are fully equipped to deal with the issues presented in section 1.


### Section 3 Spatio-Temporal Smooth Sparse Decomposition

#### AKA, the interesting signal-processing-esque signal decomposition method that conditions the data streams for ML methods on convex data

The 1D data streams, y, for p sensors are organized into matrix Y = [p x n]
y is decomposed into 3 components, mu, alpha, and e, all vectors, where y = mu + alpha + e (functional mean, anomalies, noise)
The assumption here is the a, anomalies, are sparse in the data set. 
The mean and anomalies are further deconstructed into their component spatio and temporal bases: B_t and B_s, e.g. mu = (B_mu_t x B_mu_s)theta, where theta is the spatio-temporal coefficient of mu. x here is the tensor product. 
Theta is the parameter of merit that ST-SSD minimizes over. 

The ST-SSD model is proposed using the above parameters. It is complicated so I will not transcribe, but it is a minimization problem of theta_mu and theta_a. 
The authors describe the problem space a bit more, and then present the algorithms used to achieve the minimization.They propose using a descent method called Accelerated Proximal Gradient Descent. The authors assert it always converges as proved in a prior publication.

### Section 4 ST-SSD For Streaming Data And Recursive Estimation

In section for, they note that the algorithm proposed above is not suited for streaming data, but rather for a fixed dataset y (defined above). They do the work to extend the methods to streaming data, but I am considering that extraneous detail here.

### Sections 5,6,7

These sections correspond to a simulation study, a case study, and the extension of the model to an "Online" version (discussed in class). Not in scope, imo. 



