# Simulation
```
(* Initial position of the cab inside the hoistway. It is executed only at the first cycle.  *)
IF NOT GVL.InitCycle THEN
			GVL.InitCycle := TRUE ;
			GVL.Height := 20 * GVL.InitialDeck;
		    GVL.Door := 0;
			GVL.Current := GVL.Height / 20;
END_IF;

(* Elevator management, these instructions are executed each cycle time*)

(* Elevator door control. *)
IF GVL.Closing THEN
	GVL.Door := GVL.Door + 1;		(*Closing door management*)
	GVL.Open := FALSE;
END_IF

IF GVL.Door >= 60 THEN						(* Closed door limit*)
	GVL.Closed := TRUE;
END_IF

IF GVL.Opening THEN
	GVL.Door := GVL.Door - 1; 	(* Opening door management*)
	GVL.Closed := FALSE;
END_IF

IF GVL.Door <= 0 THEN							(* Open door limit*)
	GVL.Open := TRUE;
END_IF


(* Maximum and minimum management in the door simulation*)
IF GVL.Door < 0 THEN
	 GVL.Door := 0;
END_IF

IF GVL.Door > 60 THEN
	GVL.Door := 60;
END_IF


(* Control of the cab position inside the hostway. *)
(* Displacement definition for graphic visualization*)
IF GVL.Vel THEN 
	GVL.Displacement := 2;
ELSE 
	GVL.Displacement := 1;
END_IF


(* Height modification relying on direction and speed*)
IF GVL.Motor_on AND GVL.Up THEN
	GVL.Height := GVL.Height + GVL.Displacement;
	GVL.LimitDown := FALSE;
END_IF

IF GVL.Motor_on AND NOT GVL.Up THEN
	GVL.Height := GVL.Height - GVL.Displacement;
	GVL.LimitUp := FALSE;
END_IF


(* Limits for visualization*)
IF GVL.Height < -20 THEN
	GVL.Height := -20;
END_IF

IF GVL.Height > 160 THEN
	GVL.Height := 160;
END_IF

(* Limit sensors management*)
IF GVL.Height <= -10 THEN
	GVL.LimitDown := TRUE;
END_IF

IF GVL.Height >= 150 THEN
	 GVL.LimitUp := TRUE;
END_IF


(*-------------------------------------------------------------------*)
(*  Fault Management (Deck and Ramp Sensors)   *)
(*-------------------------------------------------------------------*)
(* Deck Sensor*)
(* Range of value where the deck sensor is active*)
IF ((GVL.Height >= -1) AND (GVL.Height <= 1)) THEN
	GVL.SensorGOK := TRUE;
ELSE  
	GVL.SensorGOK := FALSE;
END_IF

IF ((GVL.Height >= 20-1) AND (GVL.Height <= 20+1)) THEN
	GVL.Sensor1OK := TRUE;
ELSE  
	GVL.Sensor1OK := FALSE;
END_IF

IF ((GVL.Height >= 40-1) AND (GVL.Height <= 40+1)) THEN
	GVL.Sensor2OK := TRUE;
ELSE  
	GVL.Sensor2OK := FALSE;
END_IF

IF ((GVL.Height >= 60-1) AND (GVL.Height <= 60+1)) THEN
	GVL.Sensor3OK := TRUE;
ELSE  
	GVL.Sensor3OK := FALSE;
END_IF

IF ((GVL.Height >= 80-1) AND (GVL.Height <= 80+1)) THEN
	GVL.Sensor4OK := TRUE;
ELSE  
	GVL.Sensor4OK := FALSE;
END_IF

IF ((GVL.Height >= 100-1) AND (GVL.Height <= 100+1)) THEN
	GVL.Sensor5OK := TRUE;
ELSE   
	GVL.Sensor5OK := FALSE;
END_IF

IF ((GVL.Height >= 120-1) AND (GVL.Height <= 120+1)) THEN
	GVL.Sensor6OK := TRUE;
ELSE  
	GVL.Sensor6OK := FALSE;
END_IF

IF ((GVL.Height >= 140-1) AND (GVL.Height <= 140+1)) THEN
	GVL.Sensor7OK := TRUE;
ELSE  
	GVL.Sensor7OK := FALSE;
END_IF

GVL.DeckSensor := (GVL.SensorGOK AND NOT GVL.SensorG) OR
				    (GVL.Sensor1OK AND NOT GVL.Sensor1) OR
				    (GVL.Sensor2OK AND NOT GVL.Sensor2) OR
				    (GVL.Sensor3OK AND NOT GVL.Sensor3) OR
				    (GVL.Sensor4OK AND NOT GVL.Sensor4) OR
				    (GVL.Sensor5OK AND NOT GVL.Sensor5) OR
				    (GVL.Sensor6OK AND NOT GVL.Sensor6) OR
				    (GVL.Sensor7OK AND NOT GVL.Sensor7);

(* Ramp Sensor*)
(* Range of value where the ramp sensor is active*)
IF ((GVL.Height >= 5-1) AND (GVL.Height <= 5+1)) THEN
	GVL.RampGUpOK := TRUE;
ELSE 
	GVL.RampGUpOK := FALSE;
END_IF

(* Ramp close to deck 1*)
IF ((GVL.Height >= 15-1) AND (GVL.Height <= 15+1)) THEN
	GVL.Ramp1DownOK := TRUE;
ELSE 
	GVL.Ramp1DownOK := FALSE;
END_IF

IF ((GVL.Height >= 25-1) AND (GVL.Height <= 25+1)) THEN
	GVL.Ramp1UpOK := TRUE;
ELSE 
	GVL.Ramp1UpOK := FALSE;
END_IF

(* Ramp close to deck 2*)
IF ((GVL.Height >= 35-1) AND (GVL.Height <= 35+1)) THEN
	GVL.Ramp2DownOK := TRUE;
ELSE 
	GVL.Ramp2DownOK := FALSE;
END_IF

IF ((GVL.Height >= 45-1) AND (GVL.Height <= 45+1)) THEN
	GVL.Ramp2UpOK := TRUE;
ELSE 
	GVL.Ramp2UpOK := FALSE;
END_IF

(*Ramp close to deck  3*)
IF ((GVL.Height >= 55-1) AND (GVL.Height <= 55+1)) THEN
	GVL.Ramp3DownOK := TRUE;
ELSE 
	GVL.Ramp3DownOK := FALSE;
END_IF

IF ((GVL.Height >= 65-1) AND (GVL.Height <= 65+1)) THEN
	GVL.Ramp3UpOK := TRUE;
ELSE 
	GVL.Ramp3UpOK := FALSE;
END_IF

(* Ramp close to deck  4*)
IF ((GVL.Height >= 75-1) AND (GVL.Height <= 75+1)) THEN
	GVL.Ramp4DownOK := TRUE;
ELSE 
	GVL.Ramp4DownOK := FALSE;
END_IF

IF ((GVL.Height >= 85-1) AND (GVL.Height <= 85+1)) THEN
	GVL.Ramp4UpOK := TRUE;
ELSE 
	GVL.Ramp4UpOK:=FALSE;
END_IF

(* Ramp close to deck  5*)
IF ((GVL.Height >= 95-1) AND (GVL.Height <= 95+1)) THEN
	GVL.Ramp5DownOK := TRUE;
ELSE 
	GVL.Ramp5DownOK := FALSE;
END_IF

IF ((GVL.Height >= 105-1) AND (GVL.Height <= 105+1)) THEN
	GVL.Ramp5UpOK := TRUE;
ELSE
	GVL.Ramp5UpOK := FALSE;
END_IF

(* Ramp close to deck  6*)
IF ((GVL.Height >= 115-1) AND (GVL.Height <= 115+1)) THEN
	GVL.Ramp6DownOK := TRUE;
ELSE 
	GVL.Ramp6DownOK := FALSE;
END_IF

IF ((GVL.Height >= 125-1) AND (GVL.Height <= 125+1)) THEN
	GVL.Ramp6UpOK := TRUE;
ELSE 
	GVL.Ramp6UpOK := FALSE;
END_IF

(* Ramp close to deck  7*)
IF ((GVL.Height >= 135-1) AND (GVL.Height <= 135+1)) THEN
	GVL.Ramp7DownOK := TRUE;
ELSE 
	GVL.Ramp7DownOK := FALSE;
END_IF

GVL.RampSensor:=(GVL.RampGUpOK AND NOT GVL.RampGUp) OR 
								(GVL.Ramp1DownOK AND NOT GVL.Ramp1Down ) OR 
								(GVL.Ramp1UpOK AND NOT GVL.Ramp1Up ) OR
								(GVL.Ramp2DownOK AND NOT GVL.Ramp2Down ) OR 
								(GVL.Ramp2UpOK AND NOT GVL.Ramp2Up ) OR
								(GVL.Ramp3DownOK AND NOT GVL.Ramp3Down ) OR
 								(GVL.Ramp3UpOK AND NOT GVL.Ramp3Up ) OR
								(GVL.Ramp4DownOK AND NOT GVL.Ramp4Down ) OR
 								(GVL.Ramp4UpOK AND NOT GVL.Ramp4Up ) OR
								(GVL.Ramp5DownOK AND NOT GVL.Ramp5Down ) OR
 								(GVL.Ramp5UpOK AND NOT GVL.Ramp5Up ) OR
								(GVL.Ramp6DownOK AND NOT GVL.Ramp6Down ) OR
								(GVL.Ramp6UpOK AND NOT GVL.Ramp6Up ) OR
								(GVL.Ramp7DownOK AND NOT GVL.Ramp7Down );
```