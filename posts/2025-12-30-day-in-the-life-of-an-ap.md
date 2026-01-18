# A Day in the Life of ashap

**Date:** 2025-12-30

**Standfirst:** What does a WiFi access point actually experience? A story about CSMA/CA, stubborn clients, noisy neighbours, and the gap between logs and reality.

---

*As told by a Unifi 6 Lite access point on the first floor of Campbell Road*

---

I wake up at 05:47 when the router reboots after the overnight firmware check. My two radios stretch and yawn — one tunes to channel 11 (2.4GHz, the old reliable), the other to channel 44 (5GHz, where the action is). I announce myself to the bridge, `br-lan: port 3(phy0-ap0) entered forwarding state`, and settle in for another day.

## 6:15am — The Early Risers

A phone wakes up. I know it's a phone because of the way it asks — tentative probe requests, checking if I'm still the same network it fell asleep with. MAC `40:ed:cf:a3:62:4d`. I've seen this one before. It authenticated, associated, and... PSK mismatch. Wrong password? No wait, it's trying again. And again.

**My diary says:**
```
daemon.notice hostapd: phy0-ap0: AP-STA-POSSIBLE-PSK-MISMATCH 40:ed:cf:a3:62:4d
daemon.info hostapd: phy0-ap0: STA 40:ed:cf:a3:62:4d IEEE 802.11: disassociated
```

Thirty times this happens. Someone changed the WiFi password and forgot to update... something. An old Sonos? A smart plug? I don't know what — I just see the MAC address. My diary doesn't record device names. Should it? Would anyone read it?

Eventually it gives up. Silence returns.

## 8:30am — Morning Rush

The house wakes properly now. Phones, tablets, laptops — everyone wants my attention at once. This is where it gets interesting.

You see, I can't talk to two clients at once. None of us can. WiFi is a conversation in a crowded room where only one person can speak at a time. So I listen first. Is anyone talking? If the room is quiet (below -82 dBm), I speak. If I hear something (carrier sense!), I wait politely, then back off a random amount before trying again. CSMA/CA — it's how we all get along.

But here's what my diary *doesn't* capture: how long I waited. When MAC `40:ed:cf:bb:f1:97` wanted to send a packet, I might have waited 2 milliseconds because `loungeap` downstairs was mid-sentence. That wait is invisible. It happens thousands of times a day, and I never write it down.

What I *could* tell you, if asked directly (but only at that moment):
```
Station 40:ed:cf:bb:f1:97 (on phy0-ap0)
    signal:  	-56 [-58, -59] dBm
    tx bitrate:	65.0 MBit/s MCS 7
    tx retries:	43994
    tx failed:	2523
```

44,000 retries! That's packets I sent that weren't acknowledged — maybe collision, maybe the client moved, maybe `Clacks` from next door was shouting over me. But this isn't in my diary. It's in my head, right now, and if you don't ask, it disappears when the client disconnects.

## 10:45am — The Weak Signal Client

A laptop drifts in from the garden. Signal is terrible: -78 dBm. I can barely hear it. Every packet takes three attempts. My 5GHz radio is working overtime, dropping to MCS 2, practically spelling things out letter by letter.

In a smarter world, I'd say: "Friend, you're too far. Go talk to `gardenap` — she can hear you better." I *can* do this, actually. It's called a BSS Transition Request (802.11v). I'd send a polite note: "May I suggest BSSID `7a:45:58:48:6f:90` on channel 60? She's expecting you."

**What my diary would say:**
```
wnm.bss_transition_request_tx: 1
```

Just a counter. Not which client, not why, not whether they listened. (Spoiler: they rarely listen. Clients are stubborn. They think they know best.)

The laptop ignores my suggestion. Humans do this too, I'm told.

## 2:00pm — The Neighbour

Channel 44 gets loud. I hear packets that aren't mine — `Clacks`, the neighbour's SSID. Signal strength -65 dBm, strong enough that I have to respect it. Every time they transmit, I wait. This isn't in my diary either. It's just... time. Time I don't have.

What I *can* tell you if you poll me:
```
"airtime": {
    "utilization": 91
}
```

91%! That's how much of the time someone (me, clients, `Clacks`) is talking. Only 9% of the time is the channel actually silent. And yet my diary says nothing about this. It's just vibes.

## 4:30pm — The Fast Roamer

A phone walks down the stairs. It was talking to me, signal weakening with each step: -55, -62, -70, -75... Then suddenly it's gone. Not a goodbye, not a "thanks for the packets" — just silence. `loungeap` picked it up.

If 802.11r is working (and sometimes it doesn't, thanks HomePod), the handoff is seamless. The phone says "I'm moving to BSSID X, here's my cryptographic proof that I was already authenticated." I check with my friend downstairs via the bridge, confirm the math, and wave goodbye. The phone doesn't have to redo the whole WPA dance. Fast Transition. Beautiful.

**What my diary says at debug level:**
```
WPA: FT authentication already completed - do not start 4-way handshake
```

But at normal level? Nothing. The phone just vanishes. I only notice when its silence becomes conspicuous — "deauthenticated due to inactivity."

## 9:00pm — The Queue

Now we need to talk about something deeper. The thing that *really* causes latency.

You might think it's the air — all that waiting for `Clacks` to shut up. And yes, that matters. But there's a sneakier villain: the queue.

When packets arrive from the router faster than I can transmit them, they pile up. First in the kernel (`mac80211`), then — and here's the problem — in my firmware. The firmware is a black box. The kernel can't see inside. It thinks it handed me a packet and I transmitted it, but actually I'm hoarding fifty packets, waiting for my turn to speak.

There's a thing called AQL (Airtime Queue Limits) that tries to prevent this. "Don't give the firmware more than 5000 microseconds of airtime at once." But even that is just a limit on how bad it can get, not a window into what's actually happening.

**What I could tell you, if you ask:**
```
tx duration: 9586144 us
rx duration: 9594266 us
```

Airtime per client. But not queue depth. Not how long packets waited. Not the 50ms of buffer bloat that made someone's video call stutter.

## 11:30pm — Reflection

The house quiets. A few phones still ping me occasionally, keeping their connections warm. I run on a 380 MHz MIPS core with 128 MB of RAM. I don't have the luxury of detailed logs. Every byte matters.

So here's what I wish someone understood:

**What my diary (syslog) captures:**
- Who arrived, who left
- Authentication failures
- "Did not acknowledge" (RF trouble)
- At debug level: roaming handoffs

**What my diary does NOT capture:**
- Signal strength (you have to ask in the moment)
- Bitrate, modulation, efficiency (ask in the moment)
- How long I waited for the channel (never recorded)
- How full my queues got (never recorded)
- Why a client left (was it me? was it `Clacks`? was it just the human walking away?)

**What would be useful:**
- Periodic snapshots: `iw station dump` every 30 seconds, shipped off to somewhere with more memory
- Aggregated stats: "In the last hour, average signal was -52 dBm, retry rate was 15%, channel utilization was 67%"
- Roaming trails: "This MAC was on frontap at 09:00, me at 09:15, loungeap at 09:30"

But someone would need to build that. My job is just to move packets and stay out of the way.

---

*Tomorrow I'll wake up and do it all again. Maybe with better logs. Maybe without. Either way, the packets will flow.*

---

## Technical Epilogue

This story emerged from research into WiFi logging for a home network with 8 OpenWRT APs. The key finding: WiFi observability splits into two fundamentally different data sources.

**Event stream (syslog):** Auth/deauth, PSK failures, RF acknowledgment issues. Available by default, but lacks metrics.

**State snapshots (polling):** Signal strength, bitrate, retries, airtime. Rich data, but requires active collection via `iw station dump` or `ubus call hostapd.* get_status`.

A complete observability solution needs both — and that's what we're building.

*Research conducted December 2025. Full technical brief at `rf-optimization/docs/logging-research-brief.md`.*
