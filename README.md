# ITSC-Project-Single-Elevator
Control of a Single Elevator system (Ladder Programming)

#### Task:

Design, using a CoDeSys programming IDE (Ladder), the logic control of a single elevator system. 
The SingleElevator_ancestor.project is the intial file which contain the reproduction of the behavior of the uncontrolled system and a graphical interface to aid simulation.

Logic control must essentially ensure:
- The correct initialization of the elevator.
- The correct operation of the elevator with efficient handling of call requests from the floors and from inside the elevator, under nominal conditions.
- The management of some non-nominal conditions.

Regarding the above "efficient management of requests," no "optimality" is required (e.g., minimization of waiting times and/or routes taken by the car, etc.), but it is important that the chosen policy allows for some degree of overall efficiency. 

The minimum requirements to be met at the call service level are as follows: 
1. The system must track and manage the calls made while the elevator is serving a request; 
2. The system must be able to handle "on the fly" any calls congruent with a motion in progress (e.g., if the elevator is moving from floor 2 to reach floor 7 and you have a call on floor 5, the logic control will have to detect it and serve it before reaching floor 7, if there are conditions).
3. The policy adopted for call service should be clearly described in the project documentation.

__

# Description of I/O (Input/Output) and User Interface

Special attention should be paid to the significance of the sensors named DeckSensor (floor sensor) and RampSensor (ramp sensor).
When the control system is turned on, the elevator may be on any floor that cannot be considered directly "measurable," given the characteristics of DeckSensor and RampSensor. Therefore, when starting up the control system, an initialization (homing) phase is required to be carried out 
as follows:
1. Door closure (passenger presence check must be performed);
2.   Slow descent to the ground floor, the elevator reaches the lower end stop;
3.   Elevator ascends to the second floor sensor it encounters, again at slow speed;
4.   Doors open;
5.   Standby status.
It is assumed that the system knows a priori the number of floors to be handled.
After initialization is finished, we have the nominal operation of the system. The following is the sequence of 
operations to be performed to serve the target floor starting from the current one:
1.   Waiting 2sec, closing doors (with passenger presence management in the door area);
2.   Slow movement until the (end) ramp signal of the starting floor;
3.   Fast movement until reaching the sensor of (start) ramp preceding the plane of 
destination;
4.   Slow movement until the plane is reached and motion stops;
5.   Waiting for 2sec and opening doors;
6.   5sec wait.
The emergency button (variable Emergency), which is present inside the car, should be managed, and of the 
operation of the system in that condition. If Emergency is pressed, the system must be brought 
as soon as possible to a safe condition that allows passengers to exit and must suspend the 
service of other calls (which must still be stored) until the same 
button is not re-pressed.
Finally, the user interface LEDs must be managed (if a button is pressed, the LED 
corresponding must light up).

