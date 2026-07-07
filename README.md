# my-pat-config
My own pat configuration for radios I helped others configure. This may help anyone else who needs a little technical assistance. 

## Setting up a service for each radio.
I setup a service following https://www.thuben.com/amateur/software/rigctl

Only difference is that I named my services rigctld-<radiomodel>.service.

## PAT - Installation notes
Pretty self explainatory, just follow their instructions for linux
https://github.com/la5nta/pat

No gotchas that I had from installation. 

### 991A = rigctld-991A.service
```
[Unit]
Description=rigctld Hamradio rig controller for FT-991A
After=syslog.target network.target
[Service]
Type=simple
ExecStart=/usr/bin/rigctld -m 1035 -r /dev/ttyUSB0 -t 4532 -s 38400
ExecReload=/bin/kill -HUP $MAINPID
RestartSec=60
Restart=always
User=rigctld
Group=rigctld
[Install]
WantedBy=multi-user.target
```

## Create a user to run as a service
```
$ sudo adduser <insert-user-here> --system --group --home /var/lib/rigctld
$ sudo adduser <insert-user-here> dialout
$ sudo usermod <insert-user-here> --expiredate 1
$ sudo systemctl daemon-reload
$ sudo systemctl enable rigctld.service
$ sudo systemctl start rigctld.service
```

## Connection Aliases

<img width="902" height="605" alt="Screenshot at 2026-07-06 20-40-02" src="https://github.com/user-attachments/assets/55db9682-755d-4360-a716-ce47d17294c4" />

## Setting up CRON (well kinda)
use https://crontab.guru/ to make life slightly easier.  I configured my PAT to poll every 30 minutes because band conditions suck.

I use this right now (07/06/2026) 30 * * * * 7100.9 varahf:4350

(Update - disabled for now, no sense in creating more noise filth)

## Links of Note
https://github.com/la5nta/pat/wiki
https://themodernham.com/setup-pat-winlink-on-raspberry-pi-with-rigcontrol/
https://github.com/la5nta/pat/issues/184
https://www.thuben.com/amateur/software/rigctl

## FT991A - Lessons Learned
for an FT991A here are my lessons learned.

1. This command seems to work on debian 13 after installing PAT.  -- rigctld -v -r /dev/ttyUSB0 -m 1035 -s 38400 -t 4532
2. For Vara HF, ensure to check box "PTT Control," in configuration menu.
3. For Vara HF, also check box "Listen for inbound p2p Traffic."
