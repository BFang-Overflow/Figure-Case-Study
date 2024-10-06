# Figure IMU Subsystem Automation Test Design Doc

<div style="display: flex;">
  <div style="flex: 50%; padding: 10px;">
    <h3>Overview</h3>
    <p> This case study focuses on automating the testing of an Inertial Measurement Unit (IMU)
    subsystem. The IMU system sends accelerations and angular rates to an application for attitude
    estimation. Key tasks include designing a test setup, preparing documentation for technicians,
    and implementing a Python test case.
    </p>
    <br>

    *** This is a mock documentation, presented in the mannar
    I would normally have in a working doc to point internal
    teams to.

   </div>
  <div style="flex: 50%; padding: 10px;">
    <h3>Point of Contacts</h3>
    <b>Electircal Eng:</b> xxx<br>
    <b>Firmware Eng:</b> xxx<br>
    <b>QA Eng:</b> xxx<br>
    <b>System Integration Eng:</b> xxx<br>
    <b>Tech :</b> xxx<br>
    <b>Test Automation Eng:</b> Bill Fang <br>
    <h3>Fake Useful Links</h3>
    <span style="color: blue; text-decoration: underline;">Altium Project Link</span><br>
    <span style="color: blue; text-decoration: underline;">Repo Link</span><br>
    <span style="color: blue; text-decoration: underline;">System Requirement Link</span><br>
  </div>
</div>

## Table of Contents

## System Block Diagram

### IMU System
![IMU_System_Overview_Image](doc_images/IMU_System_Overview.png)
### Test Setup
overview
+ Test setup block diagram
+ Link build doc







## Assumptions

Assumptions are important, below listed the top level assumptions of the whole system. Specific
assumptions will show up as notes within their context for easy reference.


!!! tip Assumptions
    **Assumption-1** The IMU System hardware has been designed to meet the requirement with reasonable
    design margins and the hardware has been validated for basic operations.
    **Assumption-2** The IMU is a stand alone in house control unit with its own MCU and it implements
    standrad protocols on top of the RS-422 interface. The IMU firmware is also regression target.
    **Assumption-3** The IMU is a MEMS type IMU in this applicaiton without magnetometer. Otherwise
    further environment control measures are needed.
    **Assumption-4** The IMU has on board temperature sense and well defined calibration routines.
    **Assumption-5** DUT and test set up has been checked for worksmanship and build to print.
    **Assumption-6** Testing covers a Industrial operating temperatures range of -40&#176;C to +85&#176;C.
    **Assumption-7** Test system design proritize preformance and accuracy with reasonable cost.
    **Assumption-8** System Computer running linux-RT the IMU app is allocated adquate resources and priority.
    **Assumption-9** This case study focus on automation test for firmware regressions. Automations tests
    can also be done for hardware validatoin, reliability, and manufacturing with different focus. Those
    test usage and set up are not considered.




## Design Considerations

## System Analysis

What do I know about the system and its limitations.

### IMU properties
#### Types of IMUs and limitations
#### IMU design considerations
To achieve high IMU Accuracy  the following factors need to be considered during design and validtions.
**Temperatures**
**Calibrations**
### RS-422 Spec
| Start Bit | Data Bits (8 bits) | Parity Bit | Stop Bit(s) |
|:---------:|:-------------------:|:-----------:|:------------:|
|     0     |      D0 D1 D2 D3 D4 D5 D6 D7    |      P      |      1       |


## Test Covorage

![IMU_System_Requirements_Image](doc_images/IMU_System_Requirements.png)

!!! note
    **Test Things Where Most Effective:** There are various ways we could test this subsystem. It
    could be a full simulated enviroment with each component been moduled in a Software in the loop(SIL)
    test, or we could instrument and modify the IMU to inject ditial signals that replaces sensor IC
    signals to avoid use of heavy equipment and improve fault injection ability. Or a system similar
    to the proposed setup could be achieved at lower cost with less capabilities. And in real world,
    we are likely to have each favor of the test infra and setup. Ideally we have all coverage on all
    setup. In practice, we choose the most effective combination of set up that can provide sufficient
    coverage given the time and resources in hand.


!!! tip Assumptions
    **Assumption-** Related test infrastures has been set up for result upload and store. This will
    happen at test tear down which is omitted in the procedures below.

### REQ-001 Attitude Accuracy
#### TEST-001 IMU full range accuracy sweep
This test checks for IMU's accuracy with small changes and impluse changes.

<u>Test Procedure:</u>
1. Zero IMU location, RS-422 link to direct connection.
2. Parameterized test sweeping each axis with 1 degree steps.
3. Assert for each step the reported meansure is within +/- 0.1 degree of the set position.
4. Parameterized test sweeping each axis with 180 degree steps.
5. Assert for each step the reported meansure is within +/- 0.1 degree of the set position.

!!! tip Assumptions
    **Assumption-** The rate table is calibrated, it's set position is true position.

#### TEST-002 IMU temperature compensation check
This test check for IMU's temperature compenstaion algorigthm implementation.

<u>Test Procedure:</u>
1. Parameterized test sweeping temperature from  -40&#176;C to +85&#176;C with 5&#176;C steps.
2. Run TEST-001

!!! tip Assumptions
    **Assumption-** Tester has enough bandwidth for running test. In real life, re

#### TEST-003 IMU drift test
!!! tip Assumptions
    **Assumption-** Assume IMU has data processing and correction algorithms implemented to prevent drifting.
This test check for IMU's coorection algorithms. This is a run on idel test that run opportunistically when
no other job is available.

<u>Test Procedure:</u>
1. Zero IMU location, set test chamber to room temp, RS-422 link to direct connection.
2. Assert inital value is within expected zero location range +/- 0.1 degree.
3. Record inital value and start a timer.
4. Assert on every data recieved is within drift rate bound until terminated by governor.
5. On teardown check upload drift rate measured in this test period in addition to results for data trend.

!!! tip Assumptions
    **Assumption-** Assume we have a test job govorner running. We can notify job to end if we sees
    other jobs come in.

#### TEST-004 IMU vibe check
~~This test check if IMU brings good vibe for the robot :sunglasses:~~
This test check for linear accelaration's affect on angular infomation. Check for motor control pwm
requency or load switching freqency from the near by subsystems that is within the sensor's bandwidth.

<u>Test Procedure:</u>
1. Parameterized test with critical frequencies.
2. Set vibration shaker to each frequency and measure assert reported attitude data within accuracy.

!!! caution Check
    Comfirm with hardward team, this likely can be a hardware characterization test instead of a
    regression item until further lessons learned.



### REQ-002 RS-422 Error Rate
#### TEST-005 RS-422 Error Rate Test
!!! tip Assumptions
    **Assumption-** The robot harness has been designed properly and tested with reprentative
    switching laod that would run parallel to the RS-422 link in the robot. This test can also
    be used for the characterization.
This test mainly targeting the IMU APP's ability to detect and report error rate correctly as a
regression test. The goal is to ensure the error rate stat is accurate.

<u>Test Procedure:</u>
1. Switch RS-422 link to fault injections mod connecting:
    `tester PC RS-422 out` <-> `RS-422 link` <-> `IMU APP computer`
2. Norminal Case send over 2e^5 messages, check total error reported <= 2
3. Fault Case send over 2e^5 messages, with 6 error message check total error reported <= (2+6)

### REQ-003 2ms Delay limit
#### TEST-006 IMU delay test
!!! tip Assumptions
    **Assumption-** The time it takes from host PC sending a command to the rate table response is
    a well defined time.
    **Assumption-** The IMU linux computer has network access and can do NTP (Network Time Protocol)

NTP is used to synchronize the clocks of all systems involved in the testing process to ensure accurate event timing.

<u>Test Procedure:</u>
1. Tester computer and IMU APP computer preform time stnchronization.
2. Trigger a physical event and record time stemp.
3. Repeat step2 for all axis directions.
4. Process time stemp assert all latency is less than 2ms.

!!! note
    **Alternatives considered:**
    + `GPS-based Time Synchronization`: For even higher precision, GPS receivers provide time synchronization down to nanoseconds.
    + `Trigger Output`: The signal generator should output a trigger signal simultaneously with the physical event. This signal can act as a reference point for when the event occurs.
    + `Oscilloscope or Logic Analyzer`: Use a scope or logic analyzer to capture signal coming out of IMU and a triger from linux computer when data is posted.



### Other Considerations
!!! note
    Other test / logging that could be considered:
    - CPU usage stats of the IMU app
    - Test equipment health check
    - Measure key value delta from average value from pervious mainline releases
    - Log run time for maintaince reminder


## Tester Build Docs

This is the build doc for the IMU subsystem automation test.
+ Block Diagram
+ Schematics
+ Check List
+ BOM
+ Harness Diagram
+ Please label cable type null modem or not
+ Equipment Manual

#### Getting Started

+ Getting Started link
+ ssh

## Test Case Implementation

```python




```

## Team, Processes, and Tools
### Team
**Electircal Eng:**
+ Talking to EE for board specs and power requirements. Any circuit related deep dive.
+ Request test points for future rev.
+ Share any bug found.
+ Share automations script that can also be leverage for design validation.

**Firmware Eng:**
+ Reling on fw team to help understand certain system behavoir, debug trace.
+ Request additional data logging or new service routine to be added.
+ Scope automation infra needs for developers to create tests as the develop new fetures.

**QA Eng:**
+ Understand the top offending failure trend.
+ Request new matrix and dashboard.
+ Align on reporting schema that can be used across the board.
+ Add automation test coverages to help faster release sign off.

**System Integration Eng:**
+ Learn about the subsystem bug that escaped to system level and add coverages.
+ Learn about time cosuming operations that can be helped with automation test coverages.
+ Align on coverage distribution, don't step over each other.

**Tech:**
+ Request build help.
+ Document feedback & red line.
+ Learn about commonly stocked/prefered common parts.
+ Learn about how to improve build documents.

**Test Automation Eng:**
+ New infrastruct to that is been added.
+ Learn about other subsystem automation setup and what can be reused.

### Processes and Tools
A **nice-to-have** leader board for people to put up their common conplaints and desired quality of
life improvement will be a good way to create traction on making feedback or improvoments.

New tool **show and tell** or **technical section deep dive** will be good ways to share lessons learned
and grow the general knowledge on systems.