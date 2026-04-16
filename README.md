# Surgical Logic Hijacking: Binary Instrumentation & Reverse Engineering

## 🛡️ Project Overview
This repository documents a comprehensive security analysis and reverse engineering project focused on the **GoldSrc engine**. The objective was to transition from surface-level memory manipulation to deep-code surgical logic bypassing using a combination of **Dynamic** and **Static Analysis**.

The project culminates in a surgical bypass of the `CBasePlayer::TakeDamage` function, achieving player-specific invulnerability without impacting global entity mechanics.

## 🛠️ Toolset & Environment
- **Debugger:** x32dbg (Dynamic Analysis & Instruction Patching)
- **SRE Suite:** Ghidra (Static Analysis, Decompilation, & Vtable Reconstruction)
- **Environment:** Windows 10 (Virtualized via VMware for process isolation)
- **Target:** `hl.dll` (Core engine logic)

## 🔍 Methodology

### 1. Format String & Offset Discovery
Using Ghidra, I performed **Format String Identification** on HUD display functions. By tracing the string `"2 Health: %i2%%"`, I located the underlying entity structures. Cross-referencing the `CBaseEntity::SUB_Remove` function revealed that health is stored as a **32-bit normalized float** at **Offset 0x160**.

### 2. Identifying Global Logic Flaws
Initial dynamic analysis identified a global subtraction instruction (`FSUB`) at `hl.dll+18551`. While a **NOP** patch here achieved invulnerability, it triggered **Global God Mode**, rendering NPCs immortal due to shared logic paths.

### 3. Vtable Reconstruction & Surgical Bypass
To achieve a refined fix, I analyzed the **Virtual Function Table (Vtable)** for the `CBasePlayer` class. By mapping cross-references (XREFs), I isolated the player-exclusive damage handler at **`1005fa70`**. 

**The Fix:**
I implemented a function prologue hijack using:
- `MOV EAX, 1`: Signals successful hit registration to the engine.
- `RET 0x10`: Immediately exits the function and cleans the 16-byte stack frame.

## 🚀 Outcomes
- Successfully isolated player-specific health logic from the global entity pool.
- Validated the patch through real-time gameplay testing (invulnerability vs. grenades/NPCs while maintaining offensive capabilities).
- Documented the inherent risks of **Client-Side Authority** in networked/local binaries.

## 📺 Demonstration
The reports provides a thorough wakthorugh of the entire processs. 

---
**Disclaimer:** This project is for educational purposes only, focusing on software architecture and binary security analysis.
