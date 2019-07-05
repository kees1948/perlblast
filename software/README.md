* 'perlblast_nh' is a Perl implementation of 'Galblast' and is ment to be used with my CSGBLAST/CSGSOCK2 hardware.
* It switches all voltages on the programming socket to a low value when idle. (remain some 1 to 1.5V)
* Is designed to protect the parallel port hardware from dangerous voltages from the DUT in case of faults.
* Has shown, under Linux, that it also can be used with certain USB<->IEE1284 dongles. (Lucent USS720 chip compatible)
* Programming voltage VPP can be set from 12.0 to 17.0 Volt, which is adequate for almost all GAL types
* Supply voltage VCC can be set for 3V3 and 5V0 volt. perlblast_nh does this automatically.

* Needs an external 24VDC adapter for supply.
* On board voltage regulators.

