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
+ + Time pulses from external test connector or internal RTC module to measure trip time with high accuracy & maximum permissible error that meet OIML R-21 standards depending on time pulses factor.
+ + Distance pulses from external test connector or external VSS sensor to measure trip distance with high accuracy & maximum permissible error that meet OIML R-21 standards depending on car K-Constant.
+ Embedded firmware will calculate the fare depending on:
+ + Measured trip time.
+ + Measured trip distance.
+ + Calculation methods single or double.
+ + Car speed in KM/H
+ + Day/Night tariff
+ + Day/Night initial trip distance
+ + Day/Night initial trip time
+ + Time/Distance taariff & cross over speed.
+ + Day/Night initial fee.
+ + fare steps based on minimum currency unit in our case 0.01 Cu.


