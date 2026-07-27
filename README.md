ISMANING FLIGHT RADAR

Start:
1. Öffne index.html in einem modernen Browser.
2. Für zuverlässige Browser-Anfragen empfiehlt sich ein kleiner lokaler Webserver:
   - Windows / macOS / Linux mit Python:
     python -m http.server 8080
   - Danach im Browser öffnen: http://localhost:8080

Funktionen:
- Live-Flugzeuge im Radius von 25, 50, 75 oder 100 NM um Ismaning
- Callsign, Typ, Registrierung, Flughöhe, Geschwindigkeit, Kurs und Squawk
- Automatische Aktualisierung alle 15 Sekunden
- Demo-Modus, falls die öffentliche API nicht erreichbar ist
- Responsive Darstellung für Desktop und Smartphone

Datenquelle:
- ADSB.lol API
- Kartenmaterial: OpenStreetMap

Hinweis:
Öffentlich empfangene ADS-B-Daten können unvollständig oder verzögert sein.
Die Anwendung ist nicht für Navigation, Einsatzführung oder sicherheitskritische Nutzung bestimmt.
Für eine produktive oder kommerzielle Nutzung sollte ein vertraglich abgesicherter Datenanbieter verwendet werden.
