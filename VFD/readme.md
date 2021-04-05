This works with the MicroMaster 420, 440, as well as the Sinamics G120. It may work with other drives, but I have not tested that. This FB has been serving me well for over 19 years. Use entirely at your own risk.

**Notes**

* This was originally for the Micromaster series, with parameter numbers up to 3999, using a single page selection bit to indicate the range. The Sinamics parameter numbers far exceed this and require a different page selection mechanism. That is not supported by this FB. So parameter R/W is limited to pages 0 and 1 (up to 3999).

* Version 3 and up: control word 1 (STW1) bits 13 and 14 (motorized potentiometer up/down) are repurposed for Drive Data Set selection.
