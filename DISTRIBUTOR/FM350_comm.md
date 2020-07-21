Example for config/comm with FM350-1:

// Module address

      L     416
      T     DBn.MOD_ADR             // DB specified in FM352-1 call DBn.DBW6


// Module Address in pointer format

      L     DBn.MOD_ADR             // DB specified in FM352-1 call DBn.DBW6
      ITD   
      SLD   3
      T     DBn.CH_ADR              // DB specified in FM352-1 call DBn.DBD6


// Transfer of user data lenght

      L     16
      T     DBn.U_D_LGTH            // DB specified in FM352-1 call DBn.DBD12


// Detect the encoder Z pulse

      A     DBn.STS_SYNC            // DB specified in FM352-1 call DBn.DBX43.5
      FP    "Encoder_Z_fp"
      =     "Enc_Z"                 // Encoder Z one-shot (use for FM_STS_SET input of distributor FB call)
      S     "Enc_reset_sync"        // Request to FM


// Wire the encoder Z signal to input I2 DI SET (pin 15)
// Interrupt to allow subsequent detection

      AN    "Encoder_Z"
      =     DBx.ENSET_UP            // DB specified in FM352-1 call DBn.DBX27.0
      =     DBx.ENSET_DN            // DB specified in FM352-1 call DBn.DBX27.1

// Call to FM350-1

      CALL  "FM350-1FC"
       DB_NO   :=DBn   // Communication DB
       SW_GATE :=
       GATE_STP:=
       OT_ERR_A:=
       OT_ERR  :=
       L_DIRECT:=
       L_PREPAR:=
       T_CMP_V1:=
       T_CMP_V2:=
       RES_SYNC:="Enc_reset_sync"
       RES_ZERO:=

// Configure the FB call

// +++ Enable the home index proxy +++

// This is enabled by default, so if this distributor is not equipped with one
// it must be disabled here.

      SET   
      =     "Distr iDB".UserConfig.UseHomeIndexProxy


// -------------------------------------
// Seek home on each new bin selection
// -------------------------------------

      SET   
      =     "Distr iDB".UserConfig.NewBin_SeekHome


// -------------------------------------
// Seek home on each new bin selection
// -------------------------------------

      L     -93
      T     "Distr iDB".Count_bias


// -------------------------------------
// Home pulse encoder counts
// -------------------------------------

// Home pulse encouder count for forward and reverse motion.

// Note: These values are irrelevant if the home proxy is not enabled.

      L     2519
      T     "Distr iDB".UserConfig.HomeCountFwd

      L     72
      T     "Distr iDB".UserConfig.HomeCountRev


// -------------------------------------
// Deadband value while searching & once position achieved
// -------------------------------------

// Optionally, a different value can be supplied for once the position has been
// achieved

      L     6
      T     "Distr iDB".UserConfig.Deadband_Searching
      T     "Distr iDB".UserConfig.Deadband_PosAchd


// -------------------------------------
// Preact value at which jogging will occur
// -------------------------------------

// This value must exceed the deadband for jogging to function properly.

      L     24                          //8
      T     "Distr iDB".UserConfig.PreActPreset


// -------------------------------------
// Motor jog time
// -------------------------------------

      L     T#10MS
      T     "Distr iDB".UserConfig.ManualJogTime
