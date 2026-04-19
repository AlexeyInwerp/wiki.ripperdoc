---
title: Fusion Drive
description: Dieser Artikel beschreibt, was ein Fusion Drive ist, aus der Perspektive der Datenrettung.
date: 2025-02-25
---

{{% alert title="Übersetzter Platzhalter" color="warning" %}}
Dies ist eine vorläufige, maschinell erzeugte deutsche Übersetzung. Die verbindliche Fassung ist die englische Originalversion: [View in English](/wiki/en/docs/mac-repair/universal/fusion_drive/).
{{% /alert %}}

{{% pageinfo %}}
Fusion Drive wurde mit dem iMac 2012 eingeführt und ist ein Nachfolger von "Core Storage", einem proprietären Software-RAID-System von Apple.
{{% /pageinfo %}}

## Was ist ein Fusion Drive
Technisch gesehen ist ein Fusion Drive ein JBOD (Just a Bunch of Disks), was bedeutet, dass beide Laufwerke hintereinander verbunden sind. Ein Defragmentierungsalgorithmus auf Kernel-Ebene sorgt dafür, dass die aktuellsten Daten im SSD-LBA-Bereich verbleiben.

## Daten einfach bei SSD-Upgrade oder Migration sichern
Wenn beide Laufwerke einwandfrei funktionieren und keine Fehler-Flags in SMART aufweisen, kann man beide einfach an einen Mac mit einem relativ modernen macOS anschließen, und sie sollten eingehängt werden. Zu beachten ist, dass der USB Gumstick4-Adapter nur mit AHCI-Laufwerken funktioniert und nicht mit iMac-Laufwerken von 2015–2017, die NVMe sind. Für den Anschluss solcher Laufwerke kann nach einem NVMe-zu-Apple-SSD-Adapter gesucht werden, der etwa 10 USD kostet und den Anschluss einer solchen SSD an einen normalen NVMe-Adapter ermöglicht. Es gibt einige Adapter auf dem Markt, die intelligent genug sind, um mit AHCI-, SATA- und NVMe-Geräten zu arbeiten, aber das ist nicht zwingend erforderlich.
Bei einer Migration auf eine neue SSD empfiehlt es sich, die originale HDD im Gerät zu lassen, macOS auf einem neuen NVMe-Laufwerk zu installieren, dann den alten SSD-Teil mit einem Adapter anzuschließen und den Migrationsassistenten zu verwenden.

## Datenrettung bei getrenntem Fusion Drive
Wenn ein Fusion Drive getrennt ist oder eines der Geräte fehlerhaft ist, sollte als erstes ein Image des beschädigten Laufwerks erstellt werden. Wenn keine Datenrettungssuite (wie PC3000 oder ähnliches) vorhanden ist, sollte die Software [OpenSuperClone](https://github.com/ISpillMyDrink/OpenSuperClone) verwendet werden. Für eine komfortable Arbeit wird ein vollständiges Byte-für-Byte-Image beider Laufwerke benötigt.

Der nächste Schritt wäre die Verwendung von UFS Explorer oder anderer Software, die mit RAID-Partitionen arbeiten kann. [UFS Explorer](https://www.ufsexplorer.com/) ist mit Abstand die beste Software für die logische Datenrettung und unterstützt APFS vollständig, einschließlich der Möglichkeit, Daten direkt aus Snapshots zu extrahieren.
Einer der Gründe für eine Fusion-Drive-Trennung ist ein beschädigter Partitionsdeskriptor. Ganz am Anfang jedes Laufwerks befindet sich ein kurzer Fusion-Drive-Header mit einer Partitions-ID. Eine Musterbeschreibung wird möglicherweise später hinzugefügt, aber grundsätzlich erfordert eine solche Wiederherstellung die Korrektur der ID, sodass beide Partitionen aufeinanderfolgende Nummern hätten (zum Beispiel ...5 ...6).
