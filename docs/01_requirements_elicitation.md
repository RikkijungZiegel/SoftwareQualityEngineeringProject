# 1. Requirements Elicitation & Clarification

## 1.1 Open Questions & PO Clarification

| ID | Offene Frage / Unklarheit | Klärung mit PO | Auswirkung auf Anforderung |
| :--- | :--- | :--- | :--- |
| `Q-01` | In welcher Form und Präzision werden Geldbeträge verarbeitet? | noch offen | `REQ-005`, `REQ-006` |
| `Q-02` | Wie hoch ist das Überziehungslimit (Overdraft Limit) für spezielle Kunden? | noch offen | `REQ-007` |
| `Q-03` | Wer darf den Report für einen einzelnen Kunden generieren? | noch offen | `REQ-008` |
| `Q-04` | Darf ein Kunde seinen eigenen Report selbst einsehen? | noch offen | `REQ-008` |
| `Q-05` | Welches Format und welche Validierungsregeln gelten für die Account ID? | noch offen | `REQ-010` |
| `Q-06` | Gibt es ein maximales Transaktionslimit pro Abhebung? | noch offen | `REQ-005` |
| `Q-07` | Gibt es ein maximales Transaktionslimit pro Einzahlung? | noch offen | `REQ-006` |

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
| `DEF-REQ-01` | `REQ-005`, `REQ-006` | **Mehrdeutigkeit in der Angabe:** Die ursprüngliche Anforderung *"deposit/withdraw money (euros and cents, no negative money)"* ließ offen, wie exakt $0{,}00\text{ EUR}$ behandelt wird und wie Rundungsfehler vermieden werden. | Hoch | Festlegung auf Festkomma-Arithmetik (Cents als Ganzzahlen/Decimal) und strikte Validierung auf Beträge $> \text{0.00 EUR}$. | `REQ-005` & `REQ-006` wurden um Genauigkeits- und Vorzeichenklausel erweitert. |
| `DEF-REQ-02` | `REQ-004` | **Unvollständigkeit in der Angabe:** Die ursprüngliche Anforderung *"close account"* enthielt keine Regelung für Konten mit verbleibendem Guthaben oder negativen Salden. | Mittel | Einführung einer strikten Pre-Condition: Ein Konto darf ausschließlich bei einem exakten Kontostand von $0{,}00\text{ EUR}$ geschlossen werden. | `REQ-004` wurde um die Bedingung `Balance == 0.00 EUR` ergänzt (sowie `REQ-014`). |
| `DEF-REQ-03` | `REQ-007` | **Fehlende Spezifikation in der Angabe:** Die Anforderung *"overdraft limit may be granted for special customers"* definierte weder das Limit noch den Überziehungsmechanismus. | Hoch | Definition eines konfigurierbaren Overdraft-Attributs pro Spezialkonto und Strukturierung als architekturrelevante Anforderung (**A-REQ**). | `REQ-007` wurde als **A-REQ** neu präzisiert. |
| `DEF-REQ-04` | `REQ-010` | **Nicht-Testbarkeit in der Angabe:** Die Angabe *"Account ID: 8 digits, not 00000000, unique"* spezifizierte kein Verhalten bei ungültigen Zeichen, Längen oder Duplikaten. | Mittel | Exakte Definition aller Format- und Validierungsregeln als Voraussetzung für automatisierte Unit-Tests. | `REQ-010` wurde SMART-konform präzisiert. |
| `DEF-REQ-05` | `REQ-011`, `REQ-012` | **Fehlende NFA-Klassifizierung in der Angabe:** Die Vorgaben *"no persistency / DB mock"* und *"simple CLI frontend"* wurden nur als "non-requirements" geführt. | Hoch | Re-Klassifizierung dieser Randbedingungen zu **A-REQs**, da sie das Repository-Pattern und die Entkopplung der Schichtenarchitektur erzwingen. | `REQ-011` und `REQ-012` wurden explizit als **A-REQ** markiert. |

### 1.3.3 Ergebnis & Outcome der SQE-Maßnahme

1. **Verbesserte Testbarkeit:** Alle 15 finalen Anforderungen erfüllen nun die **ISO 29148** Qualitätskriterien und bilden eine eindeutige Basis..
2. **Erfüllung der Definition of Entry (Architektur):**