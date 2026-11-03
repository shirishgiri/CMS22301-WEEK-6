# CMS22301 Week 6: 40 rooms, 30 seconds a day

Individual assignment for Intelligent Systems and Emerging Technologies.
It designs a LoRaWAN temperature system for 40 meeting rooms under The Things Network fair use limits (30 s of airtime and 10 downlinks a day per sensor).

## What is in this repository

| File | What it is |
|---|---|
| `Week6_Assignment_ALL_IN_ONE.ipynb` | The completed notebook (Tasks A to G): code, outputs and written answers |
| `docs/architecture.png` | Architecture diagram: sensors, gateway, network server, cloud, dashboard |
| `docs/adr/0001-send-option-by-spreading-factor.md` | The Architecture Decision Record for the main design decision |

## How to run

1. Open the notebook in Google Colab (File, Upload notebook) or Jupyter.
2. Run all cells from the top (Runtime, Run all). Every check should print PASS.
3. The diagram cell saves `docs/architecture.png`.

## Summary of the design

- The temperature is packed into 2 bytes instead of JSON text (27 bytes).
- SF7 and SF8 rooms (23) send every 5 minutes. SF9 to SF11 rooms (15) send on change. The 2 SF12 basement rooms send only when the room crosses 21 C (warm events).
- The building goes from 11,520 messages a day to about 6,978 (normal day) and 7,190 (busy day).

See the ADR for the evidence and the trade-offs.

## AI use

I used Claude (Anthropic) to help complete code blanks and draft written answers, the diagram code, the ADR and this README. The data is simulated for teaching. The 30 s and 10 downlink limits come from The Things Network Fair Use Policy.
