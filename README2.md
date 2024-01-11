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
embedded firmware will measure these parameters:
+ time pulses from external test connector or internal RTC module to measure trip time with high accuracy & error less than 0.2 seconds or less than 0.001 from trip time which one is bigger.
+ distance pulses from external test connector or external VSS sensor to measure trip distance with high accuracy & error less than 4 meters or less than 0.002 from trip distance which one is bigger.


