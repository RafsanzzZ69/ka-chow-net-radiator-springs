# Packet Tracer Verification Checklist

Use a copy of the `.pkt` file for destructive failover testing if you want to preserve the original state exactly.

## 1. Baseline and interfaces

- Open `packet-tracer/Group1808_Ka-Chow_Net.pkt` and wait for convergence.
- On every router, run `show ip interface brief`; all intended interfaces should be `up/up`.
- Run `show ip route` and confirm connected, static, and RIP-learned routes match the design.
- On SS, RBA, and DC, run `show ip protocols`; confirm RIP version 2 and `no auto-summary` behavior.

## 2. DHCP and addressing

- On both PCs in every unit, select Desktop → IP Configuration → DHCP.
- Confirm the assigned address belongs to that unit's VLSM subnet.
- Confirm the default gateway is the unit router's LAN address and DNS is `11.10.0.2`.
- On SS, run `show ip dhcp binding` to inspect leases for SS, DC, and RBA.
- Confirm relay interfaces on DC, RBA, MTY, and WWM point to their documented DHCP server.

## 3. End-to-end connectivity

- From at least one PC in each unit, ping its gateway.
- Ping the central DNS server `11.10.0.2`.
- Ping a client or printer in each of the other six units.
- Use `tracert` from representative endpoints to verify the expected path.

## 4. DNS and web

- From multiple PCs, open the browser and test the Sheriff and Flo web hostnames configured in DNS.
- If one Flo hostname variant fails, inspect DNS records: the supplied assignment brief alternates between `www.flo.radiatorsprings.nyc` and `www.flosv8.radiatorsprings.nyc`.
- Also test direct web-server IPs `11.10.0.3` and `11.10.3.3`.

## 5. Email

- Verify local-domain mail between two accounts in one unit.
- Send mail from `sheriff.rs` to `flo.rs`, then reply from `flo.rs` to `sheriff.rs`.
- Confirm send and receive operations succeed in both directions.

## 6. Failover tests

### FVC recursive backup

1. Before failure, inspect `show ip route 11.10.0.0` on FVC; the preferred route should use S0/1/0 toward SS.
2. Shut the SS–FVC link on one end.
3. Wait for convergence, then repeat `show ip route 11.10.0.0`; the recursive route via LCT (`11.10.6.66`, AD 5) should become active.
4. Ping an SS host from FVC.
5. Restore the interface with `no shutdown` and confirm the primary route returns.

### LCT floating backup

1. Before failure, inspect `show ip route 11.10.0.0` on LCT; the preferred route should use SS at `11.10.6.65`.
2. Disable the direct LCT-to-SS path on the downtown transit side as demonstrated in the report.
3. Confirm the floating route via DC (`11.10.6.67`, AD 5) becomes active.
4. Ping an SS host from LCT.
5. Restore the interface and confirm the preferred route returns.

## 7. Preserve the deliverable

- Restore every interface changed during testing.
- Wait for links and routes to reconverge.
- Re-run representative pings.
- Close the test copy without overwriting the repository's original `.pkt` unless you intentionally want to save the restored state.
