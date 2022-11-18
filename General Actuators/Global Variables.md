```
{attribute 'qualified_only'}
VAR_GLOBAL
(* Elevator managing variables*)
	Open: BOOL;				(* Sensor: the door is fully open*)
	Closed: BOOL;			(* Sensor: the door is fully closed*)
	LimitDown: BOOL;		(* Sensor: lower limit sensor, if TRUE the lift has reached the lower limit*)
	LimitUp: BOOL;			(* Sensor: upper limit sensor, if TRUE the lift has reached the upper limit*)
	DeckSensor: BOOL;		(* Sensor: if TRUE the cab is in proximity of a deck*)
	RampSensor: BOOL;		(* Sensor: if TRUE the cab is in proximity of a ramp*)
	Presence :BOOL; 		(* Sensor: if TRUE something blocks the door photocell*)
	Emergency: BOOL;		(* Input: if TRUE the emergency button has been pressed*)

	Closing: BOOL;			(* Actuator: if TRUE the door starts closing*)
	Opening: BOOL;			(* Actuator: the door starts opening*)
	Motor_on: BOOL;			(* Actuator: if TRUE the lift pulley motor start moving*)
	Up: BOOL;				(* Actuator parameter: if TRUE the lift moves upward, downward otherwise*)
	Vel: BOOL;				(* Actuator parameter: motor speed, if TRUE high speed, low speed otherwise*)
	

	Current: INT;  			(* Visualization: variable used to storage the current deck*)
    Destination: INT;		(* Visualization: variable used to storage the the destination deck*)
	EmergencyVis: BOOL; 	(* Visualization: if TRUE the emergency light is turned on*)


	
	
	InitialDeck: INT := 1;	(* Only for simulation: variable used to define the initial deck*)
	InitCycle: BOOL := FALSE;(* Only for simulation: used to execute the instruction at the first cycle*)
	Height: INT;			(* Only for simulation: elevator height*)
	DeckRest: INT;        	(* Only for simulation: division rest for generating deck sensor*)
	Memory: INT;
	RampRest: INT;			(* Only for simulation: division rest for generating the ramp position*)
	MemoryR: INT;
	Door: INT;				(* Only for simulation: to visualize the closing of the door*)
	Displacement: INT;		(* Only for simulation: lift displacement, dependent on the motor speed*)

 (*Inputs: internal panel variables*)
	PG: BOOL;
	P1: BOOL;
	P2: BOOL;
	P3: BOOL;
	P4: BOOL;
	P5: BOOL;
	P6: BOOL;
	P7: BOOL;
	
	(*Inputs: external panel variables*)
	UpDeckG: BOOL;
	UpDeck1: BOOL;
	UpDeck2: BOOL;
	UpDeck3: BOOL;
	UpDeck4: BOOL;
	UpDeck5: BOOL;
	UpDeck6: BOOL;
	DownDeck7: BOOL;
	DownDeck6: BOOL;
	DownDeck5: BOOL;
	DownDeck4: BOOL;
	DownDeck3: BOOL;
	DownDeck2: BOOL;
	DownDeck1: BOOL;

(*Visualization: internal panel variables used to turn on the LED*)
	PGVis: BOOL;
	P1Vis: BOOL;
	P2Vis: BOOL;
	P3Vis: BOOL;
	P4Vis: BOOL;
	P5Vis: BOOL;
	P6Vis: BOOL;
	P7Vis: BOOL;

(*Visualization: external panel variables used to turn on the LED*)
	UpDeckGVis: BOOL;
	UpDeck1Vis: BOOL;
	UpDeck2Vis: BOOL;
	UpDeck3Vis: BOOL;
	UpDeck4Vis: BOOL;
	UpDeck5Vis: BOOL;
	UpDeck6Vis: BOOL;
	DownDeck7Vis: BOOL;
	DownDeck6Vis: BOOL;
	DownDeck5Vis: BOOL;
	DownDeck4Vis: BOOL;
	DownDeck3Vis: BOOL;
	DownDeck2Vis: BOOL;
	DownDeck1Vis: BOOL;

(*Only for simulation: variables used to simulate the fault on deck sensor*)
	SensorG: BOOL;				(* if TRUE the magnetic information for deck G is lost and the deck sensor cannot become TRUE*)
	Sensor1: BOOL;				(* if TRUE the magnetic information for deck 1 is lost and the deck sensor cannot become TRUE*)
	Sensor2: BOOL;
	Sensor3: BOOL;
	Sensor4: BOOL;
	Sensor5: BOOL;
	Sensor6: BOOL;
	Sensor7: BOOL;
	FaultSensorAlarm: BOOL; 	(* Visulaization: if TRUE alerts the user of a deck sensor fault*)

(*Only for simulation: variables used to simulate the fault on ramp sensor*)
	RampGUp: BOOL;
	Ramp1Down: BOOL;
	Ramp1Up:BOOL;
	Ramp2Down: BOOL;
	Ramp2Up:BOOL;
	Ramp3Down: BOOL;
	Ramp3Up:BOOL;
	Ramp4Down: BOOL;
	Ramp4Up:BOOL;
	Ramp5Down: BOOL;
	Ramp5Up:BOOL;
	Ramp6Down: BOOL;
	Ramp6Up:BOOL;
	Ramp7Down: BOOL;
	FaultRampAlarm: BOOL; 		(* Visulaization: if TRUE alerts the user of a ramp sensor fault*)
	SensorGOK: BOOL;
	Sensor1OK: BOOL;
	Sensor2OK: BOOL;
	Sensor6OK: BOOL;
	Sensor3OK: BOOL;
	Sensor4OK: BOOL;
	Sensor5OK: BOOL;
	Sensor7OK: BOOL;
	RampGUpOK: BOOL;
	Ramp1DownOK: BOOL;
	Ramp1UpOK: BOOL;
	Ramp2DownOK: BOOL;
	Ramp2UpOK: BOOL;
	Ramp3DownOK: BOOL;
	Ramp3UpOK: BOOL;
	Ramp4DownOK: BOOL;
	Ramp4UpOK: BOOL;
	Ramp5DownOK: BOOL;
	Ramp5UpOK: BOOL;
	Ramp6DownOK: BOOL;
	Ramp6UpOK: BOOL;
	Ramp7DownOK: BOOL;
	Ramp7UpOK: BOOL;
	
	//var aggiunte da me
	array_dest_up_onfly: ARRAY[0..7] OF INT;
	array_dest_up_stored: ARRAY[0..7] OF INT;
	
	array_dest_down_onfly: ARRAY[0..7] OF INT;
	array_dest_down_stored: ARRAY[0..7] OF INT;
	
	served_up: BOOL; // flag di fine servizio per movimento up
	served_down: BOOL; // flag di fine servizio per movimento down
	dest_target_up: INT; //destinazioen target up
	dest_target_down: INT; //destinazione target down
	enable_target_up_down: BOOL;	
	G_floor_problem: BOOL;
	
	//Istanze dei GA
	door_module_0: Door_module;
	Cardinal_0: Cardinal;
	Moves_module_0: Moves_module;

END_VAR
```