---
title: LAB ZFS
author: Lukas Hecht und Fabian Gröger
date: 22.04.2018
output: pdf_document
toc: Inhaltsverzeichnis
toc-title: Inhaltsverzeichnis
lang: de-CH
---

***

# LAB: ZFS

## 1 ZPool

### 1.1 Erstellung eines zpools

#### Wie viel Speicher steht Ihnen im mypool zur Verfügung (zpool list) und wie können Sie darauf zugreifen (zfs list)?

**Speicher zpool:** 19.9G  
**Zugreifen:**  
1. zfs create mypool  
2. zfs list  
3. zfs set quota=500m mypool  
4. zfs set reservation=200m mypool  
5. zfs list  
6. zfs set mountpoint=/test mypool  
7. df -h |grep /test  
8. cd /test  
9. nano zugriff.txt  

#### Überprüfen Sie mit den bereits erlernten Befehlen, was nun geschehen ist. Was ist mit der zweiten Disc geschehen?

**Vorher:**  
* mypool   
* sdb  

**Nachher:**  
* mypool  
* mirror-0  
* sdb  
* sdc  

Die Disk wird auf sdc gemirrort.

#### Überprüfen Sie nun Ihre Vermutung, indem Sie die erste Disc wieder aus dem Pool nehmen (zpool detach), ist Ihre zuvor erstellte Datei noch vorhanden und hat sich an der Speicherkapazität etwas verändert?

Ja, die vorher erstellte Datei ist noch vorhanden. Die Speicherkapazität hat sich gering verändert, von 117K auf 105K.

#### Was hat sich nun verändert? Wie viel Speicherkapazität stellt Ihnen der zpool jetzt zur Verfügung?

Nun stellt der zpool 39.8G zur Verfügung.

## 2 ZFS Datasets

### 2.1 ZFS Dataset erstellen

#### In welchem Verzeichnis steht Ihnen die neuerstellten ZFS Datasets zur Verfügung?
Im Verzeichnis mypool wurde ein neues Verzeichnis home erstellt und dort die restlichen.
(/mypool/home/..)

### 2.2 Quotas und Reservations

#### Können Sie nun noch eine weitere Datei in einem anderen mypool/home/ Directory erstellen?
Nein, kann ich nicht weil wir die Kapazität schon genutzt haben.  
`error writing '/mypool/home/your_home/test100M': Disk quota exceeded`

#### Wie wäre es nun theoretisch möglich, dass jedes Dataset unter /mypool/home eine Quota von 100M hätte?

Wenn die Kapazität für den mypool genügend gross wäre, dass jedes Dataset eine Quota von 100M haben kann.

Parent Quota (angefangen mit home) = Anzahl darin liegender Datasets * 100MB

#### Wie hat sich der Befehl nun auf die anderen Datasets ausgewirkt?

Der mypool/home/my_home wurde auf 99.9M vergrössert, den anderen stehen nur noch ca 50M zur Verfügung.

### 2.3 Snapshots

#### Finden Sie nun einen Weg, wie Sie auf den Inhalt des Files /data/file1 zugreifen können, ohne den Befehl zfs rollback zu verwenden. Zeigen Sie Ihren Weg kurz auf:

(zfs list -t snapshot)  
cd /data/.zfs/snapshot/Snapshot1/file1.txt

### 2.4 ZFS Dataset mit NFS freigeben

#### Was sehen Sie für Nachteile auf dem NFS Share den Sie eingerichtet haben (Zugriff, Sicherheit, Dateiowner)?

-rwxr-xr-x  1 4294967294 4294967294   46 Apr 10 17:13 file2b.txt  
-rwxr-xr-x  1 root       root         30 Apr 10 16:03 file2.txt  

file2b.txt wurde auf iteo-rdp.el.eee.intern erstellt. user und gruppe sind "eigenartig".

### 2.5 ZFS Deduplication und Komprimierung

#### Was stellen Sie fest?

Der allozierte Speicher (ALLOC) hat wieder zugenommen, ist jetzt jedoch nur genau 100M gross. Und bei Deduplikation (DEDUP) ist der Wert von 1.00x zu 2.00x gewechselt.

#### Wie sieht die Speicherplatzverwendung nun aus?

Durch die Kompression belegt die Datei die eigentlich 100M gross wäre, nur 540K.