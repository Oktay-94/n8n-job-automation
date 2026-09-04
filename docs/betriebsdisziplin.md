# Betriebsdisziplin

Ein System, das unbeaufsichtigt in Produktivdaten schreibt, braucht Regeln,
die auch dann greifen, wenn man müde ist. Diese hier sind alle aus Schaden
entstanden.

## Jede Zahl trägt ihre Herkunft

Jede ID, jede Zahl, jeder Pfad und jedes Datum bekommt eines von vier Etiketten:

| Etikett | Bedeutung |
|---|---|
| GEMESSEN | in dieser Sitzung erhoben, mit Befehl und Zeitpunkt |
| GEFOLGERT | aus einer Messung abgeleitet, Ableitung genannt |
| ANNAHME | plausibel, aber ungeprüft |
| NICHT MESSBAR | mit den verfügbaren Mitteln nicht feststellbar |

Ein Wert ohne Herkunft aus der laufenden Sitzung wird nicht hingeschrieben,
sondern gemessen. Der Grund ist banal: Workflow-IDs wandern bei jedem Tausch,
und ein aus dem Gedächtnis übernommener Wert zeigt zuverlässig auf die
Vorgängerfassung.

## Positivkontrollen vor jedem leeren Ergebnis

Ein Suchlauf, der nichts findet, beweist nichts — er kann auch schlicht falsch
gesucht haben. Vor jedem „0 Treffer" läuft dieselbe Suche gegen einen bekannten
Treffer. Findet sie den nicht, ist das Ergebnis ungültig, nicht sauber.

Eine Variante davon kostete zwei Durchläufe: Ein Ortsname wurde aus einem
Suchmuster genommen, weil er inzwischen legitim im Code stand. Er war zugleich
der einzige Ortsname in der Kontrolldatei. Der Zweig meldete zweimal null,
ohne je gesucht zu haben. Seitdem gilt: Wird ein Muster geändert, wird die
Kontrolldatei mitgeprüft.

## Secret- und PII-Scan vor jedem Push

Fünf Zweige über jede neue Datei einzeln — API-Schlüssel, Telefon, Straße,
Postleitzahl mit Ort, E-Mail. Jeder Zweig mit eigener Positivkontrolle. Ein
Zweig ohne Positivkontrolle gilt als nicht gelaufen.

Nach einer Entschärfung wird gegen die **Originalwerte** gezählt, nicht gegen
das Muster — ein Muster trifft seine eigenen Platzhalter und meldet damit
Erfolg, obwohl nichts entfernt wurde.

## Anker zählen vor jedem Schreibvorgang

Jede Textersetzung läuft über einen Trefferzähler mit harter Erwartung. Genau
ein Treffer, sonst wird nicht geschrieben. Zwei Treffer bedeuten, dass die
Ersetzung an einer zweiten, nicht bedachten Stelle zuschlagen würde.

## Rollentrennung

Der Mensch entscheidet, committet und pusht. Automatisierung misst, plant und
führt aus. Ein Schritt pro Nachricht, mit ausdrücklicher Freigabe vor jedem
schreibenden Lauf. Lesen ist die Voreinstellung.

## Der Stolperstein-Katalog

Über 240 nummerierte Einträge, jeder mit Symptom, Ursache und Gegenmittel.
Ein Auszug:

- Ein Wertimport aus einem `"use client"`-Modul stürzt zur Laufzeit ab, ohne
  dass Typprüfung oder Linter etwas melden — das Modul kommt serverseitig als
  Client-Referenz an
- `git status --porcelain` faltet unverfolgte Verzeichnisse zu einer Zeile
  zusammen; für Dateizahlen braucht es `-uall`
- Ein CSS-Element kann nie sein eigener Container für eine Container-Query sein
- Ein laufender Produktions-Build liefert alten Code aus; erkennbar daran, dass
  eine neue Route 404 statt 405 antwortet
- Erwartungswerte in einem Prüfblock sind selbst ungetesteter Code — wo eine
  Zahl gemessen werden kann, schlägt die Messung die Rechnung
