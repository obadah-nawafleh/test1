# IOTISTIC-TAXI-METER
## Context

### Objective
build embedded firmware for taxi-meter devices that meet OIML standards R-21.

### Background
After the widespread of electronic systems for calculating fares in TAXI public transportation, there has become an urgent need to establish accurate standards for measuring the costs of trips and fares from passengers. Hence the a need for OIML standards through which transportation fees are determined accurately based on several data and methods that ensure this.

## Design
### Overview
embedded firmware for the taxi-meter device will measure & calculate a lot of parameters related to taxi trips like fare, distance & time in a very accurate manner to meet OIML R-21 standards & requirements.

### Functional Requirements
+ **Embedded firmware will measure these parameters**:
  - Trip time in seconds based on time measuring signal with high accuracy & maximum permissible error (MPE) that meets OIML R-21 standards.
  - Trip distance in meters based on distance measuring signal with high accuracy & maximum permissible error (MPE) that meets OIML R-21 standards depending on car K-Constant.
  > [!NOTE]
  > MPE for trip time is (0.2s or 0.002 from the trip time) which is greater.
  > MPE for trip time is (4 meters or 0.001 from the trip distance) which is greater.
+ **Embedded firmware will calculate the fare depending on**:
  - Measured trip time in seconds.
  - Measured trip distance in meters.
  - Calculation methods are single or double.
  - Car speed in KM/H
  - Day/Night initial trip distance
  - Day/Night initial trip time
  - Day/Night Time/Distance tariff & cross-over speed.
  - Day/Night initial fee.
  - Fare steps based on minimum currency unit in our case 0.01 Cu.
  > [!NOTE]
  > **Single application of tariff**:Fare calculation based on the application of the time tariff below the cross-over speed and application of the distance tariff above the cross-over speed.
  > 
  > **Double application of tariff**: Fare calculation based on the combined application of time tariff and distance tariff over the whole journey.
  >
  > **Taximeter constant k**: Constant expressed in pulses per kilometer which represents the number of pulses the taximeter must receive to correctly indicate a distance traveled of one kilometer.
  >
  > **Initial distance**: Distance which can be traveled according to the tariff for the initial hire fee, considering distance-counting only.
  >
  > **Initial time**: The period during which the taxi can be used for the initial hire fee, considering time-counting only.
  
+ **Embedded firmware will be able to send/receive data & configuration commands through UART-USB serial protocol via COM PORT to peripherals at this capacity:**:
  -  close to 50 Hz data rate, 50 packets/seconds at a baud rate of 0.460800 MB/second.
  > [!NOTE]
  > Based on the previous parameters, the peripherals must be able to do all fare calculations that meet OIML standards with no problems, but in our case, we decided to build all functions related to    > fare calculations inside embedded firmware to reduce the possibility of error that related to motherboard os limitations.

+ **Embedded firmware will be able to generate fare pulses synchronized with fare result**:
  - fare pulses will generated from the test connector of the taxi meter based on the fare calculation function at a maximum speed of 200KM/H with maximum tariff parameters and calculation methods.
  > [!NOTE]
  > At maximum speed 200KM/H, Maximum k-constant 6000 pulse/km in our case, output fare signal frequency up to 333.333Hz so embedded firmware will be able to generate the pulses with frequency       333.33Hz or more.

+ **Embedded firmware will calculate speed to change calculation methods from time-based to distance-based depending on cross-over speed**:
  - The speed calculation task will update the speed every one second with an accuracy of more than 99%.
  - **Cross-over speed**: Speed of the taxi (km/h) at which the time-counting and distance-counting methods operate the taximeter at the same rate. The speed value is determined by division of the time tariff value by the applicable distance tariff value.
> [!NOTE]
  > There is no MPE related to speed measuring accuracy because fare at cross-over speed will increase at the same rate if the functions calculate the fare based on distance or based on time & we take confirmation from OIML for this note.

> [!NOTE]
>
> **Distance measuring signal**: Signal supplied by the distance measurement transducer to the taximeter, in proportion to the distance traveled in our case it is VSS (Vehicle speed sensor signal) or from test connector distance-input signal.
>
> **Time measuring signal**:Signal supplied by a clock incorporated in the taximeter, in proportion to the duration of the journey. from (external test connector time-input signal or internal RTC module pulse signal).
>
> **Maximum permissible error, MPE**: Extreme value of an error permitted by specifications, regulations, etc., for a given instrument.in our case, The maximum permissible error for the trip time  is 0.2s or (0.002 x trip time in seconds) which is greater & the maximum permissible error for the trip distance is 4m or (0.001 x trip distance in meters) which is greater.
>
> **Internal RTC Real-time clock**: The real-time clock shall keep track of the time of the day and the date. One or both values may be used for the automatic change of tariff in our case we use the DS3231D module in the range of -40°C to +85°C, and the accuracy remains at ±3.5ppm (±0.3024 seconds/day), we use INT/SQW pin from RTC as a time measuring signal on 1.024Khz base.




