
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

You will also need John's HTML files.  download them in linbpq folder under a folder "HTML"
```
mkdir HTML
cd HTML
wget http://www.cantab.net/users/john.wiseman/Downloads/Beta/HTMLPages.zip
unzip HTMLPages.zip
```

Now after that is completed, create and insert a bpq32.cfg file. This will not exist in that folder. 
```
touch bpq32.cfg
```

Next, create a user to support running bpq as a service. 

```
$ sudo adduser <insert-user-here> --system --group --home /var/lib/linbpq
$ sudo adduser <insert-user-here> dialout
$ sudo usermod <insert-user-here> --expiredate 1
$ sudo systemctl daemon-reload
$ sudo systemctl enable bpq.service
$ sudo systemctl start bpq.service
```

Here is my configuration file that I am using up to this point. This took a culmination of certain pages to build out.  These are noted below for reference. 

REFERENCES: 
- https://www.cantab.net/users/john.wiseman/Documents/MailServer.html
- https://www.cantab.net/users/john.wiseman/Downloads/installWebMail
- 

```
;
;
;	CONFIGURATION FILE FOR G8BPQ BPQ32 SOFTWARE
;	The order of parameters in not important, but they
;	all must be specified - there are no defaults
;
;

;THIS IS A SYSOP PASSWORD
PASSWORD=********
;LOCATOR ENABLES MAP REPORTING
LOCATOR=FN13UB
MAPCOMMENT=GEES CNY MAIL NODE, CAMILLUS NY.
;USES PRECANNED DEFAULTS THAT ARE "RESONABLE"
SIMPLE
;THIS IS THE NODE CALLSIGN, EFFECTIVELY THIS IS
;WHAT THE ENTIRETY OF THE NODE IS CALLED
NODECALL=KD2KUB-5
NODEALIAS=GEE
IDMSG:
KD2KUB
***
INFOMSG:
Welcome to KD2KUB CNY MAIL NODE
-direct to local post office(LPO) = kd2kub-10
-direct to internet primary, fallback LPO = kd2kub-5
***
CTEXT:
Welcome to KD2KUB CNY MAIL NODE
-direct to local post office(LPO) = kd2kub-10
-direct to internet primary, fallback LPO = kd2kub-5
***
FULL_CTEXT=1		; SEND CTEXT TO EVERYBODY
HFCTEXT=KD2KUB CNY MAIL NODE
;CONTROLS PROCESSING OF LINKED COMMANDS
;Y-ALLOWS UNRESTRICTED USE
;A-ALLOWS APPLICATION PROGRAM USE
;N-DISABLED 
BTEXT:
KD2KUB CNY MAIL NODE
***
BINTERVAL=10
ENABLE_LINKED=Y
;ALLOWS USE OF BBS SOFTWARE
BBS=1
;ENABLES NODE CONTROL AND ALLOWS USERS TO CONNECT
NODE=1

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;THIS IS THE TELNET NODE. THIS IS USED TO 
;CONTROL ALL THINGS INTERNET AS WELL AS CMS
;ACTIVITY AND SYSOP MANAGEMENT
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
PORT
 PORTNUM=1
 ID=TELNET
 DRIVER=TELNET
 QUALITY=200
 ;CONFIGURATION SECTION
 CONFIG
 CMS=1
 CMSCALL=KD2KUB
 CMSPASS=**********
 LOGGING=1
 DISCONNECTONCLOSE=1
 TCPPORT=8010
 IPV4=1
 HTTPPORT=8080
 LOGINPROMPT=USERNAME:
 PASSWORDPROMPT=PASSWORD:
 MAXSESSIONS=10
 CTEXT=Welcome to GEEs linbpq telnet server.
 USER=KD2KUB,*********,kd2kub,"",sysop
 ;THIS ALLOWS SYSTEM TO FALL BACK TO BBS / RMS RELAY IF NO INTERNET. 
 FALLBACKTORELAY=1
 ;THIS LINE BELOW IS USED TO CONNECT TO RMS RELAY
 ; for some reason, this is needed to connect to local BBS.
 RELAYHOST=127.0.0.1
 ;THIS LINE BELOW IS USED TO CONNECT TO BBS.
 ;USE THIS BY DEFAULT
 RELAYAPPL=BBS
ENDPORT

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;THIS PORT IS USED TO CONNECT VIA VARA
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
PORT
 PORTNUM=2
 ID=VARA 80M AND 40M
 DRIVER=VARA
 ;CONFIGURATION SECTION
 CONFIG
 ADDR 127.0.0.1 8300 PTT HAMLIB
 ;THIS LINE IS IMPORTANT. VARA WILL LISTEN FOR ALL THESE
 ;CALLSIGNS. 
 MYCALLS KD2KUB-5 KD2KUB-10
 BW2300
 BUSYWAIT 60
 SESSIONTIMELIMIT 120
;https://www.cantab.net/users/john.wiseman/Documents/RigControl.html
 RIGCONTROL
  HAMLIB 127.0.0.1:4532
   10,3.5904,"DIG",VW,3200
   10,7.1009,"DIG",VW,3200
****
;https://www.cantab.net/users/john.wiseman/Documents/WL2KReporting.html
;https://www.cantab.net/users/john.wiseman/Documents/LinBPQ_RMSGateway.html
 WL2KREPORT PUBLIC, api.winlink.org, 80, KD2KUB-5, FN13UB, 00-23, 3570000, VARA, 30, 20, 5, 0
 WL2KREPORT PUBLIC, api.winlink.org, 80, KD2KUB-5, FN13UB, 00-23, 7097000, VARA, 30, 20, 5, 0
 WL2KREPORT EMCOMM, api.winlink.org, 80, KD2KUB-10, FN13UB, 00-23, 3594400, VARA, 30, 20, 5, 0
 WL2KREPORT EMCOMM, api.winlink.org, 80, KD2KUB-10, FN13UB, 00-23, 7105900, VARA, 30, 20, 5, 0
ENDPORT

;;UNCOMMENT BELOW LINE IF USING ACTIVE INTERNET FORWARD
APPLICATION 1,RMS,C 1 CMS,KD2KUB-5,BPQRMS,0
;THIS BELOW APPLICATION IS USED FOR SENDING MESSAGES TO RMS RELAY
;APPLICATION 1,RELAY,C 1 RELAY,KD2KUB-10,BPQRMS,0
;THIS APPLICATION LINE IS USED TO SEND MESSAGES TO BBS ON THIS SYSTEM
;INSTEAD OF RMS RELAY
APPLICATION 2,BBS,,KD2KUB-10,BPQBBS,0
LINMAIL
```
In this mix as well is another configuration called linmail.cfg

```
main :{
  Streams = 10;
  BBSApplNum = 2;
  BBSName = "KD2KUB";
  SYSOPCall = "KD2KUB";
  H-Route = "";
  AMPRDomain = "";
  EnableUI = 0;
  RefuseBulls = 0;
  OnlyKnown = 0;
  reportMailEvents = 0;
  SendSYStoSYSOPCall = 0;
  SendBBStoSYSOPCall = 0;
  DontHoldNewUsers = 0;
  DefaultNoWINLINK = 1;
  AllowAnon = 1;
  DontNeedHomeBBS = 1;
  DontCheckFromCall = 0;
  UserCantKillT = 0;
  ForwardToMe = 0;
  SMTPPort = 0;
  POP3Port = 0;
  NNTPPort = 0;
  RemoteEmail = 0;
  SendAMPRDirect = 0;
  MailForInterval = 0;
  MailForText = "";
  AuthenticateSMTP = 0;
  MulticastRX = 0;
  SMTPGatewayEnabled = 0;
  ISPSMTPPort = 0;
  ISPPOP3Port = 0;
  POP3PollingInterval = 0;
  MyDomain = "";
  ISPSMTPName = "";
  ISPEHLOName = "";
  ISPPOP3Name = "";
  ISPAccountName = "";
  ISPAccountPass = "D149210DFCBBD14F031E433E552D6AE6";
  Log_BBS = 1;
  Log_TCP = 1;
  Version = "6,0,25,41";
  WelcomeMsg = "Hello $I. Latest Message is $L, Last listed is $Z\r\n";
  NewUserWelcomeMsg = "Hello $I. Latest Message is $L, Last listed is $Z\r\n";
  ExpertWelcomeMsg = "\r\n";
  Prompt = "de KD2KUB>\r\n";
  NewUserPrompt = "de KD2KUB>\r\n";
  ExpertPrompt = "de KD2KUB>\r\n";
  SignoffMsg = "";
  RejFrom = "";
  RejTo = "";
  RejAt = "";
  RejBID = "";
  HoldFrom = "";
  HoldTo = "";
  HoldAt = "";
  HoldBID = "";
  FBBFilters = "";
  SendWP = 0;
  SendWPType = 0;
  FilterWPBulls = 0;
  NoWPGuesses = 0;
  SendWPTO = "";
  SendWPVIA = "";
  SendWPAddrs = "";
  MaxTXSize = 99999;
  MaxRXSize = 99999;
  ReaddressLocal = 0;
  ReaddressReceived = 0;
  WarnNoRoute = 1;
  Localtime = 0;
  SendPtoMultiple = 0;
  FOURCHARCONT = 0;
  FWDAliases = "ONO-EC|ONO-FAIR";
};
BBSForwarding :
{
  K2VTT :
  {
    TOCalls = "";
    ConnectScript = "";
    ATCalls = "";
    HRoutes = "";
    HRoutesP = "";
    FWDTimes = "";
    Enabled = 0;
    RequestReverse = 0;
    AllowBlocked = 1;
    AllowCompressed = 1;
    UseB1Protocol = 0;
    UseB2Protocol = 1;
    SendCTRLZ = 0;
    FWDPersonalsOnly = 0;
    FWDNewImmediately = 0;
    FwdInterval = 3600;
    RevFWDInterval = 0;
    MaxFBBBlock = 10000;
    ConTimeout = 120;
    BBSHA = "";
  };
};
Housekeeping :
{
  LastHouseKeepingTime = 0L;
  LastTrafficTime = 0L;
  MaxMsgno = 60000;
  BidLifetime = 60;
  MaxAge = 30;
  LogLifetime = 7;
  MaintInterval = 24;
  UserLifetime = 0;
  MaintTime = 0;
  PR = 0.0;
  PUR = 0.0;
  PF = 0.0;
  PNF = 0.0;
  BF = 30;
  BNF = 30;
  NTSD = 30;
  NTSF = 30;
  NTSU = 30;
  DeletetoRecycleBin = 0;
  SuppressMaintEmail = 0;
  MaintSaveReg = 0;
  OverrideUnsent = 0;
  SendNonDeliveryMsgs = 1;
  GenerateTrafficReport = 1;
  LTFROM = "";
  LTTO = "";
  LTAT = "";
};
UIPort1 :
{
  Enabled = 0;
  SendMF = 0;
  SendHDDR = 0;
  SendNull = 0;
};
UIPort2 :
{
  Enabled = 0;
  SendMF = 0;
  SendHDDR = 0;
  SendNull = 0;
};
BBSUsers :
{
  KD2KUB = "^^^^************^^************^0^24^0^1^1^0^0^,,,,,,,8,,,,,,,,,,,,,,,,,,^,,,,,,,,,,,,,,,,,,,,,,,,,";
  K2VTT = "John^^^^^^^0^65536^0^1^1^0^1790712859^4,,,4,,,,1,,,,,,,,,,,,32,,,,14,,^,,,,,,,,,,,,,,,,,,,,,,,,,";
};

```
Now, with vara, install that to run it as wine and then use this script to set up a service to start up vara and linbpq
```
#!/bin/bash

/opt/cxoffice/bin/wine --bottle Winlink --cx-app VARA.exe &
sleep 10
./linbpq

```
Next, go to my main landing page and grab/create a rigctld service to support rig control.
https://github.com/kd2kub/my-pat-config/tree/main

From there, that should get you started.  I will be updating more as I build out my system. 

