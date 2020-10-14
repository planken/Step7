Distributor FB v2.7 interface description
26Jul16


#Input#

*Lookup_DB* [BLOCK_DB]: DB block number of the lookup table. The lookup table is a collection of 32 integer pairs: equipment number (bin number) and corresponding encoder count. These values ought to be written as initial values and the DB ought to be write-protected in the PLC to ensure no values will be overwritten. This block is not organized as an array, as in Step 7 classic array elements cannot have initial values/comments.

*MoveEnable* [BOOL]: Enable distributor movement. FALSE: the contactors for forward and reverse rotation will not be energized, regardless of auto/manual. Use for locking a distributor or for maintenance. Keep TRUE for normal operation.

*Manual [BOOL]: TRUE: the manual controls can move or jog the motor forward/reverse. Keep FALSE for normal operation.

*EmergencyStop* [BOOL]: Additional condition for the permissive. If not desired, keep TRUE.

*HomeIndexProxy*: The index input, an external reference point. The proxy is mounted in a fixed location, detecting an index gap on a rotating disk. This software expects a positive value on the home index position.

*MotorStatusFwd* [BOOL]: Forward contactor status.

*MotorStatusRev* [BOOL]: Reverse contactor status.

*Faultreset* [BOOL]: Clear faults. Note that of the cause of the alarm is still present, the fault will occur again. For example, if a home index fault occurred this fault can be cleared but the distributor will enforce a SeekHome cycle.

*FM_STS_SET* [BOOL]: The Z pulse from the encoder via FM input 2. While initializing, the distributor will rotate clockwise until is has detected the Z input. On power up, the encoder count from the FM counter will be 0. While initializing, the encoder value increments (as the initialization uses forward motion only). As soon as the Z pulse from the encoder is detected by the FM counter, it again resets the counter value. Only then is the counter value valid.

*FM_count* [INT]: The counter value from the FM counter.

*Equip_SP* [INT]: The desired bin setpoint to be achieved. This value will be looked up in the lookup table to retrieve the associated counter value for that position.


#Output#

*EquipNbrCurrent* [INT]: Reported actual bin number. As soon as a new position is requested, this value will become 0. Once the position has been achieved, this value will reflect the desired position.

*PositionAchieved* [BOOL]: The distributor has achieved the desired position. The actual bin number is returned.

*MotorRunning* [BOOL]: Motor is running in either direction. I.e. either the forward or reverse contactor is energized. Can be used for HMI visualization.

*MotorRunFwd* [BOOL]: Coil for the forward contactor.

*MotorRunRev* [BOOL]: Coil for the reverse contactor.


#Input / output (alarms)#

*MotorStartFault* [BOOL]: Alarm: The forward / reverse contactor did not respond within the allotted time for UserConfig.MotorFaultTime. If no contactor feedback is desired, then the inputs MotorStatusFwd / MotorStatusRev could be fed with the contactor outputs. If made TRUE permanently, the motor will report "running" continuously. Depending on the control system wiring, this fault could also occur on a tripped overload. Correct the problem and issue a reset.

*PositioningFault* [BOOL]: Alarm: The desired position could not be achieved within the time for UserConfig.PositionFaultTime.

*HomeIndexFault* [BOOL]: Alarm: Only valid when the home index proxy is used (see the UserConfig.UseIndexProxy value). When the home index pulse is detected outside the specified range for forward / reverse (plus or minus the deadband value for searching) a home index fault is written. This could indicate an incorrect value for the home index in either forward / reverse direction (or both). Or it could mean an alignment issue with the encoder or proxy. The fault may be cleared but the desired setpoint will only be achieved with a properly detected home index. When this fault occurs, a calibration is the only solution.

*SelectionFault* [BOOL]: Alarm: The requested position could not be found in the lookup table. This could mean that either the lookup table has an incorrect or omitted value or the calling code did not perform proper boundary checks on this value. Either way, the distributor will not move. The fault can only be cleared by offering a valid setpoint (including 0).

*GeneralFault* [BOOL]: Alarm: Combined fault for use with an HMI (as often only one boolean can be used for the visibility of an object). This value is TRUE with one or more active faults.


#Configuration options#

Several options can be written to the instance datablock directly. This is done to reduce the complexity of the interface, as most of these values will likely not change often.

*UserConfig.UseIndexProxy* [BOOL]: (Initial value is TRUE.) Set this value to TRUE to use the home index proxy or to FALSE if no proxy is installed.

*UserConfig.NewBin_SeekHome* [BOOL]: (Initial value is TRUE): On a new bin selection, should the distributor seek home before positioning to the new setpoint? A seek home cycle only takes place in forward direction.

*UserConfig.First_scan* [BOOL]: Supply the PLC first scan bit if available (or detect power-up). It will initialize the FM counter. (See the FM_STS_SET input.)

*UserConfig.HomeCountFwd* / *UserConfig.HomeCountRev* [INT]: The encoder count at which the home index proxy is detected in forward / reverse motion. The proxy is detecting a gap far wider than the proxy face itself, so there will be slightly different values for forward and reverse motion.

*UserConfig.Deadband_Home* (initial value: 10): Deadband during seek home cycle.

*UserConfig.Deadband_Searching* (initial value: 10): Deadband during searching.

*UserConfig.Deadband_PosAchd* (initial value: 10): Deadband for once the position has been achieved. If desired, a more relaxed deadband can be used once the position has been achieved. However, it should be noted that typically this should be the same as UserConfig.Deadband_Searching. Once it is necessary to use a more relaxed value, it might be an indication something is mechanically amiss with the brake.

*UserConfig.PreActPreset* [INT] (initial value: 25): Preact value at which the motor will stop (and coast). Ideally, this should bring the spout on target but if needed it can be jogged from here. The preact value must exceed the deadband value for jogging to occur.

*UserConfig.CoastStopTime* [TIME] (initial value: 3s): A delay between rotation reversal to ensure the distributor motor has come to a complete stop. With a properly functioning brake the motor should not coast much (if at all) and this time could be small. With a properly functioning brake the default of 3s can be reduced.

*UserConfig.MotorFaultTime* [TIME] (initial value: 100ms): Response time from contactor. The initial value is more than adequate for NEMA contactors size 0-3.

*UserConfig.PositionFaultTime* [TIME] (initial value: 2min): Maximum time allowed to search and settle on a new position. If the position has not been achieved during this time, a position timeout fault occurs. This time should be at least twice the time for a full revolution. (If the distributor is positioned right after the index position and a bin is requested that lies right before the index, then it could take almost two revolutions to achieve the desired position.)

*UserConfig.PosAchdDly* [TIME] (initial value: 2s): Delay between position being achieved and position reported as being achieved to ensure no further movement takes place.

*UserConfig.ManualJogTime* [TIME] (initial value: 50ms): Duration for contactor being energized for forward or reverse. Typically, this should be short enough to allow for small movements and long enough to allow the motor to move the distributor.


#Calibration (optional)#

*Exec_home_cal*: Set this value to TRUE (using a one-shot) to initiate a calibration. Care should be taken that this is not executed during operations, as the distributor may immediately move to seek the home index and may introduce or update a bias to the encoder count value in order to bring the home index back to the specified value. A calibration only takes place in forward direction.

The calibration can be started with any setpoint. (No need to select "0" for "no bin" first.)

Once the calibration has been completed successfully, the distributor will immediately position to the desired equipment. (Because the desired target encoder count does not change.) If it is desired to not move the distributor after calibration, an equipment setpoint of 0 is required.
