# Workout PWA – Technische Dokumentation

## Zweck
Kostenloser Ersatz für die kostenpflichtige App "Home Workout" (40 €/Jahr).
Progressive Web App (PWA) – läuft offline auf dem iPhone als Home-Screen-Icon.

---

## Dateistruktur

```
Sportapp/
  index.html      ← Komplette App (HTML + CSS + JS + alle Bilder in einer Datei, ~413 KB)
  manifest.json   ← PWA-Konfiguration (App-Name, Icon, Vollbild-Modus)
  sw.js           ← Service Worker (Offline-Caching, Version: workout-v4)
  icon.png        ← App-Icon für den Home-Bildschirm (192×512px)
  README.md       ← Diese Datei
```

---

## App-Inhalt (index.html)

### Workouts
**ABS Advanced** – 21 Übungen:
Jumping Jacks (30s), Sit-Ups (×30), Side Bridges Left (×25), Side Bridges Right (×25),
Abdominal Crunches (×40), Bicycle Crunches (×24), Side Plank Right (30s), Side Plank Left (30s),
V-Up (×16), Push-Up & Rotation (×30), Russian Twist (×50), Abdominal Crunches (×40),
Butt Bridge (×30), Heel Touch (×40), Mountain Climber (×50), Crossover Crunch (×30),
V-Up (×16), Plank (60s), Cobra Stretch (30s), Spine Lumbar Twist Stretch Left (30s),
Spine Lumbar Twist Stretch Right (30s)

**Chest Advanced** – 16 Übungen:
Jumping Jacks (30s), Arm Circles (×20), Shoulder Stretch (30s), Burpees (×10),
Staggered Push-Ups (×16), Hindu Push-Ups (×16), Push-Up & Rotation (×16),
Diamond Push-Ups (×16), Box Push-Ups (×20), Hindu Push-Ups (×16),
Spiderman Push-Ups (×22), Decline Push-Ups (×16), Burpees (×10),
Shoulder Stretch (30s), Cobra Stretch (30s), Chest Stretch (30s)

### Ablauf pro Übung
1. **Pause-Screen** (30 Sek): Countdown mit Vorschau der nächsten Übung
2. Bei 0: Glockenklang (Boxing Bell via Web Audio API)
3. **Übungs-Screen**: Bild + Name + Wiederholungen oder Timer
4. Zeit-Übungen: laufen automatisch durch, Countdown 5–1 gesprochen (SpeechSynthesis)
5. Zähl-Übungen: großer "Fertig"-Button zum Weitertippen
6. Fortschrittsanzeige: "Übung X / 21"

### Bilder
- 29 Übungsbilder als Base64-JPEG direkt in index.html eingebettet
- Aus iPhone-Screenshots der Original-App extrahiert (PIL/Python, 330×270px pro Bild)
- Keine externen Bildquellen – funktioniert 100% offline

### Audio
- **Boxing Bell**: Web Audio API, 4 Sinus-Oszillatoren (440/880/1760/2640 Hz)
- **Countdown**: SpeechSynthesis API, Sprache de-DE, spricht 5–4–3–2–1 am Ende jeder Pause/Zeit-Übung

---

## Installation auf dem iPhone

### Erstinstallation
1. Mac und iPhone im **selben WLAN**
2. Auf dem Mac (via Claude Code oder Terminal):
   ```
   cd "/Users/karstenleipnitz/Library/Mobile Documents/com~apple~CloudDocs/Sportapp"
   python3 -m http.server 8080
   ```
3. Mac-IP ermitteln: `ipconfig getifaddr en0`
4. iPhone Safari öffnen → `http://[MAC-IP]:8080` aufrufen
5. Teilen-Button (□↑) → **"Zum Home-Bildschirm"** → Hinzufügen
6. Server kann danach wieder gestoppt werden (Strg+C im Terminal)

### Danach
Die App ist offline gecacht (Service Worker). Mac muss **nicht** laufen.
Der Server wird nur bei Neuinstallation (z.B. nach iPhone-Reset) benötigt.

### Aktuelle Mac-IP (Heimnetz)
`192.168.178.86` – kann sich nach Router-Neustart ändern, dann erneut prüfen.

---

## Service Worker
- Cache-Name: `workout-v4`
- Cached Files: `index.html`, `manifest.json`, `icon.png`
- Strategie: Cache-first, Fallback auf Netzwerk
- **Bei App-Updates**: Cache-Version in `sw.js` erhöhen (z.B. `workout-v5`), damit das iPhone die neue Version lädt

---

## Änderungshistorie
| Version | Änderung |
|---------|----------|
| v1–v2 | Initiale App, Bilder, Audio |
| v3 | Alle 29 Bilder korrigiert (falsche Koordinaten), Boxing Bell + Countdown 5–4–3–2–1 |
| v4 | Z-Index-Fix: "Fertig"-Button (z-index:2) liegt vor "Übung überspringen"-Button (z-index:1), Schatten wird nicht mehr überlagert |

---

## Technische Details
- Keine externen Abhängigkeiten, keine Frameworks, kein Build-Tool
- Alles in einer einzigen HTML-Datei
- Läuft auf iOS Safari (PWA-Standard)
- Offline-fähig nach erstem Aufruf
- Keine Nutzerdaten, kein Tracking, kein Account
