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
| `Q-08` | Gilt das Overdraft-Limit pro einzelnem Konto oder als Gesamtlimit für den Kunden? | Pro Konto kann ein eigener Überziehungsrahmen ausgehandelt werden. | `REQ-007`, `REQ-016` |
| `Q-09` | Wird ein geschlossenes Konto komplett gelöscht oder als „geschlossen“ gespeichert? | Konten werden als geschlossen (`CLOSED`) markiert; die Konto-ID bleibt dauerhaft belegt. | `REQ-004`, `REQ-010`, `REQ-020` |
| `Q-10` | Müssen Kunden im System verwaltet oder gelöscht werden können? | Eigene Kunden-Entitäten sind nicht gefordert; ein Kunde wird einfach über seinen Namen am Konto identifiziert. | `REQ-013` |

---

## 1.2 Final Requirements Specification

> **Legende:**  
> **F** = Funktionale Anforderung | **NF** = Nicht-funktionale Anforderung | **A-REQ** = Architekturrelevante Anforderung  

 1. **`REQ-001` (F):** Das System muss es einem Bankmitarbeiter ermöglichen, ein neues Konto für einen Kunden ohne Startguthaben ($0\text{ Cents}$) zu eröffnen.

 2. **`REQ-002` (F):** Das System muss es einem Bankmitarbeiter ermöglichen, ein neues Konto für einen Kunden mit einem frei wählbaren, positiven Startguthaben (Ganzzahl in Cents) zu eröffnen.

 3. **`REQ-003` (F):** Das System muss auf Anfrage die Konto-Details (Konto-ID, Kundenname, Kontostatus und aktueller Kontostand in Cents) eines spezifischen Kontos vollständig und korrekt zurückgeben.

 4. **`REQ-004` (F):** Das System muss das Schließen eines aktiven Kontos ermöglichen, sofern der Kontostand exakt $0\text{ Cents}$ beträgt, indem das Konto dauerhaft als geschlossen (`CLOSED`) markiert wird (ohne die Konto-ID wieder freizugeben).

 5. **`REQ-005` (F):** Das System muss das Abheben von Geldbeträgen auf einem aktiven Konto erlauben, sofern der Betrag strikt positiv ist (Ganzzahl in Cents) und der Kontostand nach der Abhebung nicht unter $0\text{ Cents}$ fällt (außer bei gewährtem Overdraft-Limit, welches die einzige Abhebungsobergrenze bildet).

 6. **`REQ-006` (F):** Das System muss das Einzahlen von Geldbeträgen auf ein aktives Konto erlauben, sofern der einzuzahlende Betrag strikt positiv ist (Ganzzahl in Cents), ohne betragsmäßige Obergrenze.

 7. **`REQ-007` (F / A-REQ):** Das System muss es dem Bankpersonal ermöglichen, für ein einzelnes aktives Konto ein individuelles Überziehungslimit (Overdraft-Limit in Cents) zu gewähren, sodass Abhebungen auf diesem Konto bis zu einem negativen Saldo in Höhe dieses Limits erfolgreich ausgeführt werden.

 8. **`REQ-008` (F / A-REQ):** Das System muss es dem Bankpersonal ermöglichen, einen Gesamtbericht für einen spezifischen Kundennamen zu generieren, der alle diesem Namen zugeordneten Konten mit Einzel- und Gesamtsaldo auflistet (und für den Kunden einsehbar ist).

 9. **`REQ-009` (F / A-REQ):** Das System muss es dem Bankpersonal ermöglichen, einen Gesamtbericht über die gesamte Bank zu generieren, der alle existierenden Konten aller Kunden mit dem Gesamtsaldo der Bank zusammenfasst.

10. **`REQ-010` (F / A-REQ):** Das System muss jede Konto-ID vor der Erstellung auf ein valides Format (exakt 8 Ziffern, ungleich `00000000`, bankweit eindeutig über alle aktiven und geschlossenen Konten hinweg) prüfen und ungültige oder bereits vergebene IDs mit einer Fehlermeldung ablehnen.

11. **`REQ-011` (NF / A-REQ):** Das System darf keine dauerhafte Datenbank-Persistenz nutzen; die Datenhaltung muss über ein gemocktes Datenbank-Interface isoliert und testbar sein.

12. **`REQ-012` (NF / A-REQ):** Das System muss ein einfaches Befehlszeilen-Interface (CLI-Frontend) bereitstellen, über das die Business-Logik ausschließlich durch das Bankpersonal interaktiv ausgeführt werden kann.

13. **`REQ-013` (F / A-REQ):** Das System muss jedes Konto einem Kundennamen zuordnen, ohne eine separate Kunden-Entität oder Kundenlöschung zu führen, wobei mehrere Konten demselben Kundennamen zugeordnet sein können.

14. **`REQ-014` (F):** Das System muss Transaktionen (Ein- und Auszahlungen) mit einem Betrag von exakt $0\text{ Cents}$ als ungültig ablehnen.

15. **`REQ-015` (F / A-REQ):** Das System muss Operationen auf nicht existierende Konto-IDs abfangen und mit einer definierten Fehlermeldung (`AccountNotFoundException`) quittieren.

16. **`REQ-016` (F):** Das System muss es dem Bankpersonal ermöglichen, ein bestehendes Überziehungslimit (Overdraft-Limit in Cents) eines einzelnen aktiven Kontos nachträglich anzupassen (zu erhöhen oder zu reduzieren), sofern der aktuelle Kontostand das neue Limit nicht unterschreitet.

17. **`REQ-017` (F):** Das System muss eine Abhebung mit einer spezifischen Fehlermeldung ablehnen und den Kontostand unverändert lassen, sofern der abzuhebende Betrag das verfügbare Guthaben inklusive des kontospezifischen Overdraft-Limits übersteigt.

18. **`REQ-018` (F):** Das System muss die Eröffnung eines Kontos mit einem negativen Startguthaben ($< 0\text{ Cents}$) als ungültig ablehnen.

19. **`REQ-019` (F):** Das System muss die Vergabe oder Änderung eines Overdraft-Limits mit einem negativen Betrag ($< 0\text{ Cents}$) als ungültig ablehnen.

20. **`REQ-020` (F):** Das System muss sämtliche Transaktionen und Statusänderungen (Einzahlungen, Abhebungen, Overdraft-Änderungen sowie erneutes Schließen) auf einem bereits als geschlossen (`CLOSED`) markierten Konto mit einer spezifischen Fehlermeldung (`AccountClosedException`) ablehnen.


---
## 1.3 SQE-Maßnahmen: Requirements Inspection & Verification

In Phase 1 wurde als primäre SQE-Maßnahme eine formelle **Requirements Inspection** (angelehnt an das Fagan-Inspection-Verfahren) auf Basis der ursprünglichen Aufgabenstellung sowie der anschließenden PO-Klärungen (`Q-01` bis `Q-10`) durchgeführt. Ziel war es, Unvollständigkeiten, Mehrdeutigkeiten und fehlende Testbarkeiten vor dem Architekturentwurf frühzeitig zu identifizieren und zu beheben (Early Testing Principle).

### 1.3.1 Durchgeführte SQE-Aktion

1. **Methode:** Formelles Peer-Review / Inspection der initialen Anforderungen unter Verwendung einer Prüfliste nach **ISO 29148** Kriterien (Prüfkriterien: *Unambiguous*, *Complete*, *Consistent*, *Verifiable*, *Feasible*, *Singular*).

2. **Teilnehmer & Rollen:** Gesamtes Entwicklungsteam (Rollen: Moderator, Reader, Inspector) sowie der Product Owner zur Klärung aller offenen Fachfragen (`Q-01` bis `Q-10`). Die finale Verantwortung und Ownership für die Requirements-Anpassungen verblieb beim Requirements Engineer / Product Manager.

3. **Prüfgegenstand:** Requirements Baseline v1.3 (Gegenüberstellung der ungenauen Initial-Spezifikation aus der Aufgabenstellung mit der finalen, PO-bestätigten SMART-Spezifikation).

### 1.3.2 Inspection Protocol (Review-Protokoll)

| Defect-ID | Relevante Anforderung (Ziel-REQ) | Befund / Identifizierter Mangel (aus der Ursprungs-Aufgabenstellung) | Schweregrad | Ergriffene Korrekturmaßnahme | Auswirkung auf Finale Requirement Baseline |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `DEF-REQ-01` | `REQ-005`, `REQ-006`, `REQ-014`, `REQ-018` | **Mehrdeutigkeit in der Angabe:** Die ursprüngliche Anforderung *"deposit/withdraw money (euros and cents, no negative money)"* ließ offen, wie $0\text{ Cents}$ oder negative Startguthaben behandelt werden und wie Rundungsfehler vermieden werden. | Hoch | Nach PO-Klärung (`Q-01`): Strikte Ganzzahl-Arithmetik in Cents (keine Floating-Point-Werte) und Validierung auf Beträge $> 0\text{ Cents}$ bei Transaktionen sowie $\ge 0\text{ Cents}$ bei Kontoeröffnung. | `REQ-005` & `REQ-006` auf Ganzzahl-Cents präzisiert; `REQ-014` und `REQ-018` ergänzt. |
| `DEF-REQ-02` | `REQ-004`, `REQ-020` | **Unvollständigkeit in der Angabe:** Die ursprüngliche Anforderung *"close account"* enthielt weder eine Regelung für Konten mit Saldo $\neq 0$ noch für den Lebenszyklus geschlossener Konten. | Hoch | Nach PO-Klärung (`Q-09`): Schließen ist nur bei `Balance == 0 Cents` zulässig und markiert das Konto dauerhaft als `CLOSED`; weitere Operationen werden blockiert. | `REQ-004` auf Soft-Delete (`CLOSED`) präzisiert; `REQ-020` (`AccountClosedException`) ergänzt. |
| `DEF-REQ-03` | `REQ-007`, `REQ-016`, `REQ-017`, `REQ-019` | **Fehlende Spezifikation in der Angabe:** Die Anforderung *"overdraft limit may be granted for special customers"* definierte weder Geltungsbereich, Änderbarkeit noch Grenzverhalten. | Hoch | Nach PO-Klärung (`Q-02`, `Q-08`): Kontospezifisches, nachträglich änderbares Overdraft-Limit ($\ge 0\text{ Cents}$) und Ablehnung ungedeckter Abhebungen. | `REQ-007` kontospezifisch geschärft; `REQ-016`, `REQ-017` und `REQ-019` ergänzt. |
| `DEF-REQ-04` | `REQ-010`, `REQ-013`, `REQ-015` | **Nicht-Testbarkeit & Datenmodell-Unklarheit:** Die Angabe *"Account ID: 8 digits, not 00000000, unique"* und die Kundenzuordnung ließen Randfälle und Entitätsstruktur offen. | Mittel | Nach PO-Klärung (`Q-05`, `Q-09`, `Q-10`): Kundenidentifikation rein über Kundennamen am Konto; dauerhafte Eindeutigkeit der 8-stelligen Account-ID auch über geschlossene Konten hinweg. | `REQ-010` & `REQ-013` SMART-konform präzisiert; `REQ-015` (`AccountNotFoundException`) ergänzt. |
| `DEF-REQ-05` | `REQ-008`, `REQ-009`, `REQ-011`, `REQ-012` | **Fehlende NFA- & Rollen-Klassifizierung in der Angabe:** Die Vorgaben *"no persistency / DB mock"* und *"simple CLI frontend"* wurden nur als "non-requirements" geführt und Berechtigungen waren unklar. | Hoch | Nach PO-Klärung (`Q-03`, `Q-04`): Re-Klassifizierung zu **A-REQs** (Repository-Pattern) und Beschränkung der Business-Logik-Ausführung auf Bankpersonal. | `REQ-008`, `REQ-009`, `REQ-011` und `REQ-012` als **A-REQ** markiert und geschärft. |

### 1.3.3 Ergebnis & Outcome der SQE-Maßnahme

1. **Verbesserte Testbarkeit:** Alle 20 finalen Anforderungen erfüllen nun die **ISO 29148** Qualitätskriterien (insbesondere *Unambiguous*, *Verifiable* und *Singular*) und bilden eine eindeutige Basis für Gherkin-Akzeptanzkriterien sowie automatisierte Unit- und E2E-Tests.

2. **Erfüllung der Definition of Entry (Architektur):** Nach Einarbeitung sämtlicher PO-Antworten (`Q-01` bis `Q-10`) und Behebung aller 5 kritischen Befunde (`DEF-REQ-01` bis `DEF-REQ-05`) wurde die Anforderungsspezifikation (Baseline v1.3) offiziell freigegeben. Die Testabdeckung aller 20 Systemanforderungen ist ab dieser Phase zu 100% in der Traceability-Matrix nachverfolgbar.