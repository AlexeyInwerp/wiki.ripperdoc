---
title: "MacBook funktioniert nach dem Update auf macOS 26.4.1 plötzlich nicht mehr"
linkTitle: "MacBook 26.4.1 — DFU-Fehler 4042"
date: 2026-04-18
weight: 10
description: "Plötzlich schwarzer Bildschirm nach dem Update auf macOS 26.4.1. Warum eine Wiederherstellung mit Fehler 4042 scheitert und wie sich das Gerät ohne Datenverlust retten lässt."
categories: [macOS, Apple Silicon, Firmware]
tags: [DFU, revive, kernel panic, error 4042, IPSW]
---

## Das Symptom — und die Varianten

Das MacBook ist nach einem Update auf macOS 26.4.1 plötzlich nicht mehr startklar. Das Symptom tritt in mehreren Varianten auf:

- Das 26.4.1-Update wurde manuell ausgeführt, das Gerät neu gestartet — danach blieb der Bildschirm schwarz.
- Das Update wurde über Nacht automatisch installiert (macOS-Standardverhalten: "Morgen früh um 2 Uhr installieren"). Am nächsten Morgen bootete das Gerät nicht mehr.
- Es wurde nie auf "Aktualisieren" geklickt — aber automatische Firmware- oder Sicherheitsupdates landen auf dieselbe Weise im System.

Das sichtbare Symptom ist meist unspektakulär: ein schwarzer Bildschirm. Die CPU wird leicht warm, manchmal laufen die Lüfter — mehr ist äußerlich nicht zu erkennen.

## Was technisch passiert

Der Fehler liegt in der Boot-Kette — im Übergang **iLLB → iBoot → Kernel**. Nach bisheriger Untersuchung bleibt das Gerät bei **iBoot in einem nicht behebbaren Panic-Zustand** stehen; weitere Belege ließen sich in den Logs bislang nicht finden — dieser Abschnitt wird ergänzt, sobald genug Zeit mit einem betroffenen Gerät vorhanden ist. Das Gerät fällt nicht zwangsläufig sofort aus: Manchmal tritt der Fehler erst bei einem **vollständigen Neustart** auf — und der kann Wochen nach der Installation des fehlerhaften Updates passieren, zum Beispiel, nachdem der Akku einmal komplett leer war. Ein Gerät kann also direkt nach der Update-Installation noch normal booten und erst Wochen später — oder beim nächsten vollständigen Neustart — ausfallen.

## Das Log — Fehlercode 4042

Ein typischer Auszug aus dem Log in der Konsole (Console.app):

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

Was darin steht, in einfacher Sprache: der Firmware-Write war erfolgreich. Das Gerät hat sich danach vom USB getrennt, ist aber innerhalb der 10-Minuten-Frist nicht wieder aufgetaucht. Der Vorgang gibt auf und meldet 4042. Es ist also weder eine kaputte Kabelverbindung noch ein fehlgeschlagenes Schreiben — das Gerät bootet nach dem Schreiben schlicht nicht wieder durch.

## Die sichere Lösung

**Wichtige Warnung vorab: Auf keinen Fall eine vollständige Wiederherstellung (Restore) versuchen.** Die Wiederherstellung löscht alle Benutzerdaten *und* läuft in denselben 4042-Fehler, weil die Zielfirmware dieselbe kaputte 26.4.1 ist.

{{% alert title="Seit macOS 26.5 ist das doppelte Reparieren nicht mehr nötig" color="info" %}}
Seit dem 26.5-Update ist das doppelte Reparieren im Normalfall **nicht mehr erforderlich**. macOS 26.5 korrigiert den **iLLB** (Low-Level-Bootloader); der hängende Boot lag höchstwahrscheinlich im Übergang iLLB → iBoot (oder weiter zum Kernel). Das fehlerhafte 26.4.1-Update konnte sich nicht selbst reparieren — es blieb aus demselben Grund hängen, der den Fehler ursprünglich verursacht hat.

Der einfachere Weg: einmal **Reparieren** (Revive), bis das Gerät wieder bootet — danach **Daten sichern** — und erst dann die **Betriebssystemaktualisierung** ganz normal durchführen.
{{% /alert %}}

Reparieren und Wiederherstellen erfolgen seit macOS Sonoma im **Finder** eines zweiten Macs (siehe [DFU-Modus bei Apple Silicon](/wiki/docs/mac-repair/apple-silicon/dfu-mode-apple-silicon/)).

### Doppeltes Reparieren („Double Revive") — Fallback-Technik

Seit macOS 26.5 ist dieser Weg im Normalfall nicht mehr nötig. Er bleibt aber als Technik dokumentiert, weil er in ähnlichen Fällen weiterhelfen kann:

- Tritt ein **4042-Fehler** auf, kann ein **Reparieren mit einem früheren IPSW** helfen. IPSW-Dateien pro Modell: [ipsw.me](https://ipsw.me/product/Mac/) und [Apple-Silicon-IPSW-Datenbank (Mr. Macintosh)](https://mrmacintosh.com/apple-silicon-m1-full-macos-restore-ipsw-firmware-files-database/).
- Das **doppelte Reparieren** ist eine wichtige Technik für Randfälle, in denen ein Reparieren mit der neuesten Version nicht durchläuft.

Ablauf:

1. Gerät in DFU versetzen (siehe [DFU-Modus bei Apple Silicon](/wiki/docs/mac-repair/apple-silicon/dfu-mode-apple-silicon/)).
2. **Reparieren** (nicht Wiederherstellen!) mit einem **früheren IPSW** — eine Version zurück, zum Beispiel 26.4.0 oder das letzte 26.3.x. Das Reparieren installiert nur Firmware und recoveryOS neu; die APFS-Benutzervolumes bleiben unberührt.
3. Gerät bootet danach normal auf der früheren Firmware. Login-Screen abwarten.
4. **Erneut Reparieren** — diesmal mit dem aktuellen IPSW.
5. Daten sind erhalten; Gerät ist auf aktueller Firmware.

{{% alert title="Bestimmtes IPSW auswählen" color="info" %}}
Um ein anderes als das neueste IPSW zu wählen, beim Klick auf **Reparieren** (oder **Wiederherstellen**) die **Option**-Taste gedrückt halten und die Datei auswählen. Das Release-Datum des IPSW darf nicht vor dem Release-Datum des Macs liegen — ein älteres Image funktioniert schlicht nicht und scheitert mit einem Kompatibilitätsfehler.
{{% /alert %}}

## Was auf keinen Fall tun

- **Keine Wiederherstellung (Restore)** — Datenverlust *und* derselbe 4042-Fehler.
- **Logicboard nicht reparieren oder austauschen lassen.** Es gibt zahlreiche Berichte, dass selbst mehrere autorisierte Apple-Partner dieses Problem als "Hardware"-Defekt eingestuft haben. 800 € für ein neues Board plus Datenverlust sind in den allermeisten Fällen völlig unnötig.

## Im Zweifel: passende Werkstatt suchen

Wer sich unsicher ist, sollte eine Reparaturwerkstatt suchen, die dieses Problem kennt. Der übliche Preis für eine solche Reparatur sollte etwa auf dem Niveau einer System-Neuinstallation auf einem Windows- oder Mac-Gerät liegen — technisch ist es nahezu dasselbe.

## Weiterführende Links

- [logi.wiki](https://logi.wiki) — technische Artikel zur MacBook-Reparatur
- [repair.wiki](https://repair.wiki) — Community-Dokumentation zur Geräte-Reparatur
