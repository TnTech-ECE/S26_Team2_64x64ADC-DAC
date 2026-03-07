
Planning on using UCD9081

*each line item is a FS1006-3300
---------------------------------------
Assume 3v3 operation; VCCO_VADJ 6A

FPGA_3v3 = 6A


--------------------------------------
*each line item is a FS1412
----------------------------------------------------

VCCINT + VCCBRAM = 13A, may be possible with FS1412 (12A) (1.0V)*

FPGA1v5 

FPGA 1v8 (HDMI,PHY, UART) + VCCAUX
 

------------------------------------------------------
We will power these off the 1v5 supply just like AC701
*each line item is a TPS51200
--------------------------------

VTTDDR powered by 0.75v @ 3A (provides VTTVref, no current spec)

DDR3 VTERM R 0v75 0.75 @3A

------------------------------
