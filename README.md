# Disaster Recovery

Dieses Repository soll dokumentieren, wie wir beim Freifunk-Stuttgart mit einem Datenverlust im Sinne der Nichtverfügbarkeit umgehen möchten.

## Offene Punkte

  * Netbox, noch unklar, was für Recovery benötigt wird.
  * Dieses Repository nach [https://github.com/freifunk-stuttgart/desaster-recovery-plan](https://github.com/freifunk-stuttgart/desaster-recovery-plan) synchronisieren.


## Erledigte Punkte

### Wiki enthält wichtige Informationen

Auslöser war, dass diese Dokumentation nur im Wiki verfügbar war. Inzwischen ist diese Dokumentation nach [https://gitlab.freifunk-stuttgart.de/infrastruktur/desaster-recovery-plan](https://gitlab.freifunk-stuttgart.de/infrastruktur/desaster-recovery-plan) migriert und wird auch nach github.com migriert (noch kein Repository 


## Vorbereitungen

Damit ein Desaster-Recovery möglich wird, sind einige Punkte wichtig zu beachten

  * Pläne, wie in bestimmten Situationen reagiert werden soll, damit man im Stress nichts übersieht
  * Zugangsdaten

In diesem Dokument sind keine Zugangsdaten und Schlüssel enthalten, diese müssen aber im Besitz von Menchen sein, die ein Recovery durchführen müssen. Es ist zu diesem Zweck notwendig eine lokale Kopie von seinen Zugangsdaten, die auf [https://vaultwarden.freifunk-stuttgart.de](https://vaultwarden.freifunk-stuttgart.de) liegen, anzulegen. 

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

## Anleitungen

### Restore per PBS Backup client

  - Proxmox Backup Client gemaess [Doku](https://pbs.proxmox.com/docs/installation.html#package-repositories-client-only-apt) installieren - ist bei einer normalen PVE-Installation bereits da
  - `apt install proxmox-backup-client`
  - Falls dies eine neue Installation ist und keine Backup-Jobs konfiguriert sind
    - Passwort fuer PBS-User nach `/etc/backup/pbs-password.txt` legen
    - Encrpytion key file nach `/etc/backup/pbs-encryption.key` legen
    - `export PBS_REPOSITORY=${HOSTNAME}@pbs@pbs01.freifunk-stuttgart.de:srv`
    - `export PBS_PASSWORD_FILE=/etc/backup/pbs-password.txt`
    - `export PBS_ENCRYPTION_KEY=/etc/backup/pbs-encryption.key`
  - Falls bereits ein Backup-Job im Proxmox konfiguriert ist
    - `source /etc/default/backup_pbs`
    - `export PBS_ENCRYPTION_KEY=/etc/pve/priv/storage/pbs_ffs_pbs01.enc`
  - Vorhandene Backups listen per `proxmox-backup-client snapshot list --ns ffs`
  - Restore per `proxmox-backup-client restore host/ffs13/2025-01-05T16:58:10Z root.pxar /mnt/ --ns ffs --keyfile $PBS_ENCRYPTION_KEY`
  - fuse-Mount per `proxmox-backup-client mount host/ffs13/2025-01-05T16:58:10Z root.pxar /mnt  --ns ffs --keyfile $PBS_ENCRYPTION_KEY`


