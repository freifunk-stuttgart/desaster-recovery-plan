# Disaster Recovery

## Offene Punkte

  * Wiki?!?!?! Netbox...?


## Szenarien

  * wir verlieren ffs13
  * wir verlieren ffs08
  * wir verlieren den Standort Zettachring pvez10a
  * wir verkacken irgendeine Config, ruiniern den ZFS Pool, ...

## Recovery: wir verlieren ffs08

neuen ffs08 installieren

cluster join

restore aus pbs auf ffs13

oder replikation resync

## Recovery: wir verlieren ffs13

neuen ffs13 installieren

Cluster join

### Alternative A: Restore von Leonard PBS


Leonard faehrt mit seinem PBS-Rechner nach z10a (benoetigt: USB-Netzwerkkarte oder Port von Nepustil geconfed in VLAN 284 oder ffs14 abziehen), statische IP auf dem PBS confen, statische IP auf dem neuen ffs13 confen

Leonard-PBS als Storage in ffs13 confen

### Alternative B: Restore vom Lihas PBS


Der Lihas-PBS ist auch als Backup-Storage hinterlegt und kann daher direkt zum restore genutzt werden

pbs01 restore

pbs01 resyncen von Leonards Rechner (z.B push von leonard nach pbs01)

## Recovery: wir verlieren den Standort Zettachring pvez10a

siehe Recovery: wir verlieren ffs13 (sinngemaess, keine Cluster Join) und danach wir verlieren ffs08

## Recovery: wir verkacken irgendeine Config, ruiniern den ZFS Pool, ...

restore aus PBS

