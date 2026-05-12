---
title: "Steam Deck Display bleibt schwarz – Ton oder Vibration funktionieren noch"
linkTitle: "Steam Deck: Display schwarz, Ton/Vibration aktiv"
date: 2026-05-12
author: Daniel Haftlmeier
weight: 10
description: "Wenn das Steam Deck kein Bild mehr zeigt, aber Ton, Lüfter oder Vibration noch funktionieren, liegt häufig ein Firmware- oder BIOS-Problem vor. Schritt-für-Schritt-Anleitung zu Diagnose, Firmware-Recovery und BIOS-Update."
categories: [Steam Deck, Firmware, Display]
tags: [Steam Deck, recovery, BIOS, display, schwarzer Bildschirm, firmware]
---

Wenn dein Steam Deck kein Bild mehr auf dem internen Display zeigt, aber noch startet (Ton, Lüfter, Vibration oder Haptik funktionieren), liegt häufig ein Firmware- oder BIOS-Problem vor.

## 1. Externen Monitor testen

Zuerst prüfen, ob das Steam Deck noch ein Bild über USB-C ausgibt.

**Benötigt:**

- USB-C zu HDMI Adapter oder Dockingstation
- Monitor oder Fernseher

**Vorgehen:**

1. Steam Deck vollständig ausschalten
2. Dock/Adapter anschließen
3. Monitor verbinden
4. Steam Deck einschalten
5. Bis zu 2 Minuten warten

**Ergebnis:**

- **Bild vorhanden:** Mainboard funktioniert wahrscheinlich noch → weiter mit den nächsten Schritten
- **Kein Bild:** Mögliches Hardware- oder BIOS-Problem

## 2. Firmware-Recovery durchführen

**Wichtig:** Bei diesem Vorgang bleiben alle Daten und Spiele erhalten.

1. Steam Deck komplett ausschalten
   → Power-Taste ca. 10 Sekunden halten
2. Gleichzeitig gedrückt halten:
   - Lautstärke (-)
   - „...“ Taste
3. Währenddessen Power-Taste einmal kurz drücken
4. Warten bis:
   - die weiße LED blinkt
   - oder ein Ton hörbar ist
5. Tasten loslassen
6. Bis zu 15–20 Minuten warten
   → Das Display kann dabei schwarz bleiben

**Wichtig:** Dieser Vorgang benötigt manchmal mehrere Versuche.

Falls weiterhin kein Bild erscheint:

- Power-Taste erneut ca. 10 Sekunden gedrückt halten
- Gerät ausschalten lassen
- Den gesamten Vorgang erneut durchführen

## 3. BIOS aktualisieren (falls externer Monitor funktioniert)

Einige Geräte benötigen mindestens BIOS-Version **F7A0131**.

**Schritte:**

1. Steam → Einstellungen → System
2. „Developer Mode“ aktivieren
3. Im neuen „Developer“-Menü:
   - „Show Advanced Update Channels“ aktivieren
4. Unter System:
   - OS Update Channel auf **Main** stellen
5. „Apply“ drücken und Neustart abwarten

Der Neustart kann bis zu 15 Minuten dauern.

## 4. Recovery nach BIOS-Update erneut ausführen

Nach dem BIOS-Update:

1. Steam Deck vollständig ausschalten
   → Power-Taste 15 Sekunden halten
2. Externen Monitor entfernen
3. Wieder:
   - Lautstärke (-)
   - „...“ Taste halten
   - Power einmal drücken
4. Auf blinkende LED warten
5. Wieder bis zu 15–20 Minuten warten

Falls weiterhin kein Bild erscheint:

- Gerät erneut mit 10 Sekunden Power-Taste ausschalten
- Recovery-Vorgang erneut versuchen

Danach funktioniert das interne Display bei vielen Geräten wieder.

---

Falls Sie bei der Ausführung Probleme haben, können Sie sich auch gerne [an uns wenden](https://www.ripperdoc.de/kontakt/).
