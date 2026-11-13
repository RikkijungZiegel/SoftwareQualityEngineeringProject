# 1. Requirements Elicitation & Clarification

## 1.1 Open Questions & PO Clarification

| ID | Offene Frage / Unklarheit | Klärung mit PO | Auswirkung auf Anforderung |
| :--- | :--- | :--- | :--- |
| `Q-01` | In welcher Form und Präzision werden Geldbeträge verarbeitet? | Geldbeträge werden ausschließlich als Ganzzahlen in Cents (keine Floating-Point-Werte) verarbeitet. | `REQ-005`, `REQ-006` |
| `Q-02` | Wie hoch ist das Überziehungslimit (Overdraft Limit) für spezielle Kunden? | Das Limit ist Verhandlungssache zwischen Kunde und Bank und muss nachträglich änderbar sein. | `REQ-007, `REQ-017` |
| `Q-03` | Wer darf den Report für einen einzelnen Kunden generieren? | Die Business-Logik wird ausschließlich vom Bankpersonal ausgeführt; für dieses gibt es keine Einschränkungen. | `REQ-008`, `REQ-009` |
| `Q-04` | Darf ein Kunde seinen eigenen Report selbst einsehen? | Ja, der Kunde darf einen erstellten Report einsehen, jedoch darf nur Bankpersonal den Report generieren. | `REQ-008` |
| `Q-05` | Welches Format und welche Validierungsregeln gelten für die Account ID? | Exakt 8 Ziffern, bankweit eindeutig und ungleich `00000000`.| `REQ-010` |
| `Q-06` | Gibt es ein maximales Transaktionslimit pro Abhebung? | Nein, ein separates Einzelabhebungslimit existiert nicht; die Obergrenze bildet der Kontostand inkl. eines etwaigen Overdraft-Limits. | `REQ-005` |
| `Q-07` | Gibt es ein maximales Transaktionslimit pro Einzahlung? | Nein, Einzahlungen sind ohne betragsmäßige Obergrenze jederzeit möglich. | `REQ-006` |

---

## 1.2 Final Requirements Specification

> **Legende:**  
> **F** = Funktionale Anforderung | **NF** = Nicht-funktionale Anforderung | **A-REQ** = Architekturrelevante Anforderung  

1. **`REQ-001` (F):** Das System muss es einem Bankmitarbeiter ermöglichen, ein neues Konto für einen Kunden ohne Startguthaben ($0{,}00\text{ EUR}$) zu eröffnen.
2. **`REQ-002` (F):** Das System muss es einem Bankmitarbeiter ermöglichen, ein neues Konto für einen Kunden mit einem frei wählbaren, positiven Startguthaben zu eröffnen.
3. **`REQ-003` (F):** Das System muss auf Anfrage die Konto-Details (Konto-ID, Kundenname und aktueller Kontostand) eines spezifischen Kontos vollständig und korrekt zurückgeben.
4. **`REQ-004` (F):** Das System muss das Schließen eines bestehenden Kontos ermöglichen, sofern der Kontostand exakt $0{,}00\text{ EUR}$ beträgt.
5. **`REQ-005` (F):** Das System muss das Abheben von Geldbeträgen erlauben, sofern der Betrag strikt positiv ist (Euro und Cents) und der Kontostand nach der Abhebung nicht unter $0{,}00\text{ EUR}$ fällt (außer bei gewährtem Overdraft-Limit).
6. **`REQ-006` (F):** Das System muss das Einzahlen von Geldbeträgen auf ein Konto erlauben, sofern der einzuzahlende Betrag strikt positiv ist (Euro und Cents).
7. **`REQ-007` (F / A-REQ):** Das System muss spezialisierten Kunden ein individuelles Überziehungslimit (Overdraft-Limit) gewähren können, sodass Abhebungen bis zu einem negativen Saldo in Höhe dieses Limits erfolgreich ausgeführt werden.
8. **`REQ-008` (F / A-REQ):** Das System muss auf Anfrage einen Gesamtbericht für einen spezifischen Kunden generieren, der alle zugeordneten Konten mit Einzel- und Gesamtsaldo auflistet.
9. **`REQ-009` (F / A-REQ):** Das System muss auf Anfrage einen Gesamtbericht über die gesamte Bank generieren, der alle existierenden Konten aller Kunden mit dem Gesamtsaldo der Bank zusammenfasst.
10. **`REQ-010` (F / A-REQ):** Das System muss jede Konto-ID vor der Erstellung auf ein valides Format (exakt 8 Ziffern, ungleich `00000000`, bankweit eindeutig) prüfen und ungültige IDs mit einer Fehlermeldung ablehnen.
11. **`REQ-011` (NF / A-REQ):** Das System darf keine dauerhafte Datenbank-Persistenz nutzen; die Datenhaltung muss über ein gemocktes Datenbank-Interface isoliert und testbar sein.
12. **`REQ-012` (NF / A-REQ):** Das System muss ein einfaches Befehlszeilen-Interface (CLI-Frontend) bereitstellen, über das alle Kernfunktionen interaktiv ausgeführt werden können.
13. **`REQ-013` (F / A-REQ):** Das System muss jeden Kunden über eine eindeutige Kunden-ID verwalten, wobei ein Kunde mehreren Konten zugeordnet sein kann.
14. **`REQ-014` (F):** Das System muss Transaktionen (Ein- und Auszahlungen) mit einem Betrag von exakt $0{,}00\text{ EUR}$ als ungültig ablehnen.
15. **`REQ-015` (F / A-REQ):** Das System muss Operationen auf nicht existierende Konto-IDs abfangen und mit einer definierten Fehlermeldung (`AccountNotFoundException`) quittieren.
16. **`REQ-016` (F / A-REQ):** Das System muss Operationen auf nicht existierende Konto-IDs abfangen und mit einer definierten Fehlermeldung (`AccountNotFoundException`) quittieren.
17. **`REQ-017` (F):** Das System muss es dem Bankpersonal ermöglichen, ein bestehendes Überziehungslimit (Overdraft-Limit in Cents) eines Kontos nachträglich anzupassen (zu erhöhen oder zu reduzieren), sofern der aktuelle Kontostand das neue Limit nicht unterschreitet.
18. **`REQ-018` (F):** Das System muss eine Abhebung mit einer spezifischen Fehlermeldung ablehnen und den Kontostand unverändert lassen, sofern der abzuhebende Betrag das verfügbare Guthaben inklusive eines etwaigen Overdraft-Limits übersteigt.
19. **`REQ-019` (F):** Das System muss die Eröffnung eines Kontos mit einem negativen Startguthaben ($< 0\text{ Cents}$) als ungültig ablehnen.
20. **`REQ-020` (F):** Das System muss die Vergabe oder Änderung eines Overdraft-Limits mit einem negativen Betrag ($< 0\text{ Cents}$) als ungültig ablehnen.

---
## 1.3 SQE-Maßnahmen: Requirements Inspection & Verification

In Phase 1 wurde als primäre SQE-Maßnahme eine formelle **Requirements Inspection** (angelehnt an das Fagan-Inspection-Verfahren) auf Basis der ursprünglichen Aufgabenstellung durchgeführt. Ziel war es, Unvollständigkeiten, Mehrdeutigkeiten und fehlende Testbarkeiten vor dem Architekturentwurf frühzeitig zu identifizieren und zu beheben (Early Testing Principle).

### 1.3.1 Durchgeführte SQE-Aktion
1. **Methode:** Formelles Peer-Review / Inspection der initialen Anforderungen unter Verwendung einer Prüfliste nach **ISO 29148** Kriterien (Prüfkriterien: *Unambiguous*, *Complete*, *Consistent*, *Verifiable*, *Feasible*).
2. **Teilnehmer & Rollen:** Gesamtes Entwicklungsteam (Rollen: Moderator, Reader, Inspector). Die finale Verantwortung und Ownership für die Requirements-Anpassungen verblieb beim Requirements Engineer / Product Manager.
3. **Prüfgegenstand:** Requirements Baseline v0.1 (Gegenüberstellung der ungenauen Initial-Spezifikation aus der Aufgabenstellung mit der finalen SMART-Spezifikation).

### 1.3.2 Inspection Protocol (Review-Protokoll)

| Defect-ID | Relevante Anforderung (Ziel-REQ) | Befund / Identifizierter Mangel (aus der Ursprungs-Aufgabenstellung) | Schweregrad | Ergriffene Korrekturmaßnahme | Auswirkung auf Finale Requirement Baseline |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `DEF-REQ-01` | `REQ-005`, `REQ-006`, `REQ-015` | **Mehrdeutigkeit in der Angabe:** Die ursprüngliche Anforderung *"deposit/withdraw money (euros and cents, no negative money)"* ließ offen, wie exakt $0\text{ Cents}$ behandelt wird und wie Gleitkomma-Rundungsfehler vermieden werden. | Hoch | Nach PO-Klärung (`Q-01`): Strikte Ganzzahl-Arithmetik in Cents (keine Floating-Point-Werte) und Validierung auf Beträge $> 0\text{ Cents}$. | `REQ-005` & `REQ-006` auf Ganzzahl-Cents präzisiert; `REQ-015` für Nullbeträge ergänzt. |
| `DEF-REQ-02` | `REQ-004`, `REQ-014` | **Unvollständigkeit in der Angabe:** Die ursprüngliche Anforderung *"close account"* enthielt keine Regelung für Konten mit verbleibendem Guthaben oder negativen Salden. | Mittel | Einführung einer strikten Pre-Condition: Ein Konto darf ausschließlich bei einem exakten Kontostand von $0\text{ Cents}$ geschlossen werden. | `REQ-004` um Bedingung `Balance == 0 Cents` erweitert; `REQ-014` für Fehlerfall ergänzt. |
| `DEF-REQ-03` | `REQ-007`, `REQ-017` | **Fehlende Spezifikation in der Angabe:** Die Anforderung *"overdraft limit may be granted for special customers"* definierte weder das Limit noch dessen Änderbarkeit. | Hoch | Nach PO-Klärung (`Q-02`): Definition eines individuell verhandelbaren Overdraft-Attributs in Cents inkl. nachträglicher Änderbarkeit. | `REQ-007` als **A-REQ** präzisiert; neue Anforderung `REQ-017` für Limit-Änderungen ergänzt. |
| `DEF-REQ-04` | `REQ-010`, `REQ-016` | **Nicht-Testbarkeit in der Angabe:** Die Angabe *"Account ID: 8 digits, not 00000000, unique"* spezifizierte kein Verhalten bei ungültigen Zeichen, Duplikaten oder unbekannten IDs. | Mittel | Nach PO-Klärung (`Q-05`): Exakte Definition aller Format- und Eindeutigkeitsregeln sowie Fehlerbehandlung (`AccountNotFoundException`). | `REQ-010` SMART-konform präzisiert; `REQ-016` für unbekannte IDs ergänzt. |
| `DEF-REQ-05` | `REQ-008`, `REQ-009`, `REQ-011`, `REQ-012` | **Fehlende NFA- & Rollen-Klassifizierung in der Angabe:** Die Vorgaben *"no persistency / DB mock"* und *"simple CLI frontend"* wurden nur als "non-requirements" geführt und Berechtigungen waren unklar. | Hoch | Nach PO-Klärung (`Q-03`, `Q-04`): Re-Klassifizierung zu **A-REQs** (Repository-Pattern) und Beschränkung der Business-Logik-Ausführung auf Bankpersonal. | `REQ-008`, `REQ-009`, `REQ-011` und `REQ-012` als **A-REQ** markiert und geschärft. |

### 1.3.3 Ergebnis & Outcome der SQE-Maßnahme

1. **Verbesserte Testbarkeit:** Alle 15 finalen Anforderungen erfüllen nun die **ISO 29148** Qualitätskriterien und bilden eine eindeutige Basis.
2. **Erfüllung der Definition of Entry (Architektur):**