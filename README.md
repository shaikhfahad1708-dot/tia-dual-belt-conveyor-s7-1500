# TIA Portal V19 Dual-Belt Conveyor Control & KTP700 HMI

SCL control logic for a dual-belt conveyor system on a Siemens **S7-1500 (CPU 1511-1 PN)** paired with a **KTP700 Basic PN** HMI screen, simulated with **S7-PLCSIM V19** and **WinCC RT Simulator**.

## Hardware & Software Stack
- **Engineering Tool:** Siemens TIA Portal V19 (SCL & WinCC Basic)
- **PLC:** SIMATIC S7-1500 (`CPU 1511-1 PN`)
- **HMI:** SIMATIC HMI `KTP700 Basic PN` (`Root screen`)
- **Simulation:** `S7-PLCSIM V19` + WinCC Runtime Simulator

## Control Logic (`Block_1 [FB1]` / `dual_belt_conveyor.scl`)
- **Safety & Fault Clearing:** Drops `#auto_latched` and `#fault_latched` when `NOT #safety_ok`.
- **Start Latch:** Latches `#auto_latched := TRUE` when `#safety_ok`, `NOT #fault_latched`, and `#btn_start` are active.
- **Stop Unlatch:** Clears `#auto_latched := FALSE` when `#btn_stop` is pressed.
- **Exit Jam Fault:** Latches `#fault_latched := TRUE` and unlatches `#auto_latched := FALSE` if `#auto_latched AND #exit_jam_Sensor`.
- **Fault Reset:** Clears `#fault_latched := FALSE` when `#btn_reset AND NOT #exit_jam_Sensor`.
- **Outputs:**
  - `discharge_motor`: `#safety_ok AND #auto_latched AND NOT #fault_latched`
  - `infeed_motor`: `#discharge_motor AND NOT #exit_jam_Sensor`
  - `jam_fault`: `#fault_latched`

## HMI (`Root screen`)
- **Momentary Control Buttons** (`Press` -> `SetBit`, `Release` -> `ResetBit` on `Block_1_DB`): `btn_start`, `btn_stop`, `btn_reset`
- **Safety Control:** `safety_ok`
- **Status Lamps** (Range-based Appearance animation): `infeed_motor` (grey/green), `discharge_motor` (grey/green), `jam_fault` (grey/red)
