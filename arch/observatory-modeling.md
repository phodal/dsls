## observatory software modeling

### End-to-end observatory software modeling using domain specific languages

Refs: [https://www.gmto.org/SPIE_2014/2014-SPIE-9152-58.pdf](https://www.gmto.org/SPIE_2014/2014-SPIE-9152-58.pdf)

### Component modeling

```
MKlass "Monitor",
 extends: ["Port"]
 abstract: false
 desc: "Monitors are the basic element for component telemetry.
       Monitors don't produce any direct action on the associated component"
 features:
     units: {kind: "reference", lower: 1, upper: 1, type: "UnitType",
        desc: "Units of the monitor"}
     type: {kind: "attribute", lower: 1, upper: 1, type: "BaseDataType",
        desc: "Type of the Monitor"}
     min: {kind: "attribute", lower:-1, upper: 1, type: "ValueType",
        desc: "Minimum value used for monitoring purposes"}
     max: {kind: "attribute", lower:-1, upper: 1, type: "ValueType",
        desc: "Maximum value used for monitoring purposes"}
     rate: {kind: "attribute", lower: 1, upper: 1, type: "Integer",
        desc: "Sampling rate, if zero, only on change"}
     storage: {kind: "attribute", lower: 1, upper: 1, type: "Integer",
        desc: "Decimation factor, if zero, no storage, if > 1 all samples are stored"}
```

### Device control modeling

```
Controller "BasePositionController"
 extends: ["BaseController"]
     data_outputs:
         position_actual_value:
         desc: "This output shall provide the actual value of the position
                measurement device. The value shall be given in user-defined
                position units."
         type: "float32"
         pattern: "pull"
         max_rate: 20
         default: 0

         position_internal_demand:
         desc: "This output shall indicate the internal position demand
                calculated by the internal trajectory generator that the drive
                should move to in position profile mode using the
                current settings of motion control properties such as velocity,
                acceleration, deceleration, motion profile type etc.
                The value of this input shall be interpreted as absolute
                or relative depending on the state of the property abs/rel'.
                It shall be given in user-defined position units."
         type: "float32"
         pattern: "pull"
         max_rate: 20
         default: 0
```

### Data processing modeling

```
KeywordDictionary "FITSDictionary",
 desc: "Keywords for a basic FITS standard header. In many cases,
         the keywords and descriptions are taken verbatim from
        'Definition of the Flexible Image Transport System (FITS),
        Version 3.0'."
 extends: []
 max_length: 8
 keywords:
     datamax:
         tags: ["fits_standard", "gen_primary", "data_value"]
         keyword: "DATAMAX"
             synopsis: "Maximum data value"
             index: "F"
             default: ""
             type: "Float"
             hdu: "any"
             desc: "The value field shall always contain a floating point
                     number, regardless of the value of BITPIX. This number
                    shall give the maximum valid physical value represented
                    by the array, exclusive of any special values."
```

### Observatory Operations modeling


```
GMTIFS_Observation "30DORField",
 notes: ""
 instrument: "gmtifs"
 obs_cond:
    image_quality: "20%/Best"
    cloud_cover: "20%/Best"
    wind_velocity: "20%/Best"
    sky_brightness: ""
 inst_conf:
     inst_rot_mode: "tracking"
      inst_PA: 135
      dichroic: "grating" #same as grating
      image_filter_wheel: "J"
      image_utility_wheel: "clear"
      imager_det_focus: "filter" #same as filter
      ifs_mask_wheel: "f-converter" #same as f-converter
      ifs_f_converter_wheel: "50"
      ifs_filter_wheel: "grating" #same as grating
      ifs_grating_wheel: "hK"
      ifs_detector_focus: "grating" #same as grating
  sets:
      30_DOR:
      desc: "30_DOR Observation Set"
      blocks:
      pre_std_obsq:
      desc: "Pre Standard Star Observation Sequence"
     ao_conf:
      mode: "LTAO+OIWFS"
     rate: "1000"
      targets:
      base: {ra: "05:38:44.86", dec: "-69:05:44.2"}
      science: {ra: "05:38:44.86", dec: "-69:05:44.2"}
      agwfs3: {ra: "05:38:43.40", dec: "-68:59:66.8"}
      pcwfs : {ra: "05:39:25.75", dec: "-69:11:36.0"}
      oiwfs: {ra: "05:38:48.48", dec: "-69:05:32.6"}
      acquisition:
      exp_time: 5.0
     coadds: 1
     mode: "bright_object_spectroscopy"
     pause: true # Center target in IFS field
      exposure:
      det_conf:
      exp_time: 20.0
     coadds: 10
     mode: "bright_object_spectroscopy"
      offsets: [
      { p: 0.0, q: 1.1, repeat: 1 }
      { p: 0.0, q: -1.1, repeat: 1 }
      { p: 10.0, q: 0.0, repeat: 1 }
      { p: -10.0, q: 0.0, repeat: 1 }
      { p: 0.0, q: 1.1, repeat: 1 }
      { p: 0.0, q: -1.1, repeat: 1 }
      ]
```

### Work system modeling

