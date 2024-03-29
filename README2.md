# IOTISTIC-TAXI-METER
## Context

### Objective
build embedded firmware for taxi-meter devices that meet OIML standards R-21.
<p align="center">
  <img src="taxi.png" alt="Your SVG Image" />
</p>

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

### Interfaces
**Embedded firmware will be able to send/receive data & configuration commands through UART-USB serial protocol via COM PORT to peripherals at this capacity:**:
  -  close to 50 Hz data rate, 50 packets/seconds at a baud rate of 0.460800 MB/second.
> [!NOTE]
  > Based on the previous parameters, the peripherals must be able to do all fare calculations that meet OIML standards with no problems, but in our case, we decided to build all functions related to    fare calculations inside embedded firmware to reduce the possibility of error that related user application limitations.

### Data model
+ **Taxi meter embedded firmware data**:
  - Timestamp includes date & time.
  - Total distance traveled in meters from ignition on.
  - Uptime (Total time measured from ignition on).
  - Trip time in seconds.
  - Trip distance in meters.
  - Speed in KM/H.
  - cross-over speed (time tariff per hour/distance tariff per km).
  - single time (sum of time segments in seconds when fare calculation method is time-based in single mode when speed is lower than cross-over speed).
  - single distance (sum of distance segments in meters when fare calculation method is distance-based in single mode when speed is above cross-over speed).
  - fare counting rules (time-based (single), distance-based (single), (time & distance)-based).
  - active calculation modes (day or night).
  - start fare calculation flag (indicate that fare calculation start & distance reach initial distance or time reach initial time).
  - embedded firmware version.
  - hardware version.
  - product code
  
+ **Taxi meter embedded firmware configuration parameters (Taxi meter settings):**:
  - taxi k-constant pulse/km.
  - day initial fee in 0.01 Cu.
  - night initial fee in 0.01 Cu.
  - day initial distance in km.
  - night initial distance in km.
  - day initial time in seconds.
  - night initial time in seconds.
  - day distance tariff per km.
  - night distance tariff per km.
  - day time tariff per hour.
  - night time tariff per hour.
  - fare counting mode(single/double).
  - night start time
  - night end time


> [!WARNING]
> If you need to add initial distance or initial time you need to add both, because based on the requirements confirmed by team members the calculation must start when
> trip time reaches initial time or when trip distance reaches initial distance which is faster to reach, we will discuss with OIML about this if there are any notes from their side & make necessary changes.

> [!NOTE]
**Initial hire fee (or initial charge)**: First increment of fare indication upon activation of the taximeter.
>
> **Fare increment step**:Smallest amount of money by which the fare may be incremented in equal steps in the "Hired" (Occupied) operating position by the national regulations. (in our case we choose a constant fare step equal to 0.01 Cu.
>
> **Time tariff value**: Tariff value expressed as an amount of money for a given period of time. (in our case amount of money charged per hour).
>
> **Distance tariff value**: Tariff value expressed as an amount of money for a given distance. (in our case amount of money charged per Km).
> 
<h1><strong>Microcontroller RTOS Diagram</strong></h1>

<p align="center">
<img src="rtos_diagram.drawio.svg" alt="Your SVG Image" />
</p>

+ **calculate fare task**:
this main task will do other sub-tasks:
  + Count total trip distance signal pulses & calculate the distance in km depending on the taxi k-constant in all running modes (single/double/day/night).
  + Count total trip time signal pulses & calculate the time in seconds based on RTC clock output frequency (in our case 1024 pulse/second) in all running modes (single/double/day/night).
  + Count trip distance signal pulses & calculate the distance in km depending on the taxi k-constant while the taxi speed is more than the cross-over speed. (let's name it a single distance).
  + Count trip time signal pulses & calculate the time in seconds depending on RTC clock-out frequency while taxi speed is less than or equal to cross-over speed. (let's name it a single time).
  + Calculate fare depending on taxi meter running mode (single/double/day/night) & related parameters of tariffs & cross-over speed.
> [!IMPORTANT]
> All previous calculation methods for fare applied if there is no time block signal.

> [!NOTE]
**Time block signal**: Signal to block time counting (if this signal is activated the fare calculation method will be just distance-based, OIML uses this signal to test distance counting accuracy).  
>
+ **fare signal output task**:
this task will generate an output fare pulse signal from the test connector(this signal is used to test fare counting accuracy by OIML).

<p align="center">
<img src="test_connector.drawio.svg" alt="Your SVG Image" />
</p>

+ **calculate speed task**:
+ This task calculates the speed depending on the rate of distance pulses during one second. (calculate fare task using the speed global variable to change calculation method in single mode between time-based & distance-based).
+ Speed measurement rating update every one second.

+ **RTC date/time task**:
+ reads timestamp from accurate internal RTC DS3231N chip & stores all parameters related to RTC in the main USB array
+ Configuring & resetting the RTC module depending on configuration commands from USB peripherals.
+ Determine day or night depending on the configured night start time & configured night end time.

+ **USB send/receive task**:
+ This task handles configuration & settings commands through USB serial protocol by peripherals.
+ Respond with a 109-byte packet that describes all kinds of parameters related to taxi meters.

Baud rate: 460800bps/Serial COM (109) received bytes array must requested by this command with these conditions:

data length must = 16;
data[0]=0X2A;// or '*' 
data[1]='7';// or 0X37 
data[15]=0x0A;//Line feed
other bytes from a 16-byte array don't care.

**received data received (109) bytes array (you can test on serial terminal or python_script_testing_tool):**

Arr[0-3]:Total distance (uint32) start counting after ignition on:
must divide by 1 Constant to get the distance in centimeters.
must divide by 100 Constant to get the distance in meters.
must divide by 100000 Constant to get the distance in kilometers.
 
Arr[4-7]:Up Time (uint32) start counting after ignition on:
must divide by 1 to get the time closest to (0.01 seconds).
must divide by 100 to get the time closest to (1 second).

Arr[8-11]:Car Speed (uint32):
must divide by 100 to get a speed closest to 0.01 KM/H.
 
Arr[12-14]: Time in HH MM SS (uint8). HH (24).
 
Arr[15-17]: Date in YY MM DD (uint8).
 
Arr[20-23]: Fare (uint32) in 0.01Cu
 
Arr[24-27]: Secret (uint32)
 
Arr[28-31]: Trip distance (uint32) starts counting after sending the start command
must be divided by 1 Constant to get the distance in centimeters.
must be divided by 100 Constant to get the distance in meters.
must be divided by 100000 Constant to get the distance in kilometers.
 
Arr[32-35]: Trip time (uint32) starts counting after sending the start command
must  be divided by 1 to get the time closest to (0.01 seconds).
must  be divided by 100 to get the time closest to (1 second).
 
Arr[36-37]: Stored K_Constant (uint16) default value 2000.
 
Arr[38]: Mode uint8 (Single or Double):
if(data==0):Single
if(data==1):Double
 
Arr[39-40]: Day tariff per hour (uint16) in 0.01 Cu
 
Arr[41-42]: Day tariff per Km (uint16) in 0.01 Cu
 
Arr[43-46]: Calculated cross-over speed based on stored tariff per hour & tariff per km (uint32):
must to divided by 100 to get a cross-over speed closest to 0.01 KM/H.
 
Arr[47]: fare calculation method uint8 (time based, distance based, time & distance based):
if(data==0):Time based
if(data==1):Distance based
if(data==2):Distance & Time based
 
Arr[48-49]: day Initial hire fee (uint16) in 0.01 Cu
 
Arr[50-53]: day initial time (uint32) in milliseconds (0.001 seconds).
 
Arr[54-57]: day initial distance (uint32) in meters (1 meters).

Arr[58]: start fare calculation flag (uint8)
if(data==0)car moving within initial (time or distance).
if(data==1)car moving after initial (time or distance).
 
Arr[59-62]: number of time pulses (rising & falling) when the system calculates fare in single mode time based (while car moving below cross over speed), single time. (uint32).
 
Arr[63-66]: number of distance pulses (rising & falling) when the system calculates fare in single mode distance based (while car moving in cross over speed or more) single distance. (uint32).
 
Arr[67-68]: Firmware Version (uint16) must divide by 1000.
 
Arr[69-70]: Hardware Version (uint16) must divide by 1000.
 
Arr[71-81]=Product Code ASCI.
 
Arr[82-83]: Night tariff/H  (uint16) in 0.01 Cu
 
Arr[84-85]: Night tariff/K  (uint16) in 0.01 Cu
 
Arr[86-87]: Night Initial hire fee (uint16) in 0.01 Cu
 
Arr[88]: Day/Night config uint8:
if(data==0)day
if(data==1)night

Arr[89-92]: night initial time (uint32) in milliseconds (0.001 seconds).
 
Arr[93-96]: night initial distance (uint32) in meters (1 meters).
 
Arr[97-99]: night start time [byte hour, byte minutes, byte seconds]
 
Arr[100-102]: night end time [byte hour, byte minutes, byte seconds]
 
Arr[103-108]=['A', 'B',' C',' D', CR, LF]

**command  (16) bytes array (you can test on serial terminal or python_script_testing_tool):**

#### request data:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='7';// or 0X37 

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.


#### stop trip:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='0';// or 0X30

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!NOTE]
> All trip parameters will be stored until the next trip start.


#### start trip:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='1';// or 0X31

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!NOTE]
> All trip parameters will cleared directly before the trip starts.(trip distance, trip time, etc..)

#### auto stop after reaching fare 0.01 Cu or 0.1 Cu configuration:**

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='2';// or 0X32

data[2]='0';// or 0X30 (if you need normal stop trip behaviour)

data[2]='1';// or 0X31 (if you need trip auto-stopped after reaching 0.01Cu)

data[2]='2';// or 0X32 (if you need trip auto-stopped after reaching 0.1Cu)

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.
> [!NOTE]
> These modes of operation are just for testing.
> The OIML team will connect measurement devices to the test connector to count time pulses & distance pulses when the fare reaches 0.1 Cu to calculate the time-measuring accuracy & calculate the distance-measuring accuracy so we add this mode to test the firmware & eliminate USB communication packet time.
>

#### set RTC time-date as string characters:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='3';// or 0X33

data[2]=houres value (tens);//character byte

data[3]=houres value (ones);//character byte

data[4]=minutes value (tens);//character byte

data[5]=minutes value (ones);//character byte

data[6]=seconds value (tens);//character byte

data[7]=seconds value (ones);//character byte

data[8]=years value (tens);//character byte

data[9]=years value (ones);//character byte

data[10]=months value (tens);//character byte

data[11]=months value (ones);//character byte

data[12]=days value (tens);//character byte

data[13]=days value (ones);//character byte

data[14]=[X];//Do not care

data[15]=0x0A;//Line feed


#### Turn on/off general purpose 12v outputs & GPIOs:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='5';// or 0X35

data[2]='0';//or 0x30 (out1_12v_output_off)

data[2]='1';//or 0x31 (out1_12v_output_on)

data[3]='0';//or 0x30 (out2_12v_output_off)

data[3]='1';//or 0x31 (out2_12v_output_on)

data[4]='0';//or 0x30 (debugging_led1_test_point_low)

data[4]='1';//or 0x31 (debugging_led1_test_point_high)

data[5]='0';//or 0x30 (debugging_led2_test_point_low)

data[5]='1';//or 0x31 (debugging_led2_test_point_high)

data[6]='0';//or 0x30 (debugging_led3_test_point_low)

data[6]='1';//or 0x31 (debugging_led3_test_point_high)

data[7]='0';//or 0x30 (debugging_led4_test_point_low)

data[7]='1';//or 0x31 (debugging_led4_test_point_high)

data[14]=[X];//Do not care

data[15]=0x0A;//Line feed

> [!NOTE]
> We have on the taxi meter two 12v general purpose outputs we can use to turn on/off taxi lights in addition to 4 GPIOs as test points on PCB.
> The default value for all general-purpose output signals is low.

#### handshaking secret command (unsigned long as a string (10 digits)):
ex.number="1234567890"
data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='6';// or 0X36

data[2]='0';//'1'

data[3]='0';//'2'

data[4]='0';//'3'

data[5]='0';//'4'

data[6]='0';//'5'

data[7]='0';//'6'

data[8]='0';//'7'

data[9]='0';//'8'

data[10]='0';//'9'

data[11]='0';//'0'

data[12]=[X];//Do not care

data[13]=[X];//Do not care

data[14]=[X];//Do not care

data[15]=0x0A;//Line feed

secret_value = (number/2+177)%254;


#### handshaking hello string command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='9';// or 0X39

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

hello_string="Hello From Taximeter";


#### configure k-constant command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='A';// or 0X41

data[2-3]:k_constant (uint16) [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> k-constant must be within the range of 2000 to 6000 other values will be ignored


#### Configure day tariff per hour command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='B';// or 0X42

data[2-3]: day tariff per hour (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> day tariff per hour must be within the range of 0 Cu to 100 Cu in 0.01 Cu [MSB, LSB] other values will be ignored
> For example if the day tariff per hour is 5$ you must send 500.

#### Configure day tariff per km command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='C';// or 0X43

data[2-3]: day tariff per km (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> day tariff per km must be within the range of 0.01 Cu to 100 Cu in 0.01 Cu [MSB, LSB] other values will be ignored
> If you send a zero value the updated value will be 0.01 Cu.
> For example if the day tariff per km is 5$ you must send 500.

#### Configure day initial hire fee command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='D';// or 0X44

data[2-3]: day initial hire fee (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> day initial hire fee must be within the range of 0.00 Cu to 2.00 Cu in 0.01 Cu [MSB, LSB] other values will be ignored



#### Configure mode of operation command (single/double):

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='E';// or 0X45

data[2-3]: mode of operation (uint16) [MSB,LSB]  

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!NOTE]
> if(data==0):configure single mode
> if(data==1):configure double mode

#### Configure day initial time command:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='F';// or 0X46

data[2-5]: day initial time (uint32) in 0.001 seconds [MSB,.,.LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> Initial time must be within the range of 0.00 seconds to 10800.0 seconds (3 hours) [MSB, LSB] other values will be ignored


#### Configure day initial distance command:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='G';// or 0X47

data[2-5]: day initial distance (uint32) in 0.001 meter [MSB,.,.LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> Initial distance must be within the range of 0.00 meters to 3000 meters (3 km) [MSB, LSB] other values will be ignored


#### Configure night tariff per hour command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='H';// or 0X48

data[2-3]: night tariff per hour (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> night tariff per hour must be within the range of 0 Cu to 100 Cu in 0.01 Cu [MSB, LSB] other values will be ignored
> For example if the night tariff per hour is 5$ you must send 500.

#### Configure night tariff per km command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='I';// or 0X49

data[2-3]: night tariff per km (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> night tariff per km must be within the range of 0.01 Cu to 100 Cu in 0.01 Cu [MSB, LSB] other values will be ignored
> If you send a zero value the updated value will be 0.01 Cu.
> For example if the day tariff per km is 5$ you must send 500.

#### Configure night initial hire fee command :

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='J';// or 0X4A

data[2-3]: night initial hire fee (uint16) in 0.01 Cu [MSB,LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> night initial hire fee must be within the range of 0.00 Cu to 2.00 Cu in 0.01 Cu [MSB, LSB] other values will be ignored


#### Configure night initial time command:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='L';// or 0X4C

data[2-5]: night initial time (uint32) in 0.001 seconds [MSB,.,.LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> Initial time must be within the range of 0.00 seconds to 10800.0 seconds (3 hours) [MSB, LSB] other values will be ignored


#### Configure night initial distance command:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='M';// or 0X4D

data[2-5]: night initial distance (uint32) in 0.001 meter [MSB,.,.LSB]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> Initial distance must be within the range of 0.00 meters to 3000 meters (3 km) [MSB, LSB] other values will be ignored


#### Configure night (start/end) time:

data length must = 16;

data[0]=0X2A;// or '*' 

data[1]='N';// or 0X4E

data[2-4]: night start time (uint8) per byte [HH:MM:SS]
data[5-7]: night end time (uint8) per byte [HH:MM:SS]

data[15]=0x0A;//Line feed

other bytes from a 16-byte array don't care.

> [!IMPORTANT]
> Start night time & end night time bytes must be within normal ranges of time stamp (HH<=23 mm<=59 SS<=59) other values will ignored.


