# my-pat-config
My own pat configuration for radios I helped others configure. 

## FT991A
for an FT991A here are my lessons learned.

1. This command seems to work on debian 13 after installing PAT.  -- rigctld -v -r /dev/ttyUSB0 -m 1035 -s 38400 -t 4532
2. For Vara HF, ensure to check box "PTT Control," in configuration menu.
3. For Vara HF, also check box "Listen for inbound p2p Traffic."
