# OpenROAD Physical Design Flow and STA Report

This report summarizes the physical-design flow and static timing analysis (STA) results for the `vsdbabysoc` design (sky130hd) using OpenROAD.

---

## 1. Physical-Design Flow

1. **RTL and constraints:** Read the Verilog sources and SDC timing constraints, then elaborate and synthesize the design.
2. **Floorplanning:** Define die/core dimensions, create rows and tracks, and plan macro locations.
3. **Macro placement:** Place hard macros and check their locations, halos, pins and routing access.
4. **Global placement:** Place standard cells across the core while balancing utilization, timing and congestion.
5. **Clock-tree synthesis (CTS):** Build the clock distribution network and insert clock buffers to control skew and latency.
6. **Global routing:** Estimate routing paths and report layer usage, demand, capacity and overflow.
7. **Detailed routing:** Connect nets on legal routing tracks and resolve design-rule constraints.
8. **STA and sign-off checks:** Review setup/hold slack, electrical checks and final reports across timing corners.

---

## 2. Results Summary

| Item | Observation |
|---|---|
| **Floorplan / layer view** | The OpenROAD GUI shows the routing layers (li1, mcon, met1-met5 and vias) on a dense layout canvas. Useful for checking the core view and layer visibility. |
| **Placement / cell-density view** | The cell pattern and heat-map colours show a placement/congestion visualization. These views help locate dense regions and areas that may need placement or floorplan adjustment. |
| **Global-routing congestion** | Total resource demand of 32,744 against 2,346,381 resources, about **1.40%** overall usage, with total overflow of **2**. |
| **Instance inspector** | A sequential cell (`sky130_fd_sc_hd__dfxtp_1`) has placement status **PLACED**. This shows the instance has a location; it is not by itself proof of routing or timing closure. |
| **Timing console** | The displayed path shows setup slack of **6.97** (units depend on the report configuration, typically ns) with status **MET**. |

---

## 3. Setup and Hold Timing Across Corners (STA Report)

| Corner | Setup slack (sample) | Hold slack (sample) |
|---|---|---|
| ff_100C_1v65 | 8.02 | 0.28 |
| ff | 8.87 | 0.22 |
| ff_n40C_1v56 | 6.78 | 0.32 |
| ff_n40C_1v65 | 7.49 | 0.27 |
| ff_n40C_1v76 | 8.11 | 0.24 |
| ff_n40C_1v95 | 8.81 | 0.21 |
| ss_100C_1v40 | **-2.91** | 0.92 |
| ss_100C_1v60 | 1.94 | 0.66 |
| ss_n40C_1v28 | **-28.72** | 1.72 |
| ss_n40C_1v35 | **-15.31** | 1.29 |
| ss_n40C_1v40 | **-9.76** | 1.08 |
| ss_n40C_1v44 | **-6.48** | 0.97 |
| ss_n40C_1v60 | 0.37 | 0.66 |
| ss_n40C_1v76 | 3.58 | 0.51 |
| tt_025C_1v80 | 6.94 | 0.34 |
| tt_100C_1v80 | 7.09 | 0.33 |

**Interpretation:**
- Negative setup slack indicates a setup-timing violation at that corner.
- Positive setup slack means the checked setup paths meet the requirement.
- Positive hold slack means the checked hold paths meet the requirement.
- All hold slacks are positive across every corner.
- The worst negative setup values occur in the **slow (ss) corners at low voltage** (for example `ss_n40C_1v28`, `ss_n40C_1v35`, `ss_n40C_1v40`), so these corners need attention.
- Fast (ff) and typical (tt) corners all meet setup with comfortable margin.

---

## 4. Screenshot Evidence

### Layer view / routing layout
The Display Control panel shows layers li1, mcon, met1, via, met2, via2, met3, via3, met4, via4 and met5 enabled over the routing canvas.







<img width="680" height="425" alt="WhatsApp Image 2026-10-10 at 11 21 21 PM" src="https://github.com/user-attachments/assets/e7c0cd81-f114-4df6-9335-aa050b3dd1f0" />

### Placement / density visualization
Cell density view of the `vsdbabysoc` design at the CTS stage, showing where standard cells are clustered.







<img width="474" height="839" alt="WhatsApp Image 2026-10-10 at 11 21 22 PM" src="https://github.com/user-attachments/assets/314b0dec-df7e-43bd-9f18-c8c4fd302186" />


### Global-routing congestion summary

```
[INFO GRT-0096] Final congestion report:
Layer     Resource     Demand    Usage (%)    Max H / Max V / Total Overflow
li1             10          0        0.00%        0 /  0 /  0
met1        808801      13523        1.67%        0 /  0 /  0
met2        698074      14036        2.01%        0 /  0 /  0
met3        517788       2723        0.53%        0 /  0 /  0
met4        249888       2451        0.98%        0 /  0 /  0
met5         71820         11        0.02%        1 /  0 /  2
Total      2346381      32744        1.40%        1 /  0 /  2
```







<img width="1050" height="275" alt="WhatsApp Image 2026-10-10 at 11 21 23 PM" src="https://github.com/user-attachments/assets/20e4291d-f805-44bc-9b6f-c7ddf0423eb5" />


Usage is low on every layer (highest is met2 at 2.01%). The only overflow is on met5 (2 in total), so routing congestion is minimal.

### Heat maps and display controls
The GUI offers heat maps for routing congestion, estimated congestion, IR drop, pin density, placement density and power density.







<img width="653" height="387" alt="WhatsApp Image 2026-10-10 at 11 21 25 PM (1)" src="https://github.com/user-attachments/assets/c6298a8c-55ee-4a15-81d6-baf70538048e" />

### Instance inspector / placement status
Selected instance: `core.CPU_Xreg_value_a4[2][27]$_SDFFE_PP0P_`, master `sky130_fd_sc_hd__dfxtp_1`, a sequential cell with placement status **PLACED**.








<img width="652" height="314" alt="WhatsApp Image 2026-10-10 at 11 21 27 PM" src="https://github.com/user-attachments/assets/465eb0d2-9e67-401c-87f0-ab5d323248f4" />


### Detailed routing-layer view
Zoomed-in view of the routed design showing the metal layers and a highlighted timing path in yellow.





<img width="916" height="422" alt="coresmalldiicassignment" src="https://github.com/user-attachments/assets/f39800a2-c75d-46fe-97cd-c40b561f4302" />


---

## 5. Conclusion

The `vsdbabysoc` design was taken through the OpenROAD flow from floorplanning to detailed routing and STA. Global routing shows very low congestion (1.40% usage, overflow of 2 on met5). Hold timing is met in all corners, and setup timing is met in the typical and fast corners. The slow, low-voltage corners show negative setup slack and are the main area for further timing optimization.
