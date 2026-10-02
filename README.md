# WohnungsSwipe – Cuzimgamer2 Fork

Dieser Fork basiert auf **Nicgeon/wohnungsswipe** und enthält zusätzliche Funktionen und Anpassungen für den eigenen Betrieb.

## Zusätzliche Änderungen gegenüber dem ursprünglichen Projekt

### Suche & Scraping

- Einmaliges Scrapen einer kompletten Suchergebnisseite über **„Erste 10 Inserate dieser Suche laden“**.
- Neue API: `POST /api/search/scrape`.
- Der Scraper verarbeitet mehrere Inserate nacheinander statt nur ein einzelnes Inserat.
- Fehler bei einzelnen Inseraten werden nicht mehr still verworfen:
  - fehlgeschlagene Inserate werden protokolliert
  - weitere Kandidaten werden anschließend versucht
  - die API liefert erfolgreiche und fehlgeschlagene Scrapes zurück
- Retry-Mechanismus für einzelne Inserate.
- Kleine Wartezeiten zwischen Scrape-Versuchen, um die Portale nicht unnötig aggressiv anzufragen.
- Immowelt-Suchergebnisse werden zusätzlich aus eingebettetem Script-/JSON-State erkannt.
- Immowelt-Galerien werden gezielt aus `gallery.images` ausgelesen.
- Immowelt-Bilder werden nicht mehr nur anhand einer Dateiendung erkannt, da CDN-URLs auch ohne sichtbare `.jpg`/`.png`-Endung vorkommen können.
- Mehrere Bildquellen eines Inserats werden zusammengeführt und dedupliziert.
- Immowelt-URL- und Gallery-RegEx wurden für aktuelle eingebettete Daten angepasst.
- Die Scrape-Antwort enthält Anzahl erfolgreicher und fehlgeschlagener Inserate.

> Hinweis: Ein Scraper kann ein Inserat nicht garantieren abrufen, wenn ein Portal selbst den Zugriff blockiert, ein Inserat entfernt wurde oder die Seite keine verwertbaren Daten ausliefert.

### Bewertungen

- Bewertete Inserate können in der Bewertungsansicht einzeln ausgewählt werden.
- **„Alle auswählen“** wählt alle aktuell sichtbaren bewerteten Inserate aus.
- Mehrere Bewertungen können gesammelt gelöscht werden.
- Neue API: `DELETE /api/listings/rated`.
- Das Löschen entfernt nur die Bewertung des aktuellen Benutzers; das Inserat selbst bleibt für andere Benutzer erhalten.

### Benutzer-/Profilmenü

- Benutzername und Logout wurden zu einem gemeinsamen Dropdown-Menü zusammengeführt.
- Das Menü enthält zusätzlich den Profil-/Einstellungszugriff.
- Klick außerhalb des Menüs schließt das Dropdown.

### Designs

Es gibt vier auswählbare Designs:

- **Standard** – ursprüngliches Erscheinungsbild
- **Hell** – helles Theme
- **Dunkel** – dunkles Theme
- **Anti-AI** – bewusst schlichtes, professionelles Interface mit reduzierten Rundungen, Schatten und visuellen Effekten

Die Theme-Auswahl wird lokal im Browser gespeichert und auf die gesamte Oberfläche angewendet.

### Such-URL-Hilfen

Die Portal-Hinweise im Bereich der Suchagenten sind anklickbar und öffnen die jeweilige Portal-Seite in einem neuen Tab:

- Kleinanzeigen
- ImmobilienScout24
- Immowelt
- Rentola
- meinestadt

### Detailansicht / Bilder

- Die bestehende Detail-Galerie wurde durch robustere Bildsammlung im Scraper unterstützt.
- Mehrere Bild-URLs werden in `images_json` gespeichert.
- Hauptbild, Thumbnails, Navigation und Bildzähler können damit auf die komplette Galerie zugreifen.

## API-Erweiterungen

### `POST /api/search/scrape`

Startet einen einmaligen Scrape einer Such-URL.

Beispiel:

```json
{
  "url": "https://www.immowelt.de/classified-search?...",
  "limit": 10
}
```

Die Antwort enthält unter anderem:

```json
{
  "success": true,
  "found": 10,
  "successful": 10,
  "failed": [],
  "added": 10,
  "updated": 0,
  "listings": []
}
```

### `DELETE /api/listings/rated`

Entfernt mehrere Bewertungen des aktuell angemeldeten Benutzers.

Beispiel:

```json
{
  "listingIds": [12, 15, 18]
}
```

## Betrieb mit Docker

Das Projekt verwendet weiterhin Docker Compose.

In der aktuellen Serverumgebung wird der Container intern auf Port `3000` betrieben und am Host auf **Port 3001** veröffentlicht, weil Port 3000 bereits von einem anderen Dienst verwendet wird.

Beispiel:

```yaml
ports:
  - "3001:3000"
```

### Aktualisieren

```bash
cd /opt/wohnungsswipe
git pull
docker-compose up -d --build
```

Bei Docker Compose V1 (`1.29.2`) kann beim Recreate der Fehler `KeyError: 'ContainerConfig'` auftreten. Dann:

```bash
docker-compose down
docker rm -f wohnungsswipe 2>/dev/null || true
docker-compose up -d --build
```

**Nicht `docker-compose down -v` verwenden**, da dadurch das Daten-Volume entfernt werden kann.

## Änderungsprinzip

Neue Änderungen in diesem Fork sollen jeweils:

1. in einem eigenen, verständlich benannten Commit landen,
2. eine Commit-Message enthalten, die konkret beschreibt, was geändert wurde,
3. bei größeren Funktionen in dieser README dokumentiert werden.

## Basisprojekt

Originalprojekt: https://github.com/Nicgeon/wohnungsswipe

Fork: https://github.com/Cuzimgamer2/wohnungsswipe_cuzi
