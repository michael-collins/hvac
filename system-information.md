# System Overview

**Primary control:** Each Ecobee (one per zone) decides what to call for (Heat / Cool / AUX / Fan / Dehumidify).

**BMPLUS-3000 zone panel:** Acts as a traffic director — routes those calls to the equipment and positions dampers.

**Equipment:** Rheem RP14 single-stage heat pump with single-stage propane furnace (aux heat).

**Dampers:** Each zone’s M1 = common, M4 = close, M6 = open.

**Dehumidifier:** GeneralAire unit triggered only by Zone 2 (basement) Ecobee’s humidity call through a relay.

## 1. Heating – Heat Pump Mode

**Trigger:** Either Ecobee calls for heat when outdoor conditions are above your Ecobee’s balance-point temperature.
Signals: Ecobee → Y1 (compressor), O (reversing valve = heat), G (fan).

### Sequence

Ecobee issues heat call.

**BMPLUS-3000:**

1. Opens the calling zone's damper(s).
2. Closes all other dampers.
3. Sends Y1 + O + G to the heat pump / air handler.
4. Heat pump energizes in heating mode.
5. Blower starts immediately (some units have a 5–10 s delay).

**When the thermostat is satisfied:**

1. Ecobee drops Y1; BMPLUS stops the call; blower runs ~90 s post-purge.
2. Dampers return to neutral / standby.

### Normal Delays & Protections

- **Compressor short-cycle delay:** 5 min built-in at the heat pump and sometimes in Ecobee.
- **Zone panel call overlap delay:** ~30 s stagger to prevent simultaneous zone calls.
- **Fan off-delay:** 60–90 s to clear coil heat.

## 2. Auxiliary Heat – Propane Furnace (Dual Fuel)

**Trigger:**

Ecobee decides outdoor temperature is below your Compressor Lockout or Aux Lock-in setpoint.

Or the heat pump ran too long without reaching setpoint.

**Signals:** Ecobee → W1 (heat call) only — no Y1/O.

### Sequence

1. Ecobee disables the compressor and energizes W1.
2. BMPLUS-3000 forwards W1 to the furnace control board.

**Furnace:**

1. Begins its ignition sequence (~30 s pre-purge, ignition, flame establish).
2. Controls its own blower delay (typically 30–45 s after burner ignition).

**When satisfied:**

1. Ecobee drops W1.
2. Furnace shuts gas, runs post-purge fan (60–90 s).
3. BMPLUS opens/closes dampers back to normal.

### Normal Delays & Protections

**Compressor lockout:** Ensures no simultaneous HP + furnace run.

Furnace time-based fan control: The BMPLUS lets the furnace handle blower timing.

**Short-cycle prevention:** Ecobee minimum on/off times (default 5 min).

## 3. Cooling – Heat Pump Mode

**Trigger:** Either Ecobee calls for cooling (indoor temp > setpoint).
Signals: Ecobee → Y1 (compressor), O (reversing valve = cool), G (fan).

### Sequence

1. Ecobee calls for cooling.
2. BMPLUS opens the calling zone’s damper(s), closes others.
3. Sends Y1 + O + G to the outdoor unit.
4. Heat pump runs in cooling mode.
5. Blower runs immediately.

**When satisfied:**

1. Ecobee drops Y1; BMPLUS releases the call.
2. Blower continues 60–90 s post-cool-off.
3. Dampers normalize.
4. Normal Delays & Protections
5. Compressor anti-short-cycle: 5 min typical.
6. Condensate / coil defrost safety: BMPLUS ignores brief drops in thermostat signal.

**Zone coordination delay:** ~30 s between zone calls.

## 4. Dehumidify – Basement Zone Only

**Trigger:** Zone 2 Ecobee detects humidity above setpoint (e.g., > 50%).
Signals: Ecobee ACC+ → relay coil → GeneralAire D1–D2 closed.

### Sequence

1. Ecobee sends 24 VAC on ACC+.
2. Relay (RIBU1C) energizes:
    - Closes D1–D2 on GeneralAire → dehumidifier runs.
    - Forces Zone 2 damper OPEN (M1→M6).
    - Forces Zone 1 damper CLOSED (M1→M4).
3. BMPLUS fan output energizes blower at low speed (G signal).
4. Dehumidifier pulls air from the return; exhausts dry air back into the same plenum.

**When humidity is satisfied:**

1. Ecobee drops ACC+; relay de-energizes; D1–D2 open → dehumidifier stops.
2. Dampers revert to normal zone control.
3. Blower runs its fan-off delay (30–60 s).

### Normal Delays & Protections

**Hysteresis:** Ecobee waits ~2 % RH below setpoint before dropping call (prevents short cycling).

Fan During Dehumidify: Set to OFF so blower runs only when the relay is active.

**Lockout:** Dehumidifier will not run during compressor defrost or aux-heat mode.

**Minimum off-time:** Usually 3 min internal to GeneralAire.

## 5. Common Timing Summary

| Function | Typical Delay / Protection | Who Controls It |
|----------|---------------------------|-----------------|
| Compressor restart | 5 min | Heat pump & Ecobee |
| Furnace pre-purge | 30 s | Furnace |
| Furnace blower start | 30–45 s | Furnace |
| Blower off delay | 60–90 s | Furnace or BMPLUS |
| Zone call spacing | 30 s stagger | BMPLUS |
| Dehumidifier hysteresis | ~2 % RH | Ecobee |
| Dehumidifier min-off | 3 min | GeneralAire |

## 6. What You’ll Hear / See in Operation Situation

### What You’ll Notice

Normal heat pump heat	Outdoor unit running, indoor blower on low speed; only one zone blowing air.
Aux heat (propane)	Outdoor unit silent, furnace firing after short delay; stronger airflow and warmer discharge.

Cooling	Outdoor unit running, cooler supply air; non-calling zone dampers closed (quiet).
Dehumidify	No outdoor unit; basement zone airflow only; subtle relay click near panel; GeneralAire hums.

Mode changes / power flicker	Ecobees may display “Calibrating” for ~1 min if panel browns out. Installing a small UPS + dedicated transformer eliminates this.

### Key Takeaways

- BMPLUS-3000 adds 30 s dampers-stagger and fan control, but Ecobee handles mode logic, lockouts, and delays.
- Only one zone damper opens at a time to protect airflow.
- Post-run fan periods are normal and help temperature balance.
- Dehumidify mode isolates Zone 2 — air movement limited to basement zone.
- Expect quiet pauses between stage transitions; the system is protecting itself.