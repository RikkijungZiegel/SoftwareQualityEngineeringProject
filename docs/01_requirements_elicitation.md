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

---
## 1.3 SQE Actions
* **Durchgeführte Aktion:**
* **Ergebnis / Beseitigte Fehler:**