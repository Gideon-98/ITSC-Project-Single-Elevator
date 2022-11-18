## Variables
```
Variables:
FUNCTION_BLOCK Cardinal
VAR_INPUT
	
	Start_: BOOL;
	Stop: BOOL;
	Current_FB: INT; //current floor visualization	

	Up_FB:BOOL;
	
	served_up_FB: BOOL; // finished service flag of target_up value
	served_down_FB: BOOL; // finished service flag of target_down value
	
	 (*Inputs: internal panel variables*)
	PG_FB: BOOL;
	P1_FB: BOOL;
	P2_FB: BOOL;
	P3_FB: BOOL;
	P4_FB: BOOL;
	P5_FB: BOOL;
	P6_FB: BOOL;
	P7_FB: BOOL;
	
	(*Inputs: external panel variables*)
	UpDeckG_FB: BOOL;
	UpDeck1_FB: BOOL;
	UpDeck2_FB: BOOL;
	UpDeck3_FB: BOOL;
	UpDeck4_FB: BOOL;
	UpDeck5_FB: BOOL;
	UpDeck6_FB: BOOL;
	DownDeck7_FB: BOOL;
	DownDeck6_FB: BOOL;
	DownDeck5_FB: BOOL;
	DownDeck4_FB: BOOL;
	DownDeck3_FB: BOOL;
	DownDeck2_FB: BOOL;
	DownDeck1_FB: BOOL;
END_VAR
VAR_OUTPUT
	
	State: STRING;
	
	target_up_FB: INT; //given value to Dest_target_up
	target_down_FB: INT; //given value to Dest_target_down
	
	Destination_FB: INT; //destination floor visualization
	
	enable_target_up_down_FB: BOOL; // to drive inside moves_module the direction of the calls
	
(*Visualization: internal panel variables used to turn on the LED*)
	PGVis_FB: BOOL;
	P1Vis_FB: BOOL;
	P2Vis_FB: BOOL;
	P3Vis_FB: BOOL;
	P4Vis_FB: BOOL;
	P5Vis_FB: BOOL;
	P6Vis_FB: BOOL;
	P7Vis_FB: BOOL;

(*Visualization: external panel variables used to turn on the LED*)
	UpDeckGVis_FB: BOOL;
	UpDeck1Vis_FB: BOOL;
	UpDeck2Vis_FB: BOOL;
	UpDeck3Vis_FB: BOOL;
	UpDeck4Vis_FB: BOOL;
	UpDeck5Vis_FB: BOOL;
	UpDeck6Vis_FB: BOOL;
	DownDeck7Vis_FB: BOOL;
	DownDeck6Vis_FB: BOOL;
	DownDeck5Vis_FB: BOOL;
	DownDeck4Vis_FB: BOOL;
	DownDeck3Vis_FB: BOOL;
	DownDeck2Vis_FB: BOOL;
	DownDeck1Vis_FB: BOOL;
	
END_VAR
VAR
	Dest_floor: INT;//contains the destination variable
	can_be_clicked: BOOL;
	up_motion: BOOL; 
	prio: INT;

	//CALLS ARRAY

	array_dest_up_onfly_FB: ARRAY[0..7] OF INT;

	array_dest_up_stored_FB: ARRAY[0..7] OF INT;
	

	array_dest_down_onfly_FB: ARRAY[0..7] OF INT;

	array_dest_down_stored_FB: ARRAY[0..7] OF INT;
	
	// scroll counters for writing arrays
	sliding_index_1: INT;
	sliding_index_2: INT;
	sliding_index_3: INT;
	sliding_index_4: INT;
	
	// sort counter for arrays
	k: INT;
	i: INT;
	k_1: INT;
	i_1: INT;

	//dummy variables
	var1: INT;

	var1_1: INT;
	
	min_index: INT;
	min_index_1: INT;
	max_index: INT;
	max_index_1: INT;
	already_pres: BOOL;

	//counters	
	counter_calls_up_stored: INT;
	counter_calls_down_stored: INT;
	counter_calls_up_onfly: INT;
	counter_calls_down_onfly: INT;

	cont: INT;
	cancel_time: BOOL;
END_VAR
Scheme:
```

```
Init:
//INITIALIZATION OF THE CARDINAL'S OUTPUT
target_up_FB:=8;
target_down_FB:=-1;
Destination_FB:=0;
enable_target_up_down_FB:=TRUE;

(*Visualization: internal panel variables used to turn on the LED*)
PGVis_FB:=FALSE;
P1Vis_FB:=FALSE;
P2Vis_FB:=FALSE;
P3Vis_FB:=FALSE;
P4Vis_FB:=FALSE;
P5Vis_FB:=FALSE;
P6Vis_FB:=FALSE;
P7Vis_FB:=FALSE;

(*Visualization: external panel variables used to turn on the LED*)
UpDeckGVis_FB:=FALSE;
UpDeck1Vis_FB:=FALSE;
UpDeck2Vis_FB:=FALSE;
UpDeck3Vis_FB:=FALSE;
UpDeck4Vis_FB:=FALSE;
UpDeck5Vis_FB:=FALSE;
UpDeck6Vis_FB:=FALSE;
DownDeck7Vis_FB:=FALSE;
DownDeck6Vis_FB:=FALSE;
DownDeck5Vis_FB:=FALSE;
DownDeck4Vis_FB:=FALSE;
DownDeck3Vis_FB:=FALSE;
DownDeck2Vis_FB:=FALSE;
DownDeck1Vis_FB:=FALSE;

//INTIALIZATION ARRAYS AT AN ABSURD PRIORITY VALUE, LIKE 8, WITH DESTINATION OUT OF RANGE, LIKE 8
FOR sliding_index_1 := 0 TO 7 BY 1 DO
	array_dest_up_onfly_FB[sliding_index_1]:=8;
	array_dest_up_stored_FB[sliding_index_1]:=8;
	
	array_dest_down_onfly_FB[sliding_index_1]:=-1;
	array_dest_down_stored_FB[sliding_index_1]:=-1;
END_FOR;

//INTIALIZATION SCROLL INDEX


//INTIALIZATION LOCAL VARIABLES
Dest_floor:=8; //destination
can_be_clicked:=TRUE; //let another call only after the sorting algorithm is finished 
up_motion:=false; //gives a "direction" for the calls
cancel_time:=TRUE;
 //indexs
k:=0;
k_1:=0;
i:=0;
i_1:=0;
min_index:=8;
min_index_1:=8;
max_index:=8;
max_index_1:=8;
//dummies for sorting
var1:=0;
var1_1:=0;
//scroll index for the destination visualization 
sliding_index_1:=0;
sliding_index_2:=0;
sliding_index_3:=0;
sliding_index_4:=0;
//utility flags
already_pres:=FALSE;
counter_calls_up_stored:=0;
counter_calls_down_stored:=0;
counter_calls_up_onfly:=0;
counter_calls_down_onfly:=0;

Ready:
State:='ready';

Busy:
State:='busy';

Lights:
//INTERNAL PUSH BUTTON CALLS INTERFACE
(*I HAVE CHOSEN TO GIVE THESE CALLS A 'DIRECTION' SO AS TO TREAT THEM IN THE SAME WAY AS CALLS FROM THE EXTERNAL BUTTON, THE VERSE IN THIS ISSUE IS FROM THE FLAG up_elev_curr_FB THAT IS FROM THE FLAG OF THE ELEVATOR DIRECTION COMING OUT OF THE MOVES GA (MOVES_MODULE) WITH A VALUE AT THE BEGINNING OF THE PROGRAM EQUAL TO TRUE BECAUSE AFTER HOMING I CAN ONLY GO UP. IS A FEEDBACK PRINCIPLE BECAUSE IN  IN TURN MOVES_MODULE WILL READ CARDINAL OUTPUTS TO KNOW THE DESTINATION.*)
//up_elev_curr_FB: INPUT FOR CARDINAL AND MOVES_MODULE
//up_motion: WHERE SORTING THE CALLS?

IF PG_FB AND can_be_clicked THEN
	PGVis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=0;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P1_FB AND can_be_clicked THEN
	P1Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=1;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P2_FB AND can_be_clicked  THEN
	P2Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=2;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P3_FB AND can_be_clicked  THEN
	P3Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=3;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P4_FB AND can_be_clicked  THEN
	P4Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=4;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P5_FB AND can_be_clicked  THEN
	P5Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=5;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P6_FB AND can_be_clicked  THEN
	P6Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=6;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF
IF P7_FB AND can_be_clicked  THEN
	P7Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=7;
	IF Dest_floor>Current_FB THEN
		up_motion:=TRUE;
	ELSE
		up_motion:=FALSE;
	END_IF
END_IF

//EXTERNAL BUTTONS INTERFACE

(*FIXED DIRECTION OF THE CALLS*)

IF UpDeckG_FB AND can_be_clicked  THEN
	UpDeckGVis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=0;
	up_motion:=TRUE;
END_IF
IF UpDeck1_FB AND can_be_clicked  THEN
	UpDeck1Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=1;
	up_motion:=TRUE;
END_IF
IF UpDeck2_FB AND can_be_clicked  THEN
	UpDeck2Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=2;
	up_motion:=TRUE;
END_IF
IF UpDeck3_FB AND can_be_clicked  THEN
	UpDeck3Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=3;
	up_motion:=TRUE;
END_IF
IF UpDeck4_FB AND can_be_clicked  THEN
	UpDeck4Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=4;
	up_motion:=TRUE;
END_IF
IF UpDeck5_FB AND can_be_clicked  THEN
	UpDeck5Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=5;
	up_motion:=TRUE;
END_IF
IF UpDeck6_FB AND can_be_clicked  THEN
	UpDeck6Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=6;
	up_motion:=TRUE;
END_IF
IF DownDeck7_FB AND can_be_clicked  THEN
	DownDeck7Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=7;
	up_motion:=FALSE;
END_IF
IF DownDeck6_FB AND can_be_clicked  THEN
	DownDeck6Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=6;
	up_motion:=FALSE;
END_IF
IF DownDeck5_FB AND can_be_clicked  THEN
 	DownDeck5Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=5;
	up_motion:=FALSE;
END_IF
IF DownDeck4_FB AND can_be_clicked  THEN
	DownDeck4Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=4;
	up_motion:=FALSE;
END_IF
IF DownDeck3_FB AND can_be_clicked  THEN
	DownDeck3Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=3;
	up_motion:=FALSE;
END_IF
IF DownDeck2_FB AND can_be_clicked  THEN
	DownDeck2Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=2;
	up_motion:=FALSE;
END_IF
IF DownDeck1_FB AND can_be_clicked  THEN
	DownDeck1Vis_FB:=TRUE;
	can_be_clicked:=FALSE;
	Dest_floor:=1;
	up_motion:=FALSE;
END_IF

Cancel_call:
//IF MOVES_MODULE EXECUTED THE CALL SENDS A FLAG OF CORRECT SERVICE CALL UP
IF served_up_FB THEN
//DEVO SPEGNERE L'ILLUMINAZIONE DEL TASTO
	IF target_up_FB=0 THEN
		IF PGVis_FB THEN
			PGVis_FB:= FALSE;
		END_IF
		IF UpDeckGVis_FB THEN
			UpDeckGVis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=1 THEN
		IF P1Vis_FB THEN
			P1Vis_FB:= FALSE;
		END_IF
		IF UpDeck1Vis_FB THEN
			UpDeck1Vis_FB:= FALSE;
		END_IF
		IF DownDeck1Vis_FB THEN
			DownDeck1Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=2 THEN
		IF P2Vis_FB THEN
			P2Vis_FB:= FALSE;
		END_IF
		IF UpDeck2Vis_FB THEN
			UpDeck2Vis_FB:= FALSE;
		END_IF
		IF DownDeck2Vis_FB THEN
			DownDeck2Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=3 THEN
		IF P3Vis_FB THEN
			P3Vis_FB:= FALSE;
		END_IF
		IF UpDeck3Vis_FB THEN
			UpDeck3Vis_FB:= FALSE;
		END_IF
		IF DownDeck3Vis_FB THEN
			DownDeck3Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=4 THEN
		IF P4Vis_FB THEN
			P4Vis_FB:= FALSE;
		END_IF
		IF UpDeck4Vis_FB THEN
			UpDeck4Vis_FB:= FALSE;
		END_IF
		IF DownDeck4Vis_FB THEN
			DownDeck4Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=5 THEN
		IF P5Vis_FB THEN
			P5Vis_FB:= FALSE;
		END_IF
		IF UpDeck5Vis_FB THEN
			UpDeck5Vis_FB:= FALSE;
		END_IF
		IF DownDeck5Vis_FB THEN
			DownDeck5Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=6 THEN
		IF P6Vis_FB THEN
			P6Vis_FB:= FALSE;
		END_IF
		IF UpDeck6Vis_FB THEN
			UpDeck6Vis_FB:= FALSE;
		END_IF
		IF DownDeck6Vis_FB THEN
			DownDeck6Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_up_FB=7 THEN
		IF P7Vis_FB THEN
			P7Vis_FB:= FALSE;
		END_IF
		IF DownDeck7Vis_FB THEN
			DownDeck7Vis_FB:= FALSE;
		END_IF
	END_IF

//RIORDINO L'ARRAY UP
k_1:=0;	
i_1:=0;	
WHILE i_1<=7 DO
	k_1:=i_1+1;
	min_index_1:=i_1;
	WHILE k_1<=7 DO
		IF array_dest_up_onfly_FB[k_1]<array_dest_up_onfly_FB[i_1] THEN
			min_index_1:=k_1;
		END_IF	
		k_1:=k_1+1;
	END_WHILE
	var1_1:= array_dest_up_onfly_FB[i_1];
	array_dest_up_onfly_FB[i_1]:=array_dest_up_onfly_FB[min_index_1];
	array_dest_up_onfly_FB[min_index_1]:=var1_1;
	i_1:=i_1+1;
END_WHILE
target_up_FB:=array_dest_up_onfly_FB[0];

//IF MOVES_MODULE EXECUTED THE CALL SENDS A FLAG OF CORRECT SERVICE CALL DOWN

IF served_down_FB THEN
//HAVE TO TURN OFF THE KEY LIGHTING
	IF target_down_FB=0 THEN
		IF PGVis_FB THEN
			PGVis_FB:= FALSE;
		END_IF
		IF UpDeckGVis_FB THEN
			UpDeckGVis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=1 THEN
		IF P1Vis_FB THEN
			P1Vis_FB:= FALSE;
		END_IF
		IF UpDeck1Vis_FB THEN
			UpDeck1Vis_FB:= FALSE;
		END_IF
		IF DownDeck1Vis_FB THEN
			DownDeck1Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=2 THEN
		IF P2Vis_FB THEN
			P2Vis_FB:= FALSE;
		END_IF
		IF UpDeck2Vis_FB THEN
			UpDeck2Vis_FB:= FALSE;
		END_IF
		IF DownDeck2Vis_FB THEN
			DownDeck2Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=3 THEN
		IF P3Vis_FB THEN
			P3Vis_FB:= FALSE;
		END_IF
		IF UpDeck3Vis_FB THEN
			UpDeck3Vis_FB:= FALSE;
		END_IF
		IF DownDeck3Vis_FB THEN
			DownDeck3Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=4 THEN
		IF P4Vis_FB THEN
			P4Vis_FB:= FALSE;
		END_IF
		IF UpDeck4Vis_FB THEN
			UpDeck4Vis_FB:= FALSE;
		END_IF
		IF DownDeck4Vis_FB THEN
			DownDeck4Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=5 THEN
		IF P5Vis_FB THEN
			P5Vis_FB:= FALSE;
		END_IF
		IF UpDeck5Vis_FB THEN
			UpDeck5Vis_FB:= FALSE;
		END_IF
		IF DownDeck5Vis_FB THEN
			DownDeck5Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=6 THEN
		IF P6Vis_FB THEN
			P6Vis_FB:= FALSE;
		END_IF
		IF UpDeck6Vis_FB THEN
			UpDeck6Vis_FB:= FALSE;
		END_IF
		IF DownDeck6Vis_FB THEN
			DownDeck6Vis_FB:= FALSE;
		END_IF
	END_IF
	IF target_down_FB=7 THEN
		IF P7Vis_FB THEN
			P7Vis_FB:= FALSE;
		END_IF
		IF DownDeck7Vis_FB THEN
			DownDeck7Vis_FB:= FALSE;
		END_IF
	END_IF
	array_dest_down_onfly_FB[0]:= -1;
	// sorted array
END_IF


//SORTING ARRAY DOWN
k_1:=0;
i_1:=0;
WHILE i_1<=7 DO
	k_1:=i_1+1;
	max_index_1:=i_1;
	WHILE k_1<=7 DO
		IF array_dest_down_onfly_FB[k_1]>array_dest_down_onfly_FB[i_1] THEN
			max_index_1:=k_1;
		END_IF	
		k_1:=k_1+1;
		END_WHILE
	var1_1:= array_dest_down_onfly_FB[i_1];
	array_dest_down_onfly_FB[i_1]:=array_dest_down_onfly_FB[max_index_1];
	array_dest_down_onfly_FB[max_index_1]:=var1_1;		
	i_1:=i_1+1;
END_WHILE
target_down_FB:=array_dest_down_onfly_FB[0];

//UPDATING TARGET
IF Up_FB THEN
	Destination_FB:= target_up_FB;
ELSE
	Destination_FB:= target_down_FB;
END_IF

Save_Arrays:
//ARRAY_UP 
IF up_motion THEN
	FOR i := 0 TO 7 BY 1 DO
		IF Dest_floor=array_dest_up_onfly_FB[i] THEN
			already_pres:=TRUE;
		END_IF
	END_FOR
	IF NOT already_pres THEN
//I AM IN THE ONFLY ARRAY
		IF Dest_floor>Current_FB THEN //vado negli array per le chiamate onfly
			array_dest_up_onfly_FB[sliding_index_1]:= Dest_floor; // carico nell'array delle dest
			sliding_index_1:=sliding_index_1+1; // incremento l'indice entro cui metterò la prossima chiamata verso l'alto 
			k:=0;
			i:=0;
			//SELECTION SORTER
			WHILE i<=7 DO
				k:=i+1;
				min_index:=i;
				WHILE k<=7 DO
					IF array_dest_up_onfly_FB[k]<array_dest_up_onfly_FB[i] THEN
						min_index:=k;
					END_IF	
					k:=k+1;
				END_WHILE
				var1:= array_dest_up_onfly_FB[i];	
				array_dest_up_onfly_FB[i]:=array_dest_up_onfly_FB[min_index];	
				array_dest_up_onfly_FB[min_index]:=var1;
				i:=i+1;
			END_WHILE
		ELSIF Dest_floor<=Current_FB THEN
//I AM IN THE STORED ARRAY			
			array_dest_up_stored_FB[sliding_index_2]:= Dest_floor; // carico nell'array delle dest
			sliding_index_2:=sliding_index_2+1; // incremento l'indice entro cui metterò la prossima chiamata verso l'alto
			k:=0;
			i:=0;
			//SELECTION SORTER
			WHILE i<=7 DO
				k:=i+1;
				min_index:=i;
				WHILE k<=7 DO
					IF array_dest_up_stored_FB[k]<array_dest_up_stored_FB[i] THEN
						min_index:=k;
					END_IF	
					k:=k+1;
				END_WHILE
				var1:= array_dest_up_stored_FB[i];
				array_dest_up_stored_FB[i]:=array_dest_up_stored_FB[min_index];	
				array_dest_up_stored_FB[min_index]:=var1;
				i:=i+1;
			END_WHILE
		END_IF
	target_up_FB:=array_dest_up_onfly_FB[0];
	END_IF
	IF target_up_FB=8 THEN
//CHECK THE FIRST VALUE OF THE ARRAY UP, IF=8 I TRY TO SEE THE OTHER ARRAYS		
		FOR cont := 0 TO 7 BY 1 DO
			IF array_dest_up_onfly_FB[cont]=8 THEN
				counter_calls_up_onfly:= counter_calls_up_onfly+1;
			END_IF
			IF array_dest_down_onfly_FB[cont]=-1 THEN
				counter_calls_down_onfly:= counter_calls_down_onfly+1;
			END_IF
			IF array_dest_up_stored_FB[cont]=8 THEN
				counter_calls_up_stored:= counter_calls_up_stored+1;
			END_IF
			IF array_dest_down_stored_FB[cont]=-1 THEN
				counter_calls_down_stored:= counter_calls_down_stored+1;
			END_IF
		END_FOR
//CASES
(*CASE:
FLY_UP EMPTY
FLY_DOWN FULL
STORED_UP NOT RELEVANT
STORED_DOWN NOT RELEVANT
COPY THE CALLS IN STORED_UP IN FLY_UP WHILE SERVING FLY_DOWN
 *)
		IF 	counter_calls_up_onfly=8 AND counter_calls_down_onfly<8 AND 
			counter_calls_up_stored<=8 AND counter_calls_down_stored<=8 THEN	

			FOR cont := 0 TO 7 BY 1 DO
				array_dest_up_onfly_FB[cont]:=array_dest_up_stored_FB[cont];
				array_dest_up_stored_FB[cont]:=8;
			END_FOR;
			sliding_index_1:=sliding_index_2;
			sliding_index_2:=0;
			enable_target_up_down_FB:=FALSE; //REVERSAL OF DOWNWARD MOTION
		END_IF
(*CASE:
FLY_UP EMPTY
FLY_DOWN EMPTY
STORED_UP FULL
STORED_DOWN EMPTY
COPY THE FIRST CALL IN FLY_UP AND THE OTHERS IN FLY_DOWN,IN THIS WAY IT CAN REACH THE LOW POSITIONING 
*)
		IF 	counter_calls_up_onfly=8 AND counter_calls_down_onfly=8 AND 
			counter_calls_up_stored<8 AND counter_calls_down_stored=8 THEN
//WIP		
			array_dest_down_onfly_FB[0]:=array_dest_up_stored_FB[0];
			FOR cont := 1 TO 7 BY 1 DO
				array_dest_up_onfly_FB[cont]:=array_dest_up_stored_FB[cont];
				array_dest_up_stored_FB[cont]:=8;
			END_FOR;
			sliding_index_1:=sliding_index_2-1;
			sliding_index_2:=0;
			sliding_index_3:=1;
			enable_target_up_down_FB:=FALSE;
		END_IF
	ELSE
		enable_target_up_down_FB:=TRUE;
	END_IF
	
	can_be_clicked:=TRUE;
//ARRAY_DOWN
ELSE
	FOR i := 0 TO 7 BY 1 DO
		IF Dest_floor=array_dest_down_onfly_FB[i] THEN
			already_pres:=TRUE;
		END_IF
	END_FOR
	IF NOT already_pres THEN
//ARRAY OF THE ONFLY CALLS
		IF Dest_floor<Current_FB  THEN
			array_dest_down_onfly_FB[sliding_index_3]:= Dest_floor; // carico nell'array delle dest
			sliding_index_3:=sliding_index_3+1; // incremento l'indice entro cui metterò la prossima chiamata verso l'alto 
			k:=0;
			i:=0;
//SELECTION SORTER
			WHILE i<=7 DO
				k:=i+1;
				max_index:=i;
				WHILE k<=7 DO
					IF array_dest_down_onfly_FB[k]>array_dest_down_onfly_FB[i] THEN
						max_index:=k;
					END_IF
					k:=k+1;
				END_WHILE
				var1:= array_dest_down_onfly_FB[i];
				array_dest_down_onfly_FB[i]:=array_dest_down_onfly_FB[max_index];
				array_dest_down_onfly_FB[max_index]:=var1;	
				i:=i+1;
			END_WHILE
		ELSIF Dest_floor>=Current_FB THEN
//ARRAY OF THE STORED CALLS
			array_dest_down_stored_FB[sliding_index_4]:= Dest_floor; // carico nell'array delle dest
			sliding_index_4:=sliding_index_4+1; // incremento l'indice entro cui metterò la prossima chiamata verso l'alto
			k:=0;
			i:=0;
//SELECTION SORTER
			WHILE i<=7 DO
				k:=i+1;
				max_index:=i;
				WHILE k<=7 DO
					IF array_dest_down_stored_FB[i]<array_dest_down_stored_FB[k] THEN
						max_index:=k;
					END_IF	
					k:=k+1;
				END_WHILE
				var1:= array_dest_down_stored_FB[i];
				array_dest_down_stored_FB[i]:=array_dest_down_stored_FB[max_index];
				array_dest_down_stored_FB[max_index]:=var1;
				i:=i+1;
			END_WHILE
		END_IF
	target_down_FB:=array_dest_down_onfly_FB[0];
	END_IF
	IF target_down_FB=-1 THEN
//CHECK THE FIRST VALUE OF THE ARRAY UP, IF=8 I TRY TO SEE THE OTHER ARRAYS	
		FOR cont := 0 TO 7 BY 1 DO
			IF array_dest_up_onfly_FB[cont]=8 THEN
				counter_calls_up_onfly:= counter_calls_up_onfly+1;
			END_IF
			IF array_dest_down_onfly_FB[cont]=-1 THEN
				counter_calls_down_onfly:= counter_calls_down_onfly+1;
			END_IF
			IF array_dest_up_stored_FB[cont]=8 THEN
				counter_calls_up_stored:= counter_calls_up_stored+1;
			END_IF
			IF array_dest_down_stored_FB[cont]=-1 THEN
				counter_calls_down_stored:= counter_calls_down_stored+1;
			END_IF
		END_FOR
//CASES
(*CASE WITH:
FLY_UP FULL
FLY_DOWN EMPTY
STORED_UP NOT RELEVANT
STORED_DOWN NOT RELEVANT
COPY THE CALLS IN STORED_DOWN IN FLY_DOWN WHILE SERVING FLY_UP
 *)
		IF 	counter_calls_up_onfly<8 AND counter_calls_down_onfly=8 AND 
			counter_calls_up_stored<=8 AND counter_calls_down_stored<=8 THEN	

			FOR cont := 0 TO 7 BY 1 DO
				array_dest_down_onfly_FB[cont]:=array_dest_down_stored_FB[cont];
				array_dest_down_stored_FB[cont]:=-1;
			END_FOR;
			sliding_index_3:=sliding_index_4;
			sliding_index_4:=0;
			enable_target_up_down_FB:=TRUE;
		END_IF
(*CASE WITH:
FLY_UP EMPTY
FLY_DOWN EMPTY
STORED_UP EMPTY
STORED_DOWN FULL
COPY THE FIRST CALL IN FLY_UP AND THE OTHERS IN FLY_DOWN,IN THIS WAY IT CAN REACH THE HIGH POSITIONING
*)
		IF 	counter_calls_up_onfly=8 AND counter_calls_down_onfly=8 AND 
			counter_calls_up_stored=8 AND counter_calls_down_stored<8 THEN
//WIP	
			array_dest_up_onfly_FB[0]:=array_dest_down_stored_FB[0];
			FOR cont := 1 TO 7 BY 1 DO
				array_dest_down_onfly_FB[cont]:=array_dest_down_stored_FB[cont];					
				array_dest_down_stored_FB[cont]:=-1;
			END_FOR;
			sliding_index_3:=sliding_index_4-1;
			sliding_index_4:=0;
			sliding_index_1:=1;
			enable_target_up_down_FB:=TRUE;
		END_IF
	ELSE
		enable_target_up_down_FB:=FALSE;		
	END_IF
	can_be_clicked:=TRUE;
END_IF
target_down_FB:=array_dest_down_onfly_FB[0];
target_up_FB:=array_dest_up_onfly_FB[0];

already_pres:=FALSE; //clean the flag

counter_calls_up_stored:=0;
counter_calls_down_stored:=0;
counter_calls_up_onfly:=0;
counter_calls_down_onfly:=0;
```