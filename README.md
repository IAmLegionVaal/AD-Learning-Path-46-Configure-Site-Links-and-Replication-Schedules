# AD Learning Path 46 — Configure Site Links and Replication Schedules

## Objective
Configure intersite replication cost, interval, schedule, and bridgehead topology between `HQ-Site` and `Branch-Site`, then prove replication follows the design.

## Prerequisites
- Activity 45 sites and subnets
- A domain controller in each site
- Healthy baseline replication
- Documented WAN assumptions

## Setup
1. Open Active Directory Sites and Services and locate `DEFAULTIPSITELINK` under IP transport.
2. Rename it to `HQ-Branch-Link`.
3. Add both sites to the link.
4. Set a documented cost and replication interval; use 180 minutes for the lab unless a different objective is documented.
5. Configure a restricted schedule, observe behavior, then restore an always-available lab schedule.
6. Create a disposable object in each site and compare replication timing.
7. Review connection objects and KCC-generated topology.

```powershell
Set-ADReplicationSiteLink -Identity 'HQ-Branch-Link' `
    -Cost 100 -ReplicationFrequencyInMinutes 180
Get-ADReplicationSiteLink -Filter * -Properties *
repadmin.exe /showconn *
repadmin.exe /showrepl *
```

## Validation
```powershell
Get-ADReplicationSiteLink 'HQ-Branch-Link' |
    Select-Object Name,Cost,ReplicationFrequencyInMinutes,SitesIncluded
repadmin.exe /replsummary
repadmin.exe /showrepl DC01
repadmin.exe /showrepl DC02
```

## Evidence
Store the site-link membership, cost, interval, schedule screenshot, connection objects, before/after replication timing, KCC observations, errors/remediation, and final pass/fail status under `evidence/`.

## Troubleshooting
- Sites do not replicate: verify both sites belong to a valid IP site link and RPC/DNS connectivity works.
- Schedule behaves unexpectedly: remember the schedule controls availability while the interval controls attempts within that availability.
- Manual connections persist: remove unnecessary manual topology after validating KCC operation.

## Security notes
Long replication delays increase exposure to stale passwords, group memberships, and disabled accounts. Align schedules with operational and security requirements.

## Cleanup
Restore the approved cost, interval, and schedule; remove only disposable topology changes.

## References
- Microsoft Learn: Site link design
- Microsoft Learn: `Set-ADReplicationSiteLink`

## Next activity
`AD-Learning-Path-47-Transfer-FSMO-Roles`
