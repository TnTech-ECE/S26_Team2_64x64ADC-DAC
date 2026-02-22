## Instructions: 
All .tcl files will need to be edited to change the path from USERNAME to your PC's username. \
\
In auto_email.tcl, enter put your gmail address in the fields at the top, generate an app password \
(on gmail account settings) and paste the generated password in the password field\
\
put Vivado_init.tcl in C:\Users\USERNAME\AppData\Roaming\Xilinx\Vivado \
\
Put the Vivado_notification_scripts folder in C:\Users\USERNAME\Documents\ 
## Errata: 
It may be possible to have an incorrect reported run time if a synthesis, implementation or bitstream run is interrupted. \
The email will still notify as soon as the compilation finishes, but stale timing files can get stuck. Completion of the script\
results in a full deletion of the timing files, so this error might only happen once. The reason for this is that it uses the oldest timing file on the HDD. \
\
The steps to likely cause this error are canceling a run with a synth/impl chain, resetting only one run and running to completion. 
