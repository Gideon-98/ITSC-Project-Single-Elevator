## Variables

```
FUNCTION_BLOCK Moves_module
VAR_INPUT	
	Start_: BOOL;
	StartWhat: STRING;
	Stop: BOOL;
	
//INPUT PROVENIENTI DA CARDINAL
	target_up_FB: INT; //value to be given to Dest_target_up
	target_down_FB: INT; //value to be given to  Dest_target_down
	enable_target_up_down_FB: BOOL;
	
// SENSORI
	DeckSensor_FB: BOOL;
	RampSensor_FB: BOOL;
	LimitDown_FB: BOOL;
	LimitUp_FB: BOOL;
	
	Emergency_FB: BOOL;
END_VAR
VAR_OUTPUT
	State: STRING;
	Done: BOOL;	
//OUTPUT towards CARDINAL
	served_up_FB: BOOL; //flag of end service of target_up
	served_down_FB: BOOL; //flag of end service of target_down
//OUTPUT towards POLICY
	Current_FB: INT; //visualization variable current floor
	Up_FB: BOOL; //operate the direction of the elevator
	Motor_on_FB: BOOL; //elevator motor switch
	Vel_FB: BOOL; //operate the speed of the elevator
	deck_error_FB: BOOL; //deck error flag
	ramp_error_FB: BOOL; //ramp error flag
	EmergencyVis_FB: BOOL;
	
	G_floor_prob: BOOL;
END_VAR
VAR
	sync: BOOL;
	Opok: BOOL;
	ramp_counter: INT;
	deck_counter: INT;
END_VAR

```
## Output Initialization  
```
Init:
//OUTPUT towards CARDINAL
served_up_FB:=FALSE; //end of service flag valore target_up
served_down_FB:=FALSE; //end of service flag valore target_down

//OUTPUT towards POLICY
//Current_FB:=8; //visualization variable current floor
Up_FB:=TRUE; //intizialized TRUE cause after homing the elevator can go only up
Motor_on_FB:=FALSE;
Vel_FB:=FALSE; 
// deck_error_FB:=FALSE;
// deck_error_FB:=FALSE;

//LOCAL VARIABLES
sync:=FALSE;
Opok:=FALSE;
ramp_counter:=0;
deck_counter:=0;

Ready:
State:=’ready’
deck_error_FB:=FALSE; 
ramp_error_FB:=FALSE; 
Done:=FALSE;

Busy:
State:=’busy’
G_floor_prob:=FALSE;
EmergencyVis_FB:=FALSE;
IF NOT enable_target_up_down_FB OR Current_FB=7 THEN
	Up_FB:=FALSE;
END_IF

IF enable_target_up_down_FB OR Current_FB=0 THEN
	Up_FB:=TRUE;
END_IF

```

## Homing_0:
```
//OPENING DOOR
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='close_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;
END_IF

Opok:= sync and not door_module_0.Done;

Exit instruction:
sync:=FALSE;
Opok:=FALSE;
```
### Homing_1:
```
//HOMING
Motor_on_FB:= TRUE;
Up_FB:= FALSE;
Vel_FB:= FALSE;
```
### Homing_2:
```
//HOMING_1
Motor_on_FB:= TRUE;
Up_FB:= TRUE;
Vel_FB:= FALSE;
```
### Homing_3:
```
//STOP
Motor_on_FB:= FALSE;
Current_FB:=0;
//APERTURA PORTE 
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='open_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;
	Moves_module_0.Done:=TRUE;
	//SE SI E' MANIFESTATO UN RICHIAMO DI EMERGENCY
	IF EmergencyVis_FB THEN
		Emergency_FB:=FALSE;
	END_IF
END_IF
Opok:= sync AND NOT door_module_0.Done;

Exit instruction:
sync:=FALSE;
Opok:=FALSE;
```
### Exception_2:
```
//GO UP
Motor_on_FB:= TRUE;
Up_FB:= TRUE;
Vel_FB:= FALSE;
```
### Exception_3:
```
//STOP
Motor_on_FB:= FALSE;
//SE SI E' MANIFESTATA UN PROBLEMA AL PIANO ZERO (PARTICOLARITA')
Current_FB:=1;
//OPENING DOORS
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='open_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;
	Moves_module_0.Done:=TRUE;
	//SE SI E' MANIFESTATO UN RICHIAMO DI EMERGENCY
	IF EmergencyVis_FB THEN
		Emergency_FB:=FALSE;
	END_IF
END_IF
Opok:= sync AND NOT door_module_0.Done;

Exit instruction:
sync:=FALSE;
Opok:=FALSE;
```
### Closing_door:
```
//CLOSING DOORS
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='close_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;
END_IF

Opok:= sync and not door_module_0.Done;

Exit instruction:
sync:=FALSE;
Opok:=FALSE;
```
### Slow_u:
```
ramp_counter:=0;
//GOING UP
Motor_on_FB:= TRUE;
Up_FB:= TRUE;
Vel_FB:= FALSE;
```
### Stop_u:
```
IF LimitUp_FB THEN
//STOP
	Motor_on_FB:=FALSE;
	deck_error_FB:=TRUE;
ELSE
//STOP
	Motor_on_FB:=FALSE; 
	Current_FB:=Current_FB+1;
// SEND FLAG CALL MADE
	served_up_FB:=TRUE;
END_IF
```
### Stop_Op_Doors_up:
```
// RESET FLAG CALL MADE
served_up_FB:=FALSE;
//OPENING PORTE 
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='open_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;

END_IF

Opok:= sync AND NOT door_module_0.Done;

Exit instruction:
sync:=FALSE;
Opok:=FALSE;
Fast_u:
//GO UP
Motor_on_FB:= TRUE;
Up_FB:= TRUE;
Vel_FB:= TRUE;
```
### Check_Ramp_u:
```
//RAMPSENSOR CHECK
IF RampSensor THEN
	ramp_counter:=ramp_counter+1;
	IF ramp_counter=2 THEN
		deck_counter:=deck_counter+1;
	END_IF
	IF ramp_counter=3 THEN
		deck_error_FB:=TRUE;
	END_IF
END_IF
```
### Check_Deck_u:
```
//DECKSENSOR CHECK
IF DeckSensor THEN
	IF ramp_counter=1 THEN
		ramp_error_FB:=TRUE;
	END_IF
	ramp_counter:=0;
	IF Current_FB<deck_counter THEN
		Current_FB:=Current_FB+1;	
	END_IF
END_IF
```
### Errors_u:
```
//STOP
Motor_on_FB:=FALSE;
// SE VIENE CHIAMATA EMERGENZA DEVO ACCENDERE IL LED DI EMERGENZA
IF Emergency_FB THEN
	EmergencyVis_FB:=TRUE;
END_IF
deck_counter:=0;
Current_FB:=0;
```
### Slow_d:
```
ramp_counter:=0;
//GO DOWN
Motor_on_FB:= TRUE;
Up_FB:= FALSE;
Vel_FB:= FALSE;
```
### Stop_d:
```
IF LimitDown_FB THEN
//STOP
	Motor_on_FB:=FALSE;
	deck_error_FB:=TRUE;
ELSE
//STOP
	Motor_on_FB:=FALSE;
	Current_FB:=Current_FB-1;
// MANDO FLAG DI SERVIZIO CHIAMATA EFFETTUATO
	served_down_FB:=TRUE;
END_IF
```
### G_floor_Problem:
```
G_floor_Problem:
G_floor_prob:=TRUE;
```
### Stop_Op_Doors_down:
```
Stop_Op_Doors_down:
// RESET MADE SERVICE FLAG
served_down_FB:=FALSE;
//OPENING DOORS 
IF door_module_0.State='ready' THEN
	door_module_0.Do_:=TRUE;
	door_module_0.DoWhat:='open_door';
END_IF

IF door_module_0.Done AND door_module_0.State='busy' THEN
	door_module_0.Do_:=FALSE;
	sync:=TRUE;
END_IF

Opok:= sync AND NOT door_module_0.Done;

Exiting instruction:
sync:=FALSE;
Opok:=FALSE;
```
### Fast_d:
```
//GO DOWN
Motor_on_FB:= TRUE;
Up_FB:= FALSE;
Vel_FB:= TRUE;

IF LimitDown_FB THEN
	deck_error_FB:=TRUE;
END_IF
```
### Check_Ramp_d:
```
//RAMPSENSOR CHECK
IF RampSensor THEN
	ramp_counter:=ramp_counter+1;
	IF ramp_counter=2 THEN
		deck_counter:=deck_counter-1;
	END_IF
	IF ramp_counter=3 THEN
			deck_error_FB:=TRUE;
	END_IF
END_IF
```
### Check_Deck_d:
```
//DECKSENSOR CHECK
IF DeckSensor THEN
	IF ramp_counter=1 THEN
		ramp_error_FB:=TRUE;
	END_IF
	ramp_counter:=0;
	IF Current_FB>deck_counter THEN
		Current_FB:=Current_FB-1;	
	END_IF
END_IF
```
### Errors_d:
```
//STOP
Motor_on_FB:=FALSE;
//EMERGENCY = LED EMERGENCY
IF Emergency_FB THEN
	EmergencyVis_FB:=TRUE;
END_IF
IF NOT G_floor_prob THEN
	Current_FB:=0;
ELSE
deck_counter:=1;		
END_IF

```

## Scheme
![1](..\imgs\Picture1.png)
![1](..\imgs\Picture2.png)
![1](..\imgs\Picture3.png)
![1](..\imgs\Picture4.png)