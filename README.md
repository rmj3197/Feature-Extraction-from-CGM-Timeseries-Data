# Feature-Extraction-from-CGM-Timeseries-Data

## I. Introduction

Continuous Glucose Monitoring (CGM) is a method to track glucose levels in a
patient round the day. CGM systems take glucose measurements at regular
intervals and generates a series of dynamic data. The CGM system can calculate
the rate of change in glucose levels and understand the direction in which it is
moving. CGM lets users proactively manage glucose highs and lows and can give
added insight into impacts due to meals. In this project we have real world data
from a CGM system and want to intuitively guess features which are important.
We have data for glucose levels for every five minutes for 2.5 hours during lunch.
The data starts 30 minutes before meal intake and continues up to 2 hours after
meal consumption. There are multiple time series of multiple patients.

## II. Data Pre-processing

We have used two types of data for this project – CGMSeriesLunchPat* and
CGMDatenumLunchPat* where the timestamps and the glucose levels are stored
respectively. The first task was to read the datafiles for all the patients and convert
the machine-readable timestamps to human readable timestamps. Furthermore, as
the timestamps and the corresponding glucose values were stored in different files,
these data were merged for convenience using pandas. Being real world data,
another issue was missing glucose level values in certain timestamps of patients. If
the number of missing values were greater than or equal to 50% of total number of
glucose measurements for each time series then that time series was dropped, else

the missing values were interpolated. But the interpolate function in pandas doesn’t
work if data at the beginning is missing, such timeseries data was also dropped.
The introduction of interpolated values might add error to the dataset.

## III. Features

This project requires us to extract four different types of features from the time series data provided. 
The four different types of features extracted are –

i. Peaks using Fast Fourier Transformation
ii. CGM Velocity
iii. Auto Correlation
iv. Polyfit Regression Coefficients

## IV. Detailed Discussion on the Features

### i. Fast Fourier Transformation

Intuition: A fast Fourier transformation is an algorithm that computes the
discrete Fourier transformation of a signal or sequence. This is often used in
converting a signal into frequency domain from the time or space domain.
FFT can capture peaks in a signal. Generally, after having a meal, the CGM
value should rise. FFT will be able to capture such peaks.
fftpack from SciPy was used to detect the peaks. In this project the 2 nd, 3rd and
4 th peak values are considered, and the first peak is not considered as it considers
the DC component of the signal. Moreover, the FFT produces a mirrored plot,
which essentially captures the crest and troughs of a signal.

### ii. CGM Velocity Calculation

Intuition: CGM Velocity is the change rate of change of CGM value.
Intuitively speaking, the glucose level should change when a person has a meal.
Hence calculating the CGM Velocity, we can estimate meal intake as there will
be sudden change in the glucose level in a time series.

In this project the top four values of CGM velocity as considered as a feature.

CGM 1 – Maximum value of CGM Velocity, CGM 2 - 2 nd maximum value of
CGM Velocity, CGM 3 - 3 rd maximum value of CGM Velocity , CGM 4 - 4 th
maximum value of CGM Velocity

### iii. Autocorrelation

Intuition: The similarity between a time series and a lagged version of the same
time series is called autocorrelation. We need to specify a parameter called
lagged which is the difference in the time periods of the original and the recreated
signal after certain time intervals specified by lag. In this project, various values
of Lag are used from Lag = 2 to 9.
The autocorrelation is useful for estimating the future values of glucose levels
from the current glucose levels. Say for example, if there exist a high value of
autocorrelation between the lagged and the leading time series then the leading
time series will match considerably with the lagged time series, thus highlight
the similarity in trends. Autocorrelation analysis helps to identify trends in a
given time series.

### iv. Polyfit Regression

Intuition: Complex non-linear relationships are represented by
polynomial curves. Using polynomial fit, we can fit out time series data
and try to represent the CGM values as accurate as possible. Polyfit
regression employs the least-squares method to minimize the error while
fitting the original data. We can use the fitted curve to estimate future
glucose levels and also calculate future CGM velocities. The order of the
polynomial is very important here. Lower order polynomial is relatively
flat and smooth as compared to higher order polynomials. We have
selected the degree of the polynomial to be 6.

## V. Principal Component Analysis

All the above-mentioned features were calculated for the five patients and
an initial feature matrix of size ( 199 x 23) was generated. Before applying
the PCA on this feature matrix, the feature matrix was standardized using
the StandardScaler from sklearn.

Normalization is important in PCA since its is a variance maximizing
exercise and projects the original data onto directions that maximize the
variance. In the absence of standardization, it might appear that only the first
principal component explains most of the variance in the data. We need
to normalize the data to understand the contribution of each principal
component towards maximizing the variance.

The features present in the initial feature matrix are –
~~~
['coeff1', 'coeff2', 'coeff3', 'coeff4', 'coeff5', 'coeff6', 'coeff7',
'autocorr_lag_2', 'autocorr_lag_3', 'autocorr_lag_4', 'autocorr_lag_5',
'autocorr_lag_6', 'autocorr_lag_7', 'autocorr_lag_8', 'autocorr_lag_9',
'Vel_1', 'Vel_2', 'Vel_3', 'Vel_4', 'Peak_2', 'Peak_3', 'Peak_4','Peak_5']
~~~

Principal component analysis (PCA) is a technique for reducing the
dimensionality of such data sets, increasing interpretability but at the
same time minimizing information loss. It does so by creating new
uncorrelated variables that successively maximize variance.

For calculating the Principal Components, the PCA method from sklearn
was used.

## VI. Results

The final 5 features capture about 90 .6% of the original data.
From the analysis, the features which contribute the most are –
autocorrelation values when the lag = 5, the fourth coefficient of
polynomial fit, the second maximum value of CGM Velocity, Fifth
maximum peak value of FFT and the sixth coefficient of polynomial fit.
These features have the high variance and hence contribute the most
towards principal component construction.

## VII. Explanation of the Results

i. Autocorrelation values when lag=

This gives the autocorrelation between signals that have a time lag of 5 -
time periods and gives the similarity between the lagged and the leading
signals. Based on the data we observe a high amount of variance in this
feature and due to that it was picked as the main component of the first
principal component in PCA.

ii. Coeff4 – Fourth Coefficient of Polynomial Fit
Equation

The fourth coefficient of the 6th degree polynomial fit is also a
important feature. It was expected that the coefficients of the
higher terms would contribute significantly towards the principal
component construction. And also, it has a good amount of
variance.

iii. Vel- 2 – Second Maximum CGM Velocity
As explained previously, spikes in the CGM plot might mean
intake of a meal, hence our intuition is somewhat. The variance
in the 2 nd CGM velocity is considerable and hence it contributes
mainly towards the 3rd Principal Component.

i. Peak- 5 – Fifth Peak value from FFT

Generally, the increase in the glucose level is captured by the FFT peaks.
This was a expected feature that would contribute towards the Principal
Component, however why the fifth maximum peak is important instead of
2/3/4 peaks is unclear.

i. Coeff 6 – Sixth Coefficient of Polynomial Fit
Equation

The sixth coefficient of the 6th degree polynomial fit is also a
important feature. It was expected that the coefficients of the
higher terms would contribute significantly towards the principal
component construction. And also, it has a good amount of
variance.

