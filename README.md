# MetalDrive IT-Support-Agent

## Projektziel
Automatisierung des IT-Support-Prozesses bei der MetalDrive GmbH (Tier-1-Automotive, ca. 80 Mitarbeitende). Vorher: unkontrollierte Kanäle (E-Mail, Teams, mündlich, Post-it), ~90 % der Vorgänge nicht dokumentiert, kein Ticketsystem, keine Priorisierung. Ziel: jeder Mitarbeiter erhält schnelle, zuverlässige IT-Unterstützung ohne Wartezeiten und ohne Zufallsprinzip.

## Verwendete Tools
- n8n (Docker)
- Ollama (lokal) mit qwen2.5:14b
- Gmail (OAuth2)
- Google Sheets

## Workflow
1. **Gmail Trigger** – prüft jede Minute ein gelabeltes Postfach auf neue Support-Mails
2. **Code (JavaScript)** – extrahiert Absender, Betreff, Inhalt, Zeitstempel, Message-ID
3. **Basic LLM Chain (qwen2.5:14b)** – klassifiziert die Anfrage in Kategorie, Dringlichkeit und Zusammenfassung, Ausgabe als JSON
4. **Code (JavaScript)** – parst die JSON-Antwort und führt sie mit den Rohdaten zusammen
5. **IF-Node** – prüft Dringlichkeit
   - Hoch → sofortige interne Eskalations-Mail
   - sonst → direkt weiter zu Schritt 6
6. **Google Sheets** – legt automatisch ein Ticket mit Ticket-ID, Datum, Absender, Kategorie, Dringlichkeit, Status "OFFEN" an
7. **Gmail** – sendet dem Absender eine automatische Eingangsbestätigung mit Ticket-ID

**Klassifikationsregeln Dringlichkeit:**
- Hoch: Produktionsstopp möglich, mehrere Mitarbeiter betroffen, kein Workaround
- Mittel: Einzelperson betroffen, Workaround vorhanden
- Niedrig: Komfortproblem, kein Zeitdruck

## Screenshots
*(hier Screenshot des n8n-Workflows einfügen – per Drag & Drop direkt im GitHub-Editor)*

## Herausforderungen
- DSGVO-Konformität sicherstellen (Rechtsgrundlage Art. 6, Informationspflicht Art. 13, AV-Vertrag mit Google)
- Einordnung nach EU AI Act (Ergebnis: Risikoklasse MINIMAL)
- Make-or-Buy-Entscheidung: Hybrid-Lösung (lokales LLM via Ollama) zur Vermeidung von Vendor Lock-in und für volle Datenkontrolle
- Akzeptanz im Unternehmen über Führungskoalition, Key User und Kommunikation nach dem SCARF-Modell sichern

## Lernerfahrungen
- Saubere Datenextraktion und JSON-Parsing sind kritisch für die Stabilität des gesamten Workflows
- Ein Hybrid-Ansatz (lokal betriebenes LLM) liefert volle Datenkontrolle bei niedrigen Kosten – auch praktikabel für KMU
- Erfolgreiche KI-Projekte scheitern selten an der Technik, sondern an fehlender Dokumentation, unklarer Verantwortung und fehlender organisatorischer Verankerung
- Aufgebaute Kompetenzen (n8n, Prompt Engineering, Governance) sind direkt auf weitere Use Cases übertragbar (z. B. geplantes Folgeprojekt: Materialstammdaten-Prozesse)

## Status
Vollständig umgesetzt und produktiv (Published in n8n, alle Nodes erfolgreich getestet).

## Dateien in diesem Repository
- `workflow.json` – exportierter n8n-Workflow
- `MetalDrive_KI_Abschluss.pdf` – Abschlusspräsentation (Kotter-Phasenmodell)
