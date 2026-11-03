# ADR 0001: Choose the sending option by spreading factor


**Status:** proposed

**Context:** The university has 40 meeting rooms with LoRaWAN temperature sensors on The Things Network, which allows each sensor 30 s of airtime and 10 downlinks a day. Sending a JSON reading every 5 minutes (288 messages) fits only 14 of the 40 rooms; packing the temperature into 2 bytes raises that to 23. The other 17 rooms (SF9 to SF12) cannot send every 5 minutes because of the 13-byte header and the slow spreading factor. The facilities team needs a dashboard and a warning when a room is warmer than 21 C. The gateway can also go down (tested 14:30 to 16:25), and messages sent then are lost.

**Options considered:**
1. Send every 5 minutes (packed).
2. Send the average every 60 minutes.
3. Send on change (0.5 C step, 3-hour heartbeat).
4. Warm events (send only when the room crosses 21 C, 3-hour heartbeat).

**Decision:** Pack every reading into 2 bytes. SF7 and SF8 rooms (23 rooms) send every 5 minutes. SF9, SF10 and SF11 rooms (15 rooms) use send on change. The 2 SF12 basement rooms use warm events.

**Evidence:**
- Packed budget per day: SF7 647, SF8 323, SF9 181, SF10 90, SF11 45, SF12 25 (need 288).
- Messages, normal day / busy day: every 5 min 288 / 288, every 60 min 24 / 24, on change 22 / 36, warm events 12 / 13.
- On change breaks the SF12 budget on the busy day (36 messages against 25) but fits SF10 (36 of 90).
- Minutes wrong about warm, normal / busy: every 60 min 185 / 120, on change 40 / 30, warm events 0 / 0.
- Average error (C), normal / busy: on change 0.14 / 0.12, warm events 0.43 / 0.51.
- Outage test: losing one warm-events message raised wrong-warm time from 0 to 180 minutes.
- Building total: 11,520 messages a day before, about 6,978 (normal day) and 7,190 (busy day) after.

**Consequences:**
- (+) Every room stays inside its airtime budget on both test days, with a different rule for each group.
- (+) Warm events is never wrong about 21 C on either day when no message is lost, so warnings are reliable in the basement.
- (+) About 38 to 39 percent fewer messages for the building, so longer battery life and less load on the shared network.
- (-) The SF12 dashboard is less accurate (error 0.43 to 0.51 C).
- (-) Warm events is fragile: one lost message can leave the cloud wrong for up to 3 hours. Mitigation: keep the 3-hour heartbeat, confirm only the heartbeat (within 10 downlinks) and re-send missed readings together in one batch (1.32 s fits in the 2.28 s spare).
- (-) SF11 has a thin margin on the busy day (36 of 45 messages). Needs monitoring.
- (-) Results come from one simulated room and are assumed to hold for every room in a group, so real data should be checked after deployment.
