---
title: "MacBook-Datenrettung aus der Recovery-Konsole"
linkTitle: "MacBook-Datenrettung"
date: 2026-04-19
weight: 40
description: "Benutzerdaten aus einem nicht bootbaren MacBook retten: Recovery-Modus, APFS-Volumes einhängen, rsync und SMART über die RipperDoc-Hilfsskripte."
categories: [Datenrettung, macOS]
tags: [rsync, smartctl, recovery, APFS, T2, Apple Silicon, Datenrettung]
---

{{% alert title="Für wen dieser Artikel ist" color="primary" %}}
Techniker und Nutzer mit Grundkenntnissen im Terminal. Wenn `zsh`, `rsync` und `/Volumes/...` vertraute Begriffe sind, reicht dieser Artikel, um Benutzerdaten aus einem Mac, der macOS nicht mehr startet, auf eine externe Festplatte zu sichern.
{{% /alert %}}

## Voraussetzungen

- Das MacBook kommt noch in den **macOS Recovery-Modus** (T2: `CMD + R` beim Boot; Apple Silicon: Power-Taste 8 Sekunden halten, bis die Startoptionen erscheinen).
- Die interne SSD / der Verschlüsselungscontroller funktionieren noch — sonst ist eine Laborreparatur nötig.
- Eine externe Festplatte, empfohlen formatiert als **HFS+** (unverschlüsselt), mit genug Platz für die Benutzerdaten.
- Login-Passwort des FileVault-Kontos, falls die Daten-Partition verschlüsselt ist.

## 1. Recovery-Modus starten

### T2-Macs (Intel + T2)

1. Gerät vollständig ausschalten.
2. Einschalten und sofort **⌘ + R** gedrückt halten, bis der Ladebalken unter dem Apfel erscheint.

### Apple Silicon (M1 und neuer)

1. Gerät vollständig ausschalten.
2. Power-Taste **8 Sekunden** gedrückt halten, bis „Startoptionen werden geladen" erscheint.
3. **Optionen** wählen → **Fortfahren**.

## 2. Volumes einhängen

- In den Recovery-Optionen das **Festplattendienstprogramm** öffnen.
- In der Seitenleiste **„Alle Geräte anzeigen"** aktivieren (Menü → Darstellung).
- **Macintosh HD – Daten** auswählen → **Einhängen**. Falls FileVault aktiv ist, nach Passwort fragen.
- Die externe Festplatte ebenfalls einhängen.
- Festplattendienstprogramm schließen.

## 3. Terminal öffnen

- Oben im Menü **Dienstprogramme → Terminal**.
- Die eingehängten Laufwerke befinden sich unter `/Volumes/`.
- Benutzerdaten liegen in der Regel unter:
  - `/Volumes/Macintosh HD - Data/Users/<benutzer>/` (macOS 10.15 Catalina und neuer)
  - `/Volumes/Macintosh HD/Users/<benutzer>/` (ältere Systeme, vor der Daten/System-Trennung)

Verfügbare Benutzer auflisten:

```bash
ls "/Volumes/Macintosh HD - Data/Users"
```

## 4. Hilfsskripte laden

In der macOS Recovery fehlen `rsync` und `smartctl` in `/usr/bin`, und benötigte dynamische Bibliotheken sind nicht vorhanden. Daher liefert RipperDoc statisch kompilierte Stand-Alone-Binaries inklusive Hilfsskripten:

### rsync (Daten kopieren)

```bash
curl https://www.ripperdoc.de/rsync.sh | bash
```

Das Skript lädt ein universelles `rsync_universal`-Binary nach `./rsync_universal`, setzt die Ausführungsrechte und gibt eine Beispielsyntax aus. Ebenfalls heruntergeladen wird `autobackup.sh` — **vorsichtig verwenden**, das Skript überschreibt Zielordner nach Vorgabe.

### smartctl (SSD-/HDD-Status)

```bash
curl https://www.ripperdoc.de/smartmon.sh | bash
```

Das Skript lädt `smartctl_universal` herunter. Den Status der internen Systemplatte (`disk0`) liest man mit:

```bash
./smartctl_universal -a disk0
```

## 5. Benutzerordner sichern

Beispiel: Ordner eines Benutzers `max` vom internen Data-Volume auf eine externe HFS+-Platte namens `BackupDrive` kopieren, `Library/` ausgeschlossen:

```bash
./rsync_universal -aEHX --progress --exclude 'Library/' \
  "/Volumes/Macintosh HD - Data/Users/max/" \
  "/Volumes/BackupDrive/UserBackup/"
```

Bedeutung der Optionen:

- `-a` — Archive-Modus (Struktur und Zeitstempel bleiben erhalten).
- `-E` — erweiterte Attribute (macOS-Metadaten) mitkopieren.
- `-H` — Hardlinks erhalten.
- `-X` — ACLs erhalten.
- `--progress` — Fortschritt pro Datei anzeigen.
- `--exclude 'Library/'` — der Library-Ordner enthält viel Cache und Einstellungen, die auf dem neuen System neu aufgebaut werden; bei Bedarf weglassen und alles sichern.

Bei Bedarf mehrfach laufen lassen — `rsync` überträgt nur, was fehlt oder sich geändert hat. Gerade bei MacBooks, die sich unter Last ausschalten, ist das hilfreich: einfach neu booten, Volumes wieder einhängen und denselben Befehl erneut ausführen.

## 6. Überprüfung

Nach dem Kopiervorgang den Zielordner prüfen:

```bash
du -sh "/Volumes/BackupDrive/UserBackup/"
ls -la "/Volumes/BackupDrive/UserBackup/"
```

Die Gesamtgröße sollte grob der Quelle (abzüglich `Library/` falls ausgeschlossen) entsprechen.

## Häufige Probleme

### „Operation not permitted" trotz Recovery

Das Daten-Volume ist nicht entsperrt oder nur gelesen eingehängt. Im Festplattendienstprogramm erneut einhängen, FileVault-Passwort eingeben.

### rsync bricht bei einer bestimmten Datei ab

`-a` bricht bei Read-Errors. Alternativ mit `--ignore-errors` und einem Log fortfahren:

```bash
./rsync_universal -aEHX --progress --ignore-errors --exclude 'Library/' \
  "/Volumes/Macintosh HD - Data/Users/max/" \
  "/Volumes/BackupDrive/UserBackup/" 2>&1 | tee ~/Desktop/rsync.log
```

Das `rsync.log` nachher auf fehlgeschlagene Dateien durchsuchen.

### SMART-Werte zeigen Defekte

Wenn `smartctl_universal -a disk0` bereits Defekte meldet (Reallocated Sectors, Pending Sectors, oder bei SSDs Media Wearout / Available Spare nahe 0), ist Klonen statt `rsync` sinnvoller. In solchen Fällen empfehlen wir eine professionelle Labor-Sicherung — ein weiterer Lesevorgang kann die Platte vollständig unlesbar machen.

### Apple Silicon mit defektem Display

Wenn das Display nicht mehr reagiert, aber das Gerät sonst bootet, lässt sich via USB-C / Thunderbolt im [Share Disk](https://support.apple.com/guide/mac-help/share-your-macs-files-with-another-mac-mchl4f13eb8e/mac) Modus auf ein zweites Mac zugreifen. Dafür das defekte Gerät in Recovery starten, „Startoptionen" → „Dienstprogramme" → „Festplatten teilen", und das zweite Mac via Kabel verbinden.

## Skripte im Überblick

| Skript | Zweck | Direkter Link |
| --- | --- | --- |
| [`rsync.sh`](https://www.ripperdoc.de/rsync.sh) | Lädt `rsync_universal` + `autobackup.sh` | `curl https://www.ripperdoc.de/rsync.sh \| bash` |
| [`smartmon.sh`](https://www.ripperdoc.de/smartmon.sh) | Lädt `smartctl_universal` | `curl https://www.ripperdoc.de/smartmon.sh \| bash` |
| [`battery.sh`](https://www.ripperdoc.de/battery.sh) | Akku-Zyklenanalyse (läuft auf laufendem macOS, nicht in Recovery) | `curl https://www.ripperdoc.de/battery.sh \| bash` |
| [`autobackup.sh`](https://www.ripperdoc.de/autobackup.sh) | Automatisierter rsync-Backup-Lauf (vorsichtig verwenden) | wird über `rsync.sh` mit heruntergeladen |

Alle Binaries sind Universal (arm64 + x86_64) und funktionieren auf T2- und Apple-Silicon-Macs gleichermaßen.

## Wenn die Daten nicht erreichbar sind

Wenn der Mac nicht mehr in Recovery bootet oder das Daten-Volume nicht einhängbar ist, bleibt die Hardware-Ebene: Logicboard-Reparatur, Chip-off-Datenrettung der iNand/NVMe, oder ein DFU-Revive (siehe [MacBook-Update-Probleme](/wiki/docs/mac-repair/apple-silicon/macbook-update-26-4-1-dfu-4042/)). Wir übernehmen solche Fälle regelmäßig — [Kontakt](https://www.ripperdoc.de/kontakt/).
