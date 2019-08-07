# PeakDetection

PeakDetection is an Arduino library for real time peak detection in sensor data.

## Algorithm

It is based on the [principle of dispersion](https://en.wikipedia.org/wiki/Statistical_dispersion): if a new datapoint is a given `x` number of [standard deviations](https://en.wikipedia.org/wiki/Standard_deviation) away from some [moving average](https://en.wikipedia.org/wiki/Moving_average), the algorithm signals (also called [z-score](https://en.wikipedia.org/wiki/Standard_score)).

The algorithm takes 3 inputs:

* `lag`: is the lag of the moving window. This parameter determines how much your data will be smoothed and how adaptive the algorithm is to changes in the long-term average of the data. The more stationary your data is, the more lags you should include. If your data contains time-varying trends, you should consider how quickly you want the algorithm to adapt to these trends.

* `threshold`: this parameter is the number (between 0 and 1) of standard deviations from the moving mean above which the algorithm will classify a new datapoint as being a signal. This parameter should be set based on how many signals you expect. The threshold therefore directly influences how sensitive the algorithm is and thereby also how often the algorithm signals.

* `influence`: is the z-score at which the algorithm signals. This parameter determines the influence of signals on the algorithm's detection threshold. If put at 0, signals have no influence on the threshold, such that future signals are detected based on a threshold that is calculated with a mean and standard deviation that is not influenced by past signals. You should put the influence parameter somewhere between 0 and 1, depending on the extent to which signals can systematically influence the time-varying trend of the data.

## Functions

- begin()
- detect()]
- getPeak()
- getFilt()

### begin()

Initialize PeakDetection object and configuration for tweaking parameters. If no parameters have been set, the default remains.

```C++
peakdetection.begin(lag,threshold,influence);
```

Alternaly:

```C++
peakdetection.begin(); //lag=32, threshold=2, influence=0.5
```

### detect()

Adds a new data point to algorithm, calculates the standard deviations and moving average.

```
peakdetection.detect(datapoint);
```

### getPeak()

Returns peak status {-1, 0, 1}, representing below, within or above standard deviation threshold, respectively.

```
double peak = peakdetection.getPeak();
```

### getFilt()

Returns the last data point filtered by the moving average.

```
double filtered = peakdetection.getFilt();
```

## Installation

To use the library:

1. Download the zip and uncompress the downloaded file.
2. Copy the folder to the Arduino libraries folder (_C:/Users/username/Documents/Arduino/libraries_).
3. Rename it to PeakDetection.

## Example

### Code

```C++
#include <PeakDetection.h> // import lib

PeakDetection peakDetection; // create PeakDetection object

void setup() {
  Serial.begin(9600); // set the data rate for the Serial communication
  pinMode(A0, INPUT); // analog pin used to connect the sensor
  peakDetection.begin(48, 3, 0.6); // sets the lag, threshold and influence
}

void loop() {
    double data = (double)analogRead(A0)/512-1; // reads the value of the sensor and converts to a range between -1 and 1
    peakDetection.detect(data); // adds a new data point
    int peak = peakDetection.getPeak(); // returns 0, 1 or -1
    double filtered = peakDetection.getFilt(); // moving average
    Serial.print(data); // print data
    Serial.print(",");
    Serial.print(peak); // print peak status
    Serial.print(",");
    Serial.println(data); // print moving average
}
```

### Output

![Example output](https://github.com/leandcesar/PeakDetection/blob/master/examples/output.gif)

## Credits

* [StackOverFlow](https://stackoverflow.com/questions/22583391/peak-signal-detection-in-realtime-timeseries-data).
