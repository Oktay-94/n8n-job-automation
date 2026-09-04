# Workflow-Katalog

28 aktive Workflows auf einer selbst gehosteten n8n-Instanz.
Knotenzahlen und Auslöser sind gemessene Werte aus dem Produktivbetrieb.

## Bewerbungs-Kette — Erzeugen, Bündeln, Versand

| Workflow | Knoten | Auslöser | Zweck |
|---|---|---|---|
| Anschreiben erzeugen | 41 | Execute-Workflow | LLM-Lauf mit Profil- und Stellenkontext, Gate gegen tote Anzeigen |
| Lebenslauf anhängen | 6 | Execute-Workflow | PDF-Bündel aus Anschreiben, Lebenslauf und Zeugnissen |
| Cockpit-Aktionen | 52 | Webhook | zentrale Aktions-Drehscheibe der Oberfläche |
| Manuelles Vollbündel | 14 | Webhook | kompletter Durchlauf für handaufgenommene Stellen |
| Cockpit-Restamp | 17 | Webhook | Statuswechsel mit Schutz gegen Doppelversand |
| Cockpit-Rerender | 26 | Webhook | Brief neu setzen aus gespeichertem Text, ohne LLM-Aufruf |
| Cockpit-Erkundung | 7 | Webhook | startet den Browser-Agenten zur Firmenrecherche |

Der Rerender existiert, weil eine nachgetragene Anrede kein neues Anschreiben
rechtfertigt. Der gespeicherte Text wird lediglich neu gesetzt — kein
LLM-Aufruf, keine Kosten, kein verändertes Schreiben.

## Nachschub, Anreicherung, Bewertung

| Workflow | Knoten | Auslöser | Zweck |
|---|---|---|---|
| Nachschub-Motor (BA) | 13 | täglich 09:00 | Bundesagentur, REST v6 |
| Nachschub-Motor (Adzuna) | 12 | täglich 09:15 | zweigeteilt: Umkreis 30 km und Remote 600 km |
| Volltext-Anreicherung | 10 | Execute-Workflow | Beschreibungstext nachladen |
| Stellen bewerten (Stufe 3) | 13 | Execute-Workflow | Erstbewertung gegen das Bewerberprofil |
| Stellen nachbewerten | 13 | täglich 10:00 | Zweitmeinung mit anderem Modell |
| Geo-Nachtrag | 7 | täglich 10:30 | Ortskoordinaten für die Umkreissuche |

Bei der BA-Umstellung auf v6 fielen mehrere Fallen an: der Parameter
`angebotsart=1` ist Pflicht, sonst sind 18 von 25 Treffern Ausbildungsplätze.
Die Trefferliste heißt `ergebnisliste`, nicht mehr `stellenangebote`. Ein
Homeoffice-Filter auf API-Ebene liefert null Treffer bei über 90.000
Gesamttreffern und wird deshalb im Code ausgewertet statt in der Abfrage.

## Wächter

| Workflow | Knoten | Auslöser | Zweck |
|---|---|---|---|
| Frische-Wächter | 20 | täglich 07:00 | prüft live, ob Anzeigen noch leben: tot / lebt / unsicher |
| Rückmeldungs-Tracker | 32 | stündlich | ordnet eingehende Antworten Stellen zu |
| Bounce-Wächter | 8 | alle 15 Min | erkennt unzustellbare Bewerbungen |
| E-Mail-Scout | 10 | täglich 11:00 | sucht Bewerbungsadressen zu Anzeigen ohne Kanal |
| Zufluss-Wächter | 4 | täglich 11:30 | meldet Quellen ohne Zugänge |

Der Rückmeldungs-Tracker kennt eine Rangfolge: Eingangsbestätigung (1) rangiert
unter Einladung (2), Zusage und Absage stehen gleichauf (3). Ein niedriger Rang
überschreibt einen höheren nie — sonst degradiert die automatische
Eingangsbestätigung eine bereits erkannte Einladung. Eine unbekannte Kategorie
schreibt grundsätzlich nicht.

## Dokumenten-Pipeline

| Workflow | Knoten | Auslöser | Zweck |
|---|---|---|---|
| Dokumentenverarbeitung | 36 | alle 2 Min | OCR, Typerkennung, Ablage |
| Dokument-Löschsweep | 6 | alle 2 Min | atomares Löschen inklusive Vektoren und Protokoll |
| Auto-Vektorisierung | 6 | alle 10 Min | Embeddings für die semantische Suche |
| Fakten-Sammler | 2 | alle 10 Min | Entitäten und Verknüpfungen |
| Befund-Wächter | 2 | alle 15 Min | Auffälligkeiten in Dokumenten |
| Befund-Nachprüfer | 2 | alle 15 Min | zweite Stufe mit stärkerem Modell |
| Einkommen-Sync | 2 | alle 15 Min | Kennzahlen fortschreiben |
| Frist-Intelligenz | 2 | alle 30 Min | erkennt Fristen im Volltext |
| Dokument-Ablaufwächter | 6 | täglich 09:00 | Eskalation 30 / 7 / 1 Tage vorher |

## Chat

| Workflow | Knoten | Auslöser | Zweck |
|---|---|---|---|
| Chatbot Dokumente | 7 | Chat-Trigger | Fragen an den eigenen Bestand, strikt lesend |
