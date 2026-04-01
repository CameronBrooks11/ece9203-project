E190_8JUL2017_J510W_Raven.csv
E190_8JUL2017_J510W_1_XTRA.csv or _XTRA_213.csv
E190_8JUL2017_J510W_EmulationData.csv
E190_9DEC2017_J570W_OnBoard.csv
E190_9DEC2017_J570W_Raven.csv
E190_9DEC2017_J150_OnBoard.csv
E190_9DEC2017_J150_Raven.csv


## extended

## Dataset Selection

Three datasets are used to cover development, validation, and robustness:

1. **38mm Prototype Rocket (J510W / J570W / J150)**
   - primary development dataset
   - multiple logger types (Raven, XTRA, onboard, emulation)
   - used for core implementation and main results

2. **Madcow Adventurer (Raven + GPS)**
   - validation dataset
   - includes GPS logs for independent reference
   - used to verify cross-rocket generalization

3. **LOC Precision Vulcanite (trimmed + GPS)**
   - robustness dataset
   - includes known data-quality issues and cleaned logs
   - used to test estimator stability on imperfect data

Source: :contentReference[oaicite:0]{index=0}