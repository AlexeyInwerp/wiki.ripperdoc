---
title: "MacBook funktioniert nach dem Update auf macOS 26.4.1 plötzlich nicht mehr"
linkTitle: "MacBook 26.4.1 — DFU-Fehler 4042"
date: 2026-04-18
weight: 10
description: "Plötzlich schwarzer Bildschirm nach dem Update auf macOS 26.4.1. Warum ein DFU-Restore mit Fehler 4042 scheitert und wie man das Gerät mit einem doppelten Revive ohne Datenverlust rettet."
categories: [macOS, Apple Silicon, Firmware]
tags: [DFU, revive, kernel panic, error 4042, IPSW]
---

## Das Symptom — und die Varianten

Das MacBook ist nach einem Update auf macOS 26.4.1 plötzlich nicht mehr startklar. In der Werkstatt sehen wir das in mehreren Varianten:

- Der Kunde hat das 26.4.1-Update manuell ausgeführt, das Gerät neu gestartet — danach bleibt der Bildschirm schwarz.
- Das Update ist über Nacht automatisch installiert worden (macOS-Standardverhalten: "Morgen früh um 2 Uhr installieren"). Am nächsten Morgen bootet das Gerät nicht mehr.
- Es wurde nie auf "Aktualisieren" geklickt — aber automatische Firmware- oder Sicherheitsupdates landen auf dieselbe Weise im System.

Die sichtbaren Symptome unterscheiden sich leicht: komplett schwarzer Bildschirm, Apple-Logo gefolgt von Schwarz, Lüfter laufen ohne Bild, Power-LED an ohne Display, oder ein endloser Neustart-Loop. Viele Kunden kommen zu uns in der festen Überzeugung, nichts aktualisiert zu haben — automatische Updates machen das oft unsichtbar.

## Was technisch passiert

Die 26.4.1-Firmware löst auf betroffenen Modellen eine **Kernel Panic während der DFU-Restore-Transition-Phase** aus. Das Schreiben der Firmware läuft sauber durch, aber sobald das Gerät vom DFU-Modus in den recoveryOS booten soll, friert es ein und meldet sich nie wieder zurück.

## Das Log — Fehlercode 4042

Ein typischer Auszug aus Apple Configurator 2:

```
[17:59:01.7501] Finished DFU Restore Phase: Successful
[17:59:01.7505] Changing state from 'Restoring' to 'Transitioning'
[17:59:01.7505] Creating timer to monitor transition
[17:59:01.7505] Creating a timer for 10 minutes
[17:59:01.8554] DFU mode device disconnected
[17:59:01.8554] Device disconnected during transition
[18:09:01.7592] Timer fired to timeout transitioning device
[18:09:01.7596] Changing state from 'Transitioning' to 'Disappeared'
[18:09:01.7596] Device disappeared during transition
[18:09:01.7597] Device isn't booted but USB is up.
[18:09:01.7655] Restore completed, status: 4042
[18:09:01.7655] Elapsed time (in seconds): 607
[18:09:01.7655] Failure Description:
[18:09:01.7655] Depth:0 Code:4042 Error:Gave up waiting for device to transition from DFU state to DFU state.
[18:09:01.7664] AMPDevicesAgent: Restore error 4042
```

Was darin steht, in einfacher Sprache: der Firmware-Write war erfolgreich. Das Gerät hat sich — wie erwartet — vom USB getrennt, um in recoveryOS zu booten. Es ist aber innerhalb der 10-Minuten-Frist nicht wiederaufgetaucht. Configurator gibt auf und meldet 4042. Ursache ist keine kaputte Kabelverbindung und kein fehlgeschlagenes Schreiben — es ist eine **Kernel Panic beim Boot**, die das Gerät in einen toten Zwischenzustand zwingt.

## Die sichere Lösung — "Double Revive"

**Wichtige Warnung vorab: Auf keinen Fall einen vollständigen Restore versuchen.** Restore löscht alle Benutzerdaten *und* läuft in denselben 4042-Fehler, weil die Zielfirmware dieselbe kaputte 26.4.1 ist.

Der sichere Weg in Apple Configurator 2:

1. Gerät in DFU versetzen (je nach Modell die bekannte Tastenkombination).
2. **Revive** (nicht Restore!) mit einem **älteren IPSW** — eine Version zurück, zum Beispiel 26.4.0 oder das letzte 26.3.x. Revive installiert nur Firmware und recoveryOS neu; die APFS-Benutzervolumes bleiben unberührt.
3. Gerät bootet danach normal auf der älteren Firmware. Login-Screen abwarten.
4. **Erneut Revive** — diesmal mit dem **aktuellen IPSW (26.4.1)**. Dieser zweite Revive gelingt, weil das Gerät jetzt lebt und den Firmware-Übergang nicht mehr aus dem kalten DFU-Boot-Pfad heraus machen muss.
5. Daten sind erhalten; Gerät ist auf aktueller Firmware.

Warum das funktioniert: der erste Revive bringt das Gerät zurück auf eine bootfähige Firmware, die die Kernel Panic im Transition-Pfad nicht auslöst. Der zweite Revive aktualisiert die Firmware aus einem laufenden System heraus und umgeht den fehlerhaften Kaltstart-Pfad.

## Was auf keinen Fall tun

- **Kein Restore** in Configurator — Datenverlust und derselbe 4042-Fehler.
- **Kabel nicht ziehen**, solange das Gerät im Zustand "Transitioning" hängt — das Risiko eines tieferen Bricks steigt deutlich.
- **Keine Drittanbieter-Tools** für Apple-Silicon-Firmware. Es gibt dafür keine seriösen Alternativen zu Configurator.

## Wann zu uns bringen

Das ist ein sicherer Werkstatt-Fix, den wir regelmäßig durchführen. Die Daten bleiben erhalten, die Reparatur dauert in der Regel weniger als eine Stunde. [Kontakt und Terminvereinbarung](https://www.ripperdoc.de/kontakt/).

## Weiterführende Links

- [logi.wiki](https://logi.wiki) — technische Artikel zur MacBook-Reparatur
- [repair.wiki](https://repair.wiki) — Community-Dokumentation zur Geräte-Reparatur
