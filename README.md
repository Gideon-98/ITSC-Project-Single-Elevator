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
a) the system must track and manage the calls made while the elevator is serving a request; 
b) the system must be able to handle "on the fly" any calls congruent with a motion in progress (e.g., if the elevator is moving from floor 2 to reach floor 7 and you have a call on floor 5, the logic control will have to detect it and serve it before reaching floor 7, if there are conditions).

The policy adopted for call service should be clearly described in the project documentation.
