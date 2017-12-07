# Getting-Com-Port-input
External device sends information to com port, but How do I get it with QB64 code?
C code in external device sends info to com port
 if ( cmd == 2)//getanalog
   {
        int sensorValue = analogRead(pin);//
        //delay(10);
        float voltage= sensorValue*(5.0/1023.0);
        Serial.print("Voltage =");
        Serial.print(voltage);
        Serial.print(",");
        //Serial.print(sensorValue);//(analogRead(pin)) replaced with (sensorValue)
       Serial.print("*");
      }
      I send the device the command=2 and pin=4 it should return the voltage of a 1.2Vdc battery connected between Gnd and pin 4
      My program runs and I only get 10 blank spaces.
      MY QB64 TEST CODE:
      CLS
ctr = 1                                                                                      ' Counts number times you press continue
again:
INPUT "Press any key to continue"; d$                                                 ‘ Run program to receive voltage reading

OPEN "Com3:9600,n,8,1,ds0,cs0,rs" FOR RANDOM AS #1
SLEEP 1                                                                                                      ‘ delay to let com settle
FIELD #1, 50 AS msg$

                                                                                              ‘ Form Message to send to External Device
Command = 2: pin = 4: argument = 0 '                                                          ‘ Code to Get analog voltage reading
msg$ = ""
msg$ = msg$ + RIGHT$("00" + STR$(Command), 2) + ","
msg$ = msg$ + RIGHT$("00" + STR$(pin), 2) + ","
msg$ = msg$ + RIGHT$("0000" + STR$(argument), 4) + "*"                                          ‘*= end of message signal
PRINT "msg$ Sent= "; msg$, CHR$(13)                                                               ‘ Show Message
LSET m$ = msg$

PUT #1, , m$                                                                                 ‘  Send message to the external device

'C code in external device
'External device responds to message & prints to Com3 buffer.
' if ( cmd == 2)//getanalog
'   {
'        int sensorValue = analogRead(pin);//
'        //delay(10);
'        float voltage= sensorValue*(5.0/1023.0);
'        Serial.print("Voltage =");
'        Serial.print(voltage);
'        Serial.print(",");
'        //Serial.print(sensorValue);//(analogRead(pin)) replaced with (sensorValue)
'       Serial.print("*");
'      }

CLOSE #1

m$ = "": msg$ = ""                                                                                       ‘  Wipe out old mesages

OPEN "Com3:9600,n,8,1,ds0,cs0,rs" FOR RANDOM AS #2
SLEEP 1                                                                                               ‘ Delay to let Com Port settle
FIELD #2, 50 AS m$

t1 = TIMER                                                                                               ‘ Start time for getdata

getdata:
bytes% = LOC(2)                                                                                  ‘ Check receive buffer for data
IF bytes% > 0 THEN                                                                     ‘ After thousands of getdata cycles Have 1 byte
    DO UNTIL EOF(2)
        GET #2, , m$                                                                                  ‘First DO Loop gets EOF
        PRINT "LEN(m$)= "; LEN(m$)                                                                    ‘Length of m$ = 10
        IF INSTR(m$, "*") > 0 THEN GOTO duh                        ‘Check for end of message,Never finds End of Message (*)
    LOOP
END IF
IF bytes% = 0 THEN GOTO getdata

t2 = TIMER                                                                                             ‘ End time, Have data
DeltaT = t2 - t1                                                                                   ‘ How long did it take?

duh:
tmp$ = "DeltaT= #.### Seconds Until LOC(2) > 0"                                          ‘ Various times arround 0.66 Sec
PRINT USING tmp$; DeltaT
PRINT "m$ Received= "; m$ + "&"                                                                ‘ Prints 10 spaces No Data
PRINT "endofdata= "; endofdata                                                                 ‘ endofdata=0, Never got "*"
PRINT "ctr= "; ctr, CHR$(13)                                                      ‘ Show number of times you pressed continue
CLOSE #2
ctr = ctr + 1
GOTO again

The comments show various responses discovered while debuging. 
HELP ANYONE
