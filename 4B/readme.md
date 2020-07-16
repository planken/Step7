Core networks:

**Convert peripheral start address**

The hardware must be configured to form 242 consecutive bytes that we can read out here. The composition of the slots in the hardware configuration is not relevant.


**Activity counter - signal timeout**

The activity counter value will increment each time the F500 receives new data from the T500. At 255 the value rolls over to 0. Delay between updates may be up to 5 s. The manual mentions a 15 s timeout value.


**Read status bytes**

Core network, where the 160 sensor bytes are being read.
