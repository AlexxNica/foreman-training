# Foreman Training - Teil 2 - Provisionieren

Das Provisionieren besteht aus:
  - Betriebssystem und -version
  - OS Architektur
  - Installations Medium (Repository Server)
  - Provisionierungstemplates

## Provisioning OS

Es koennen meherere OS ausgewaehlt werden.
Das OS mit Version und Architektur der Foreman Instanz ist automatisch hinzugefuegt worden.

## Provisioning Repository Server

Default Repository Server fuer unterschiedliche Betriebssysteme sind bereits standardmaessig angelegt.

## Provisioning Templates

Foreman Login -> Hosts -> Provisioning Templates

Templates unterscheiden sich nach Funktionalitaet:

  - PXELinux, PXEGrub, PXEGrub2 : werden auf den TFTP Server deployed
  - Provision : Kickstart oder Preseed fuer unattended Installation
  - Finish : Post-Install Scripts
  - user_data: Post-Install fuer Cloud VMs
  - Script : generische Skripte
  - iPXE : {g,i}PXE anstelle von PXE


Normalerweise werden nur die ersten 3 benoetigt.

Standard Templates sind "Locked". Niemals Standard Templates editieren, immer Klonen!

Beispiel Suchen: kind=PXELinux

### Provisionierungs Templates mit OS Assoziieren

#### Foreman Login -> Hosts -> Provisioning Templates

1. Stelle: Templates mit OS Assoziieren

1.a. kind = PXELinux

"Kickstart default PXELinux" auswaehlen -> Association -> OS auswaehlen

1.b. kind = provision

"Kickstart default" auswaehlen -> Association -> OS auswaehlen

1.c. kind = finish

"Kickstart default finish" auswaehlen -> Association -> OS auswaehlen

1.d. PXE bauen

"Build PXE Default"

#### Installation media

Wenn man einen eigenen anstelle der CentOS Mirror verwenden moechte:

Foreman Login -> Hosts -> Installation Media -> Create medium

Name eintragen, bei "Path" den http Pfad eintragen.
Achtung: auf Namensaufloesung achten!

Operatingsystem Family angeben!

#### Foreman Login -> Hosts -> Operating Systems

2. Stelle: OS mit Templates Assoziieren

- Partition Table -> "Kickstart default" auswaehlen
- Installation media -> "CentOS mirror" auswaehlen (oder das vorher angelegten Installation media)
- Templates -> alle Templates auswaehlen

Submit

ACHTUNG: beim Verwenden der default templates muss safemode : false gesetzt werden.
Foreman Login -> Administrator -> Settings


## Provisionieren

Foreman Login -> Infrastructure -> Provisioning Setup

- Pre-requisites: Provisioning Network: 10.100.10.101/24 -> Submit
- Network config: -> Submit (Bei Fehler das Netzwerk umbenennen) (provision.example42.training)
- Foreman Installer: 'Install provisioning with DHCP' -> Kopieren und Ausfuehren -> Next
- Installation Media: 'CentOS mirror' -> Submit

ACHTUNG: nach dem foreman installer Kommando unbedingt die /etc/named/options.conf forwarders pruefen!!

### Host Group

Host Group braucht das Puppet Environment

Foreman Login -> Configure -> Host Groups

- "Provision from foreman.example42.training" auswaehlen
- Host Group: Puppet Environment setzen (Production)
- Operating System: PXE Loader auswaehlen (PXELinux BIOS)

Submit

### Host erzeugen in VirtualBox:

New -> Host -> 1024 MB RAM -> 20 GB HDD

Boot Einstellungen aendern: Festplatte -> Netzwerk
Netzwerk aendern: gleiches vboxnet, wie foreman.example42.training
