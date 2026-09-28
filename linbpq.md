
This page is to guide others where John Wiseman has left us off.  I love BPQ's conceptual practice of command like support for winlink CMS and mail handling.  Unfortunately documentation leaves alot to be desired and needs quite a bit of work.  It seems amateur radio community leaves bpq a damn secret to setup and it is a damn shame. 

What my guide here hopes to do is show my own configuration, In a step by step format to recreate on a debian 13 system [at this time].

First thing is first, we need to install the software.  Now, what I did was build out linbpq per John's instructions. I did run into an issue with libbacktrace even though I installed it, it was not found by compiler.  I did run it in admin mode and it did build out an executable.  That executable does work.

REFERENCE: https://www.cantab.net/users/john.wiseman/Documents/buildinglinbpqfromsource.html

```sudo apt install build-essential git libminiupnpc-dev libconfig-dev libpcap-dev zlib1g-dev  libcap2-bin libjansson-dev libpaho-mqtt-dev libbacktrace-dev
```

Then run (optional and if needed) a fresh buildout of libbacktrace. 

```git clone https://github.com/ianlancetaylor/libbacktrace.git
cd libbacktrace 
mkdir build 
cd build 
../configure 
make
sudo make install
```

Then download and run source of linbpq, compile and stage in a working folder on your local user folder. This is where you will run into that issue with libbacktrace.

```mkdir git/linbpq
git clone https://github.com/g8bpq/linbpq.git
cd linbpq
make nomqtt
mkdir ~/linbpq
mv linbpq ~/linbpq
```

Now after that is completed, create and insert a bpq32.cfg file. This will not exist in that folder. 
```
touch bpq32.cfg
```

Here is my configuration file that I am using up to this point. 

```
;
;
;	CONFIGURATION FILE FOR G8BPQ SWITCH SOFTWARE
;
;	The order of parameters in not important, but they
;	all must be specified - there are no defaults
;
PASSWORD=<REDACTED>	  	 ; SYSOP Passord

LOCATOR=FN13UB		; Enable Map Reporting
MAPCOMMENT=GEES CNY MAIL NODE,CAMILLUS NY.
SIMPLE;
;
;
;	BBS enables the Application support system. If you have specified any of the APPLnCALLS,
;	you should set BBS to 1.
;
BBS=1		; INCLUDE BBS SUPPORT
;
;	NODE
NODE=1		; INCLUDE SWITCH SUPPORT



; The NODES and ROUTES tables can be saved, so that they can be reloaded when the software is restarted,
; rather than having to wait for the tables to be rebuilt. There is a program SAVENODES.exe and a command
; to the BPQ32 console to to this. By Setting AUTOSAVE=1, the tables will be saved each time the softare closes

AUTOSAVE=1		; Save Nodes File before exiting

;
;	Station Identification.
;
;	If a user connects to the NODE Callsign or Alias, he is linked
;	to the switch code, and can use normal NetRom/TheNet commands
;
;	If he connects to an Application Callsign or Alias he will be connected
;	directly to the corresponding application. If not available, the connect will
;	be rejected. See the section on Application Calls towards the bottom of the file for
;	more information.
;
;	Note that for compatibility with the DOS version, and older versions of BPQ32, BBSCALL is an alias for APPL1CALL,
;	and BBSALIAS is an alias for APPL1ALIAS. If both BBSCALL and APPL1CALL are specified, the BBSCALL will be ignored.	
;

NODECALL=KD2KUB	; NODE CALLSIGN
NODEALIAS=GEE

;	'ID' MESSAGE - SENT EVERY IDINTERVAL MINS
;
;	WILL BE ADDRESSED FROM THE PORT CALLSIGN (IF DEFINED)
;	     ELSE FROM THE NODE CALL
;
;	The main purpose of this is to satisfy the requrements of those administations that require a regular station 
;	identification in the same mode as used for communication. 

IDMSG:
KD2KUB
***
;

;	'I' COMMAND TEXT
;
;
INFOMSG:
GEES CNY MAILBOX NODE C/O KD2KUB
***

; BTEXT is the default beacon sent by the Node. Note that application programs may change this, or
; generate their own beacons.

; An APRS compatible position may be included. 

BTEXT:
={BPQ32}
***

IDINTERVAL=10		; 'ID' BROADCAST INTERVAL (UK Regs require an AX25 ID every 15 mins)
BTINTERVAL=15		; BTEXT is sent at this interval

;
;	CTEXT - Normally will only be sent when someone connects to 
;	the NODE ALIAS at level 2. If FULL_CTEXT is set to 1, it 
;	will be sent to all connectees. Note that this could confuse BBS
;	forwarding connect scripts. 
;
CTEXT:
Welcome to KD2KUB'S "GEE" CNY MAIL NODE
***

FULL_CTEXT=1		; SEND CTEXT TO EVERYBODY

HFCTEXT=KD2KUB'S "GEE" LINBPQ CNY MAIL NODE, 

;	Network System Parameters. 
;
;	These are my values. Many other node sysops use other values. If in doubt, liase with
;	those running nodes that you link to

OBSINIT=5		; INITIAL OBSOLESCENCE VALUE
OBSMIN=4		; MINIMUM TO BROADCAST
NODESINTERVAL=60		; 'NODES' INTERVAL IN MINS

L3TIMETOLIVE=25		; MAX L3 HOPS
L4RETRIES=4;		; LEVEL 4 RETRY COUNT
;
L4TIMEOUT=60;		; LEVEL 4 TIMEOUT
L4DELAY=10		; LEVEL 4 DELAYED ACK TIMER
L4WINDOW=4		; DEFAULT LEVEL 4 WINDOW
;
MINQUAL=140		; MINIMUM QUALITY TO ADD TO NODES TABLE	



;	The following MAX params set the limits for various tables. 
;
;	Although significantly larger values can be used, a common area is used
;	for these tables and the buffer pool, so don't increase them more than 
;	necessary.

MAXLINKS=100		; MAX LEVEL 2 LINKS (UP,DOWN AND INTERNODE)
MAXNODES=300; 		; MAX NODES IN SYSTEM
MAXROUTES=30		; MAX ADJACENT NODES
MAXCIRCUITS=150		; NUMBER OF L4 CIRCUITS

	

BUFFERS=999		; PACKET BUFFERS - 999 MEANS ALLOCATE AS MANY
				; AS POSSIBLE - NORMALLY ABOUT 600, DEPENDING
				; ON OTHER TABLE SIZES
;
;	TNC DEFAULT PARAMS
;
PACLEN=236		; MAX PACKET SIZE
;
;	PACLEN is a problem! The ideal size depends on the link(s) over
;	which a packet will be sent. For a session involving another node,
;	we have no idea what is at the far end. Ideally each node should have
;	the capability to combine and then refragment messages to suit each
;	link segment - maybe when there are more of my nodes about than 'real'
;	ones, i'll do it. When the node is accessed directly, things are a
;	bit easier, as we know at least something about the link.
;	So there are two PACLEN params, one here and
;	one in the PORTS section. This one is used to set the initial value
;	for sessions via other nodes, and for sessions initiated from here.
;	The other is used for incoming direct (Level 2)	sessions. In all cases
;	the Node PACLEN command can be used to override the defaults.
;
;	236 is the largest that can be sent over a NETROM link without fragmetation.
;	so don't go above this unless you don't have ant NETROM links.
;
;	Level 2 Parameters
;
; 	Most Level 2 parametes are specified in the PORTS section'
;
T3=180	 	   	 	; LINK VALIDATION TIMER (3 MINS)
IDLETIME=900		; IDLE LINK SHUTDOWN TIMER (15 MINS)	
;
;
HIDENODES=0		; IF SET TO 1, NODES STARTING WITH # WILL
				; ONLY BE DISPLAYED BY A NODES * COMMAND
;
;	THE *** LINKED COMMAND IS INTENDED FOR USE BY GATEWAY SOFTWARE, AND
;	CONCERN HAS BEEN EXPRESSED THAT IT COULD BE MISUSED. I RECOMMEND THAT
;	IT IS DISABLED IF NOT NEEDED.
;
ENABLE_LINKED=A		; CONTROLS PROCESSING OF *** LINKED COMMAND
				; Y ALLOWS UNRESTRICTED USE
				; A ALLOWS USE BY APPLICATION PROGRAM
				; N (OR ANY OTHER VALUE) DISABLE
ROUTES:
***

APPLICATION 1,RMS,C 1 CMS,KD2KUB-10
APPLICATION 2,BBS,,KD2KUB

PORT
PORTNUM=2
ID=TELNET
DRIVER=TELNET
QUALITY=0
CONFIG
CMS=1
CMSCALL=KD2KUB
CMSPASS=<REDACTED>
LOGGING=1
DISCONNECTONCLOSE=0
SECURETELNET=1
LOGGING=1
TCPPORT=8010
FBBPORT=8011
IPV4=1
HTTPPORT=8080
LOGINPROMPT=USERNAME:
PASSWORDPROMPT=PASSWORD:
MAXSESSIONS=2
CLOSEONDISCONNECT=1
CTEXT=Welcome to GEE's linbpq telnet server.
USER=KD2KUB,<REDACTED>,kd2kub,"",sysop
ENDPORT

PORT
 PORTNUM=1
 ID=VARA
 DRIVER=VARA
 CONFIG
 ADDR 127.0.0.1 8300 PTT HAMLIB 
 BW2300
 BUSYWAIT 15
 SESSIONTIMELIMIT 40
; RIGCONTROL
;  HAMLIB 127.0.0.1:4532
;   10,3.5944,"DIG",VW,3200
;   10,7.1009,"DIG",VW,3200
****
ENDPORT

LINMAIL
```
