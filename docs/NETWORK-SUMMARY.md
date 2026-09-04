# Technical Summary

## Topology

The network contains seven departmental routers and LANs. SS, LCT, DC, FVC, and MTY share the downtown Ethernet transit network (`11.10.6.64/29`). SS, RBA, and DC form the serial Gossip Loop. SS has a direct serial link to FVC, while WWM is reached only through FVC.

Each unit has a router, access switch, two client PCs, and a printer. Server roles are distributed according to the assignment brief.

## Infrastructure addressing

| Unit | Gateway / router interfaces | Static services |
|---|---|---|
| SS | G0/0 `11.10.0.1/23`; G0/1 `11.10.6.65/29`; S0/0/0 `11.10.6.73/30`; S0/0/1 `11.10.6.81/30`; S0/1/0 `11.10.6.85/30` | DNS `.0.2`; web `.0.3`; mail `.0.4`; printer `.0.5` |
| LCT | G0/0 `11.10.4.1/24`; G0/1 `11.10.6.66/29` | DHCP `.4.2`; mail `.4.3`; printer `.4.4` |
| DC | G0/0 `11.10.2.1/24`; G0/1 `11.10.6.67/29`; S0/1/0 `11.10.6.78/30`; S0/1/1 `11.10.6.82/30` | Mail `.2.2`; printer `.2.3` |
| FVC | G0/0 `11.10.3.1/24`; G0/1 `11.10.6.68/29`; S0/1/0 `11.10.6.86/30`; S0/1/1 `11.10.6.89/30` | DHCP `.3.2`; web `.3.3`; mail `.3.4`; printer `.3.5` |
| RBA | G0/0 `11.10.5.1/25`; S0/1/0 `11.10.6.74/30`; S0/1/1 `11.10.6.77/30` | Mail `.5.2`; printer `.5.3` |
| MTY | G0/0 `11.10.5.129/25`; G0/1 `11.10.6.69/29` | Mail `.5.130`; printer `.5.131` |
| WWM | G0/0 `11.10.6.1/26`; S0/1/0 `11.10.6.90/30` | Mail `.6.2`; printer `.6.3` |

## Routing strategy

- **Gossip Loop:** SS, RBA, and DC run RIPv2 with automatic summarization disabled.
- **SS:** Redistributes its static routes into RIP with metric 1.
- **LCT and MTY:** Use explicit static routes to every non-connected subnet.
- **FVC:** Uses static routes. Its preferred route to the SS LAN exits S0/1/0; a recursive route via LCT (`11.10.6.66`) with administrative distance 5 provides backup.
- **LCT redundancy:** The preferred SS route uses SS (`11.10.6.65`); a floating static route via DC (`11.10.6.67`) with administrative distance 5 provides backup.
- **WWM:** Uses a default route to FVC (`11.10.6.89`).

## Address assignment and services

- SS router provides IOS DHCP pools for SS, DC, and RBA.
- DC relays DHCP requests to SS at `11.10.6.81`; RBA relays to SS at `11.10.6.73`.
- LCT server `11.10.4.2` provides DHCP for LCT and MTY; MTY relays to it.
- FVC server `11.10.3.2` provides DHCP for FVC and WWM; WWM relays to it.
- All clients use central DNS `11.10.0.2`.
- Web services run at SS (`11.10.0.3`) and FVC (`11.10.3.3`).
- Each unit has a local mail server. The documented cross-domain test between `sheriff.rs` and `flo.rs` succeeded.

## Evidence in the supplied report

The report includes the labeled topology, VLSM tree, addressing tables, DHCP configuration, DNS/web/email setup, end-to-end connectivity tests, cross-domain email tests, two failover tests, and router configuration appendices. The Packet Tracer file is retained unchanged as the executable network artifact.
