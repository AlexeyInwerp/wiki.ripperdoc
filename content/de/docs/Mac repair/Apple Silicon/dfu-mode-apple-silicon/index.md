---
title: "Apple Silicon in den DFU-Modus versetzen — Tastenkombination und Methodik"
linkTitle: "DFU-Modus (Apple Silicon)"
date: 2026-05-31
weight: 9
description: "Die Tastenkombination, um einen Mac mit Apple Silicon in den DFU-Modus zu versetzen — mit zuverlässigem Timing, dem richtigen Port, Akku-Hinweisen und den Logs für Reparieren und Wiederherstellen."
categories: [macOS, Apple Silicon, Firmware]
tags: [DFU, revive, restore, Apple Configurator, IPSW]
---

Der DFU-Modus (Device Firmware Update) ist die Voraussetzung für jedes **Reparieren** (Revive) oder **Wiederherstellen** (Restore) eines Macs mit Apple Silicon. Anders als beim Recovery-Modus zeigt das Gerät dabei **kein Bild** — kein Apple-Logo, keine Anzeige, bei MagSafe auch keine Lade-LED. Das Display bleibt komplett schwarz; das ist normal und kein Defekt.

{{% alert title="Seit macOS Sonoma kein Apple Configurator mehr nötig" color="info" %}}
Seit **macOS Sonoma (14)** lässt sich ein Mac im DFU-Modus direkt über den **Finder** eines zweiten Macs reparieren oder wiederherstellen — Apple Configurator 2 wird nicht mehr benötigt. Der Host erkennt das Gerät im DFU-Modus und zeigt im Finder die Schaltflächen **„Mac reparieren"** (Revive) und **„Mac wiederherstellen"** (Restore). Configurator funktioniert weiterhin, ist aber nur noch für ältere Host-Systeme oder Batch-Abläufe nötig.
{{% /alert %}}

## Voraussetzungen

- Ein **zweiter Mac** (Host) mit **macOS Sonoma 14 oder neuer** für den Finder-Weg (oder [Apple Configurator 2](https://apps.apple.com/de/app/apple-configurator/id1037126344) auf älteren Systemen).
- Ein **USB-C-Kabel**, das **Daten und Laden** unterstützt (z. B. das Apple-USB-C-Ladekabel) — **kein** Thunderbolt-3-Kabel und kein reines Ladekabel.
- Verbindung mit dem **richtigen DFU-Port** am Zielgerät (siehe unten).
- Das Zielgerät muss **etwas geladen** sein (siehe Akku-Hinweis weiter unten).

## Die Tastenkombination und das Timing

Bei MacBooks mit Apple Silicon werden drei Tasten plus der Power-Knopf (Touch ID) verwendet:

![Tastenpositionen für den DFU-Modus: links Control und Option, rechts Shift, oben rechts der Power-/Touch-ID-Knopf](apple-dfu-keys.png)

- **Linke Control-Taste** (⌃)
- **Linke Option-Taste** (⌥)
- **Rechte Shift-Taste** (⇧)
- **Power-Knopf** (Touch ID, oben rechts)

Ablauf:

1. Gerät komplett **ausschalten**.
2. **Power + linke Control + linke Option + rechte Shift** gleichzeitig drücken und **genau 10 Sekunden** halten (bewusst mitzählen).
3. Nach exakt 10 Sekunden die **drei Tasten loslassen** — aber **den Power-Knopf weiter halten**.
4. Power-Knopf **weitere ca. 5–10 Sekunden** gedrückt halten, bis auf dem Host das große **DFU-Fenster** erscheint.
5. Erst dann den Power-Knopf loslassen.

{{% alert title="Das präzise Timing ist entscheidend (Methode von Mr. Macintosh)" color="warning" %}}
Die zuverlässige, mitgezählte 10-Sekunden-Methode stammt von Mac-Admin **Mr. Macintosh** und erreicht in der Praxis nahezu 100 % Erfolgsquote. Apples eigene Anleitung formuliert nur vage „etwa 10 Sekunden" — der entscheidende Trick ist das **genaue Zählen** statt Schätzen. Erscheint nach insgesamt ~20 Sekunden kein DFU-Fenster, abbrechen und neu beginnen.

Ausführliche Anleitung mit Screenshots: [Restore macOS Firmware on an Apple Silicon Mac + Boot to DFU Mode — mrmacintosh.com](https://mrmacintosh.com/restore-macos-firmware-on-an-apple-silicon-mac-boot-to-dfu-mode/)
{{% /alert %}}

## Welcher Port?

Nur **ein bestimmter Port** am Zielgerät ist der DFU-Port. Am falschen Port erscheint kein DFU-Fenster, egal wie sauber das Timing ist. Die Lage hängt vom Modell ab und **hat sich bei den 2025er-Modellen geändert** — Apple beschreibt die Position jeweils „mit Blick auf die linke Seite des Macs", also unter den USB-C-Ports auf der **linken Seite** des Geräts:

- **MacBook Air bis 2024 / die meisten MacBook Pro:** der **vordere** der beiden linken Ports (Apple: „der linke USB-C-Port, mit Blick auf die linke Seite").
- **MacBook Air ab 2025 sowie 14" MacBook Pro mit M4/M5-Basis:** der **andere** der beiden linken Ports (Apple: „der rechte USB-C-Port, mit Blick auf die linke Seite") — **nicht** die rechte Geräteseite, sondern der zweite Port links.

Apple listet die exakte Position pro Modell: [DFU-Port identifizieren — support.apple.com](https://support.apple.com/de-de/120694).

{{% alert title="Im Zweifel: Port wechseln" color="info" %}}
Erscheint nach einem sauberen 10-Sekunden-Versuch kein DFU-Fenster, ist meist nur der Port falsch — den anderen Port am Gerät verwenden und erneut versuchen. Es muss ein **USB-C-Daten- und Ladekabel** sein, kein reines Ladekabel und kein Thunderbolt-3-Kabel.
{{% /alert %}}

## Akku muss geladen sein — und der „pre-SMC"-Trick

Ein Reparieren oder Wiederherstellen funktioniert **nicht mit komplett leerem Akku** — das Gerät muss etwas geladen sein. Tückisch wird es, wenn ein Gerät im **pre-SMC-Zustand** festhängt: Dann lädt es am Kabel gar nicht und scheint „tot".

Der Ausweg: **Trotzdem ein Reparieren starten.** Auch wenn das Reparieren wegen Timeout fehlschlägt, lädt das Gerät währenddessen eine **RAMDisk mit funktionsfähigem SMC-Teil**. Dadurch kann der Mac wieder laden. Gerät anschließend **am Kabel liegen lassen**, etwas laden lassen und das Reparieren erneut versuchen — jetzt mit ausreichend Ladung.

## Detaillierte Logs in der Konsole (Console.app)

Wird das Reparieren/Wiederherstellen aus dem **Finder** ausgeführt, schreibt macOS auf dem Host einen **sehr detaillierten Log**. In der App **Konsole** (Console.app) lässt sich der gesamte Ablauf live mitlesen und nach dem Versuch auswerten.

Diese Logs sind für eine **saubere Diagnose** Gold wert: Sie zeigen exakt, in welcher Phase ein Vorgang scheitert — Firmware-Write, der Übergang (Transition) oder ein USB-Problem — und liefern den konkreten Fehlercode (z. B. den [4042-Fehler im Transition-Schritt](/wiki/docs/mac-repair/apple-silicon/macbook-update-26-4-1-dfu-4042/)). Ohne diese Logs ist man auf Raten angewiesen; mit ihnen lässt sich gezielt entscheiden, ob ein erneutes Reparieren, ein anderes IPSW oder eine Hardware-Maßnahme nötig ist.

## Technischer DFU-Modus ohne funktionierende Tastatur (Jumper-Methode)

Lässt sich das Gerät nicht über die Tastatur in DFU bringen — etwa bei defekter Tastatur, abgeklemmtem Top-Case oder einem Logicboard auf der Bank — kann das **FORCE_DFU**-Signal auf dem Board direkt ausgelöst werden. Das Signal wird auf der Platine auf die passende Spannung (PP1V25 / 1,25 V) gezogen; bei den meisten Boards ist der Pull-up-Widerstand nicht bestückt, daher muss man nach Schaltplan vorgehen. Diese Methode funktioniert komplett ohne Tastatur und ist im Reparatur-Alltag deutlich zuverlässiger.

Board-spezifische Jumper-Punkte und Schaltplan-Hinweise:
[DFU Mode Restore (Macs) — logi.wiki](https://logi.wiki/index.php?title=DFU_Mode_Restore_(Macs))

## Reparieren vs. Wiederherstellen

Sobald das DFU-Fenster erscheint, stehen zwei Aktionen zur Wahl:

- **Reparieren** (Revive) — installiert nur Firmware und recoveryOS neu. **Benutzerdaten bleiben erhalten.** Erste Wahl bei Firmware-Problemen nach einem Update.
- **Wiederherstellen** (Restore) — löscht das gesamte Gerät und installiert es komplett neu. **Alle Daten gehen verloren.** Nur, wenn das Gerät ohnehin gelöscht werden soll.

Ein praktischer Anwendungsfall für das Reparieren findet sich hier: [MacBook-Update-Probleme nach macOS 26.4.1 (DFU-Fehler 4042)](/wiki/docs/mac-repair/apple-silicon/macbook-update-26-4-1-dfu-4042/).

## Offizielle Apple-Anleitung

- [Firmware eines Mac reparieren oder wiederherstellen (Finder) — support.apple.com](https://support.apple.com/de-de/108900)
- [DFU-Port identifizieren — support.apple.com](https://support.apple.com/de-de/120694)
- [Mac mit Apple Silicon mit Apple Configurator wiederherstellen — support.apple.com](https://support.apple.com/guide/apple-configurator-mac/revive-or-restore-a-mac-with-apple-silicon-apdd5f3c75ad/mac)

## Weiterführende Links

- [logi.wiki](https://logi.wiki) — technische Artikel zur MacBook-Reparatur
- [repair.wiki](https://repair.wiki) — Community-Dokumentation zur Geräte-Reparatur
