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
+ Embedded firmware will measure these parameters:
  - Time pulses from external test connector or internal RTC module to measure trip time with high accuracy & maximum permissible error that meet OIML R-21 standards depending on time pulses factor.
  - Distance pulses from external test connector or external VSS sensor to measure trip distance with high accuracy & maximum permissible error that meet OIML R-21 standards depending on car K-Constant.
+ Embedded firmware will calculate the fare depending on:
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
> **Cross-over speed**: Speed of the taxi (km/h) at which the time-counting and distance-counting methods operate the taximeter at the same rate. The speed value is determined by division of the time tariff value by the applicable distance tariff value.
> 
> **Single application of tariff**: Fare calculation based on the application of the time tariff below the cross-over speed and application of the distance tariff above the cross-over speed.
> 
> **Double application of tariff**: Fare calculation based on the combined application of time tariff and distance tariff over the whole journey.
>
> 


