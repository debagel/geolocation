# Geo- und Karten-Unterstützung für [REDAXO](https://redaxo.org) 5.14+

Das Addon bündelt Funktionen für den Umgang mit Geo-Koordinaten: Tile-Proxy, Tile-Cache, Rechnen mit
Koordinaten auf PHP-Ebene, Karten mit Leaflet anzeigen.

Die Konfiguration ist flexibel und update- bzw. reinstallations-sicher individualisierbar. Die
Kartenanzeige mit LeafletJS kann mit eigenen JS-Schnipseln rasch und on the fly erweitert werden.

Kontakt: [Thomas Skerbis](https://github.com/skerbis)

![Titelbild](docs/assets/titel.jpg)

## Features:

- Backend
    - [Karten-URLs](docs/admin.md#tile) zu Karten-Anbietern mit weiteren Parametern inkl.
      Sprachunterstützung und Cache-Verhalten
    - [Kartensätze](docs/admin.md#mapset) zusammenstellen und verwalten, die auf einer oder mehreren
      Karten-Urls basieren
    - Datenverwaltung mit YForm
    - [Proxy-Server](docs/proxy_cache.md#proxy) für Karten-Abrufe vom Browser
    - [Cache](docs/proxy_cache.md#cache) für Karten-Abrufe
    - Verschleierung der tatsächlichen Karten-Url ggü. dem Client / Schutz der ggf. kostenpflichtigen
      appId´s z.B. von Google oder HERE
    - Kartendarstellung im Moduloutput mit demselben Code wie im Frontend
    - Ausführliche Dokumentation online im Backend bzw. auf GitHub

- Karten
    - LeafletJS als Kartensoftware integriert
    - Karten-HTML als [Custom-HTML-Element](#rm) `<rex-map .... ></rex-map>`
    - [Erweiterbare Tools](docs/devtools.md) zur Datendarstellung inkl. geoJSON (Basis, erweiterbar)
    - Anwendungsbeispiel für [Module](docs/devphp.md#module)
    - Einfache Einbindung des komprimierten JS / CSS im Frontend passend zur individuellen Konfiguration.

- Demo
    - [Stand-alone-Demo](docs/example/demo.html) zur Demonstration des Custom-HTML-Elements und des Cache
    - *redaxo/src/addons/geolocation/docs/example/demo.html* bitte ins REDAXO-Root kopieren wg. der Pfade

- [Cache](docs/proxy_cache.md#cache)
    - Je Tile-URL ein eigenes Verzeichnis, separat löschbar
    - [Cronjob](docs/proxy_cache.md#cron) zum Aufräumen
        - Dateien löschen, die älter sind als die Time-to-live der Tile-URL
        - weitere ältere Dateien löschen wenn das Verzeichnis zu viele Dateien enthält

- [Rechnen mit Koordinaten (PHP)](docs/devmath.md)
    - [class FriendsOfRedaxo\Geolocation\Calc\Point](docs/devmath.md#point): Koordinaten verwalten
        - verschiedene Format als Input/Output; Konvertierung
        - Distanzen und Richtung ermitteln
        - Zielpunkt aus Richtung und Distanz
    - [class FriendsOfRedaxo\Geolocation\Calc\Box](docs/devmath.md#box): Rechteckigen Bereich verwalten
        - Anlegen aus zwei gegenüberliegenden Ecken
        - Dynamisch erweitern
        - diverse Abfragen (north, south, ....)
        - Berechnete Werte (center, innerRadius, outerRadius)
    - [class FriendsOfRedaxo\Geolocation\Calc\Math](docs/devmath.md#math): Rechteckigen Bereich verwalten
        - diverse Methoden und Defaults basierend auf phpGeo
        - Distanzen und Richtung ermitteln
        - Zielpunkt aus Richtung und Distanz
        - Konvertierung (DezimalDegree nach Degree/Minute und Degree/Minute/Second)

## Was fehlt?

- Karten interaktiv gestalten (Bounding-Box auswählen, Marker setzen/verschieben, ...)
- Interaktion Karte und Eingabefeld
- Zu einer Adresse die Koordinate ermitteln
- Abfragen (z.B. Koordinate zu Adresse, Umkreissuche) cachen oder zumindest durch den Proxy leiten
- Umgang mit Links z.B. auf Marker/Icon-Bildchen in geoJSON-Datensätzen (cachen oder zumindest durch
  den Proxy leiten)
- .....

Ideen gibt es, Hilfe ist willkommen.

## Installation

In das Verzeichnis `redaxo/src/addons/geolocation` entpacken und in der Addon-Verwaltung die
Installation durchführen.

Dabei werden Demo-Daten (Links zu Tile-Servern und zwei Kartensätze installiert). Die Here-Kartenlinks
sind ohne die nötige "appId", die nach Registrierung bei [HERE](https://developer.here.com/) erzeugt
werden kann. Die vorgesehene Stelle in der URL ist mit `..........` markiert.

Der Cronjob für die Cache-Bereinigung hat die Einstellungen
- Einmal am Tag (04:30)
- Backend/Frontend
- Scriptanfang
- aktiviert

Details - auch zu individualisierten Installationen - stehen in der [Installationsanleitung](docs/install.md)

## OSMProxy ersetzten
OSM-Proxy-Nutzer müssen im Abschnitt Karten die gewünschten Tileserver hinterlegen und im Anschluss die URL für die Tiles im z.B. Leaflet JS ändern.
```
/index.php?geolayer=«id»&x={x}&y={y}&z={z}&r={r}
```(«id» steht für die Datensatz-ID der gewünschten Karte)


## Beispielmasken:

### Allgemeine Konfiguration

![Konfiguration](docs/assets/config.jpg)

### Kartensatz

![Kartensatz: Auflistung](docs/assets/maps_list.jpg)
![Kartensatz: Formular](docs/assets/maps_edit.jpg)

### Layer-/Tile-Server

![Tile-Layer: Auflistung](docs/assets/tiles_list.jpg)
![Tile-Layer: Formular](docs/assets/tiles_edit.jpg)

<a name="rm"></a>
### `<rex-map .... ></rex-map>`

Darstellung der Karte im Browser über ein [Custom-HTML-Element](docs/devphp#maphtml):

```html
<rex-map mapset="..." dataset="..."></rex-map>
```
Die Attribute sind zu Strings umgeformte Arrays/Objekte.

```html
<rex-map
    class="leaflet-map"
    mapset="[{&quot;tile&quot;:&quot;1&quot;,&quot;label&quot;:&quot;Karte&quot;,....}]"
    dataset="{&quot;position&quot;:[47.516669,9.433338],....}"
></rex-map>
```

Der JS-Code dazu ist ausbaufähig. Das HTML-Beispiel zeigt aber schon, wie mit setAttribute der Datensatz und die Kartenzusammenstellung ausgetauscht werden können.

Idee: komplexe interaktive Inputs für verschiedene Datensatz-Elemente in einer Web-Komponente gebündelt
```html
<rex-map-input class="..." name="..." value="«dataset»" tools="position,marker,bounds,..."></rex-map-input>
```


#### Datensatz

Die Karte wird über einen Datensatz gesteuert, der die relevanten Daten zur Karte enthält.

```javascript
datensatz = {
    position: [47.516669,9.433338],
    bounds: [[47.5,9.3],[47.7,9.7]],
    marker: [[47.611593,9.296344],[47.586204,9.560653],[47.54378,9.686559]],
}
```
Alle Angaben sind optional. Position ist der "Hauptmarker" in der Karte in rot. Alle anderen
zusätzlichen Marker sind blau. "Bounds" legt das Rechteck fest, dass auf jeden Fall in der
Karte sichtbar sein soll.

Grundidee: es gibt gleich strukturierte Klassen je "Tool", wobei alle Elemente im Datensatz (position,bounds,marker)
jeweils eine eigene Klasse haben. Die Klasse steuert die Anzeige auf der Karte toolspezifisch. Das System
ist einfach erweiterbar nur durch Bereitstellung einer neuen Toolklasse.


#### Kartensatz

Im Backend werden aus Layern(Tiles/Kacheln) Kartensätze zusammengestellt, die ihrerseits einen Datensatz bilden. Leaflet unterscheidet zwischen Basiskarten (type=b) und Overlays (type=o).
```javascript
kartensatz = [
    {
        "layer":"1",
        "label":"Karte",
        "type" :"b",
        "attribution":"Map Tiles &copy; 2020 <a href=\"http:\/\/developer.here.com\">HERE<\/a>",
        "active": true,
    },
    {
        "layer":"2",
        "label":"Satellit",
        "type" :"b",
        "attribution":"Map Tiles &copy; 2020 <a href=\"http:\/\/developer.here.com\">HERE<\/a>"
        "active": false,
    },
    {
        "layer":"3",
        "label":"Hybrid",
        "type" :"b",
        "attribution":"Map Tiles &copy; 2020 <a href=\"http:\/\/developer.here.com\">HERE<\/a>"
        "active": false,
    }
]
```

## Referenzen

Basiversion by [Christoph Böcker](https://github.com/christophboecker).

Inspiriert von [Thomas Skerbis](https://github.com/skerbis) Addon "[osmproxy](https://github.com/FriendsOfREDAXO/osmproxy)" und gefüttert mit Ideen und Snippets aus anderen Addons und aus den Diskussionen dazu auf GitHub und im [Slack-Channel](https://friendsofredaxo.slack.com/).

Kartendarstellung mit [LeafletJS](https://leafletjs.com/) von [Vladimir Agafonkin](https://agafonkin.com/) und weiteren
Tools (siehe [CREDITS](CREDITS.md)).

Für Rechenoperationen mit Koordinaten und Formatumwandlungen ist die PHP-Bibliothek
[phpGeo](https://github.com/mjaschen/phpgeo) von [Markus Jaschen](https://github.com/mjaschen) eingebaut.

Ideen-Beitrag für das [Geo](https://github.com/FriendsOfREDAXO/friendsofredaxo.github.io/issues/124)-Projekt von [Thomas Blum](https://github.com/tbaddade)
