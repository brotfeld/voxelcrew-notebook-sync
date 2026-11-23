# VoxelCrew - Single Source of Truth (Sync Bundle)
Stand: 2026-11-23 13:46:15 | Quell-Repo: brotfeld/mc-bot-leitstelle-code

================================================================================
## DATEI: 2026-11-23__AGENTS.md
================================================================================
# Agenten- und Betriebsregeln

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: AGENTS.md

# AGENTS.md — Spielregeln für Agenten

Dieses Repository ist die Leitstelle für Minecraft-Bedrock-Bots. Du bist der Resident-Agent und arbeitest die Tasks aus `ops/BOARD.md` ab. Chris gibt frei und testet als Mensch; der Mastermind schreibt Tasks, prüft und mergt. **Chris hat am 2026-11-18 bestätigt: Du (der Resident-Agent) bist der Mastermind.**

## 1. Lese-Reihenfolge pro Task

1. `AGENTS.md` (diese Datei)
2. `ops/BOARD.md`
3. `ops/tasks/<ID>.md`
4. Bei Wissensbezug: die unter `wissen:` genannten Dateien im Vault
5. Bei Rework: `ops/REVIEWS.md`

Arbeite immer genau **einen** Task. Kein Task ohne Datei in `ops/tasks/`.
Das Task-Frontmatter `repo:` bestimmt das Ziel-Repository (Standard `leitstelle`); registrierte Repositories stehen in `ops/repos.json`.

## 2. Umgebung

* Node.js **26.x** ist Pflicht (`node --version`). Der Standard-PATH kann auf eine ältere Version zeigen.
* Falls falsch: `nvm use 26.10.0` oder den absoluten Node-26-Pfad verwenden.
* **Port-Aufteilung (Chris, 23.11.2026, SSOT `ops/SYSTEM_SSOT.json`):**
  * **127.0.0.1:19132** = Live-Welt (`nethernet`, `server-portv6=19142`,
    `enable-lan-visibility=true`, exklusiver UDP-7551-Inhaber). Strikt tabu für
    Showcases, Tests und die Notbremse.
  * **127.0.0.2:19133** = Showcase-Bühne (`nethernet`, `19143`, LAN `false`,
    direkter Endpunkt, UDP `19133`). Showcases starten ausschließlich per
    Ingame-Chat-Befehl `!show`, nie blind vorab.
  * **127.0.0.3:19134** = E2E-Testserver (`nethernet`, `19144`, LAN `false`,
    direkter Endpunkt, UDP `19134`).
  * BDS 1.26.63.1 unterstützt **ausschließlich** NetherNet (`TRANSPORT TYPE
    ERROR` bei `raknet`): Alle drei Server nutzen `transport=nethernet`. Nur
    `19132` hat `enable-lan-visibility=true` und exklusiv UDP 7551;
    `19133`/`19134` haben LAN `false`, eigene UDP-Ports und direkten,
    port-exakten Endpunkt. Die frühere Regel „19133/19134 = RakNet" ist
    damit ausdrücklich ersetzt.
  * **Akteurs-Isolation:** Live-Profile (`WaechterBot`, `BauBot`, `SWARM_BOT`)
    niemals auf 19133/19134; dort nur `SHOW_ACTOR`/`OBSERVER` — und umgekehrt.
    Erzwungen in `ops/lib/actor-profile-guard.cjs` vor jedem Verbindungsaufbau.
* **Single Source of Truth:** `ops/SYSTEM_SSOT.json` ist maßgeblich für Worker-,
  Maschinen-, Server-, Vision-, Prozess- und Freigaberegeln. Generierte Dateien
  (`ops/runner.json`, `ops/runner.alt-laptop.json`, `ops/workers.json`) werden
  mit `node ops/scripts/system-config-sync.mjs` erzeugt und nie manuell gepflegt.
* Tests: `npm test`. Einzelne Datei: `node tests/<datei>.test.js`.
* **UI-Änderungen an `ops/cockpit` brauchen zusätzlich den echten Browser-Test** `npm run test:browser` (Headless-Chromium gegen temporären Fixture-Server). Die Verankerung steht in §10.4.
* Keine neuen Abhängigkeiten ohne Freigabe im Task. `playwright` ist mit Chris' Freigabe vom 22.11.2026 für den Browser-Test freigegeben.

## 3. Arbeitsweise

* Ein Task = ein eigener Worktree + Branch `task/<ID>`.
* Start über `node ops/scripts/task-worktree.mjs start <ID>`.
* Reservierung: erster Push auf `task/<ID>` mit `claim: <ID> durch <agent>`. Ein abgelehnter Push bedeutet, dass der Task bereits vergeben ist.
* Im Haupt-Checkout wird während eines Tasks nicht editiert.
* Keine parallelen Edits im selben Worktree.
* Commits klein und thematisch. Branch am Ende pushen.
* **Jeder Arbeitsschritt endet mit `git add -A`, `git commit` und `git push`.** Kein Schritt bleibt nur lokal liegen.
* Am Task-Ende gehören Result, Board, Log und der Code in denselben Push. Erst committen und pushen, dann den Schritt als erledigt melden.
* **Merge nach `main` ist von Chris mit 2026-11-18 bis auf Widerruf genehmigt** und erfolgt durch den Mastermind (Resident-Agent) nach abgeschlossenem Review. Vorher galt `Kein Merge nach main` — diese Regel ist damit ausdrücklich aufgehoben. Kein Force-Push. Kein History-Rewrite. Merge-Konflikte in `ops/BOARD.md`, `ops/LOG.md` und `ops/QUESTIONS.md` werden **immer automatisch chronologisch und ohne Datenverlust** aufgelöst (kein Rückfragen-Stopp).

## 4. Grenzen

Nicht ohne expliziten Auftrag im Task:

* `app/backend/safety/**` (SafetyGuard) und Sicherheitsregeln,
* Auth, Tokens, Zugangsdaten, `.env*`,
* `data/auth-cache/**`, `data/worlds/**`, `server/**`,
* öffentliche Erreichbarkeit, Ports, Firewall,
* Tests abschwächen, überspringen oder löschen.

Zusätzlich:

* Keine Secrets, Tokens, Weltdateien oder Auth-Caches committen.
* `AGENTS.md` nicht ohne Freigabe ändern.
* Keine Architekturentscheidung allein treffen.

## 5. Vault

Der Vault liegt als Junction `vault/` im Worktree, sofern eingerichtet.

* `vault: read` — lesen erlaubt, schreiben nicht.
* `vault: write` — schreiben nur in `20 Wissen/Minecraft/Daten/kuratiert/**` und eigene Notizen mit `status: entwurf`.
* Niemals schreiben: `Daten/extrahiert/**`, `Index/**`, `Schema.md`.
* Vor Item-, Block- oder Rezeptarbeit `vault/20 Wissen/Minecraft/README.md` und die genannten Datensätze lesen.
* Keine IDs, Härtewerte oder Rezepte raten. Fehlendes als offenen Punkt markieren.
* Jede Kuratierung braucht `source` und `begruendung`.

## 6. Ergebnis

Schreibe `ops/results/<ID>.md` nach `ops/templates/result.md`, maximal eine Seite:

* Status `review`
* was geändert wurde
* Testbefehle und Ergebnis
* offene Punkte und Risiken
* `vault_auswirkung` und geänderte Vault-Dateien
* Branch und Commit-Hash

Am Task-Ende zusätzlich (ab T-009):

```bash
npm run verify -- --out ops/results/<ID>.verify.json
```

Die Datei mitcommitten. Bis T-009 genügt `npm run verify`. `ops/verify/last.json` bleibt lokal und gitignoriert.

Aktualisiere `ops/BOARD.md` (Task nach `review`) und `ops/LOG.md` (eine Zeile).

## 7. Stop-Bedingungen (Autopilot, Chris-Anweisung 22.11.2026)

Stoppen und **nur dann** Chris fragen (Frage in `ops/QUESTIONS.md`, Task in `ops/BOARD.md` auf `blocked`), wenn **eine** dieser echten Außen-Risiken vorliegt:

1. **Eingriff in den Live-Port `19132`** oder **Löschung/Überschreiben von Weltdaten**.
2. **Kostenpflichtige API-Calls** (keine Bestätigung vorhanden).
3. **Windows-System- oder Firewall-Änderungen**.
4. **Finale Einladung zur fertigen Live-Präsentation** an Chris (erst melden, wenn alle Gate-Punkte grün sind).

**Nicht mehr stoppen (autonom weiter):**
- Merge-Konflikte in `ops/BOARD.md`, `ops/LOG.md`, `ops/QUESTIONS.md` → immer **chronologisch und ohne Datenverlust** automatisch mergen.
- Nach einer beantworteten Frage (z. B. `Q-T…` mit Status `beantwortet`) → **kein zweites „Go“**, Task läuft sofort automatisch weiter.
- Rote QA-/Observer-Gates → autonome Nachbesserungs-Schleife (siehe §10.2), **kein vorzeitiges `blocked`** nur wegen Gate-Rot.
- `blocked` bleibt task-lokal nur für die oben genannten Außen-Risiken oder wenn nach Ausschöpfung der autonomen Schleifen (max. 5 Versuche je Gate-Loop) kein Fortschritt möglich ist; dann sauber dokumentieren und weiter mit anderen Tasks.

Weitere Regeln bleiben: Nicht raten. Nicht die Anforderung selbst ändern. Bei SafetyGuard/Auth/Secrets/öffentlicher Erreichbarkeit/echten Konten gilt weiterhin maximale Vorsicht; im Zweifel die Außen-Risiko-Liste heranziehen.

### 7.1 Keine Werkzeug-Schleifen

* Zwei identische fehlgeschlagene Werkzeugaufrufe hintereinander = Stop nur bei Außen-Risiko (siehe oben); sonst alternativer Plan, maximal 5 Versuche in einer autonomen Nachbesserungs-Schleife.
* Fehler wörtlich zitieren, `git status --short` zeigen, Fortschritt in Result/Log dokumentieren.
* Ein neuer Anlauf beginnt mit frischem Kontext und anderem Plan.
* Zwischenstände werden committet und gepusht, damit der Verlauf nachvollziehbar ist.

## 8. Sprache

* **Task-Nummern niemals nackt:** Jede Erwähnung einer Task-ID in Chat, Log, Board, Result, Feed, Report und Meldungen hat die Form `T-XYZ (klarer Titel aus ops/tasks/T-XYZ.md)`. Beispiel: `T-018 (Theater-Show: Item halten + Jukebox)`. Nackte Nummern wie `T-193` allein sind verboten — auch in Listen, Meldungen und automatisch erzeugten Berichten.
* Doku, Chat, UI: Deutsch.
* Code, Dateinamen, Konfigurationsschlüssel: Englisch.
* Commits: `typ: kurze beschreibung` (z. B. `test: kompaktes verify-skript`).
* Dateien als **UTF-8 ohne BOM** speichern.

## 9. Worker-Regeln und Prozessautonomie (Chris, 19.11.2026)

* **Entscheiden ist Default, Fragen sind Ausnahme.** Der Watcher läuft durchgehend (`continueAfterRun: true`) und dispatcht automatisch den nächsten geeigneten Ready-Task. Ein leerer Task, eine Stundenlimite oder eine blockierte Task-Karte beenden **nicht** den Betrieb. Der 00:05-UTC-Wecker ist nur die Erinnerung, dass ab da **auch** Kilo2 wieder automatisch läuft — kein Starttor für die anderen Worker.
* **Fünf Worker, zwei Maschinen (SSOT 23.11.2026):** Hauptrechner = `kilo1` (`gateway`, `kilo-auto/free`), `kilo5` (`gateway`, `kilo-auto/free`) und `kilo2` (`openrouter` mit Fallback-Kette aus T-199), `maxParallelByHost = 2`. FELDBOOK = `kilo3` und `kilo4` (beide `gateway` / `kilo-auto/free`), `maxParallelByHost = 2`. Kilo6 entfällt. Quelle ist ausschließlich `ops/SYSTEM_SSOT.json`; `ops/workers.json` und beide `runner*.json` werden daraus generiert. Worker-Dispatches bleiben pausiert, solange `ops/runner.stop` aktiv ist.
* **Reihenfolge:** OpenRouter zuerst, dann Gateway, dann Mastermind. Innerhalb eines Typs rotiert die Auswahl deterministisch nach Task-Nummer. Scheitert ein Worker, übernimmt automatisch der nächste; sind alle aktiven Worker für einen Task erfolglos, pausiert dieser Task 30 Minuten und die Queue läuft weiter.
* **Drei Runden je Kilo:** Ein Kilo-Lauf darf maximal **3 Runden inklusive Erstlauf** haben (Erstlauf + 2 Korrekturen). `maxRounds: 3` ist damit der Standard für `kilo run`, `kilohandler` und den MCP-Server. Technische Wiederholversuche nach 429/Credit- oder Startfehlern zählen nicht als Runden; `maxAttempts: 16` begrenzt die Startversuche je Gesamtlauf. Die Modellauswahl je Worker bleibt davon unberührt.
* **Kilo-Auto-Final-Try (optional, aktuell aus):** OpenRouter-Worker mit `kiloAutoFinalTry: true` in `ops/workers.json` hängen nach `openrouter/free` genau **einen** Abschlussaufruf auf `kilo/kilo-auto/free` an. Der Aufruf bleibt im OpenRouter-Worker (kein Gateway-Wechsel, kein Chris-Tor, keine Task-Statusänderung); bei 429 oder Fehler wird er im selben Job nicht wiederholt. Ohne `openrouter/free` als letztem Preset-Glied startet der Worker gar nicht erst. Chris hat `openrouter/free` am 21.11.2026 aus `ops/kilo-handler/openrouter-preset.json` entfernt; deshalb ist `kiloAutoFinalTry` bei Kilo2 (dem einzigen bisherigen Nutzer) auf `false` gesetzt. Eine Reaktivierung braucht zuerst wieder `openrouter/free` als letztes Preset-Glied und Chris' Freigabe.
* **`blocked` ist task-lokal:** Eine blockierte Task-Karte pausiert nur diesen Task, nicht die Queue. Chris beantwortet Fragen in `ops/QUESTIONS.md` (Status `beantwortet`); danach laufen betroffene Tasks **sofort automatisch weiter** (kein zweites „Go“). Chris' finale Freigabe-Tore für die **Live-Präsentation** (Punkt 4 in §7) bleiben ihm vorbehalten; kein Agent lädt ihn eigenmächtig zur fertigen Show ein.
* **Merge:** Der Merge nach `main` ist von Chris seit 18.11. bis auf Widerruf genehmigt und erfolgt durch den Mastermind nach abgeschlossenem Review (`§5`), nie per Force-Push oder History-Rewrite.
* **Ausnahmen:** SafetyGuard, Auth, Secrets, öffentliche Erreichbarkeit, echte Konten sowie CDP-Fensterzugriff (nur Port 9399) und Screengrab-/externe Bilder bleiben beim Mastermind — unabhängig von der Worker-Reihenfolge.
* **Vault-Hygiene als Dauerbetrieb:** Die Kontrolle auf verwaiste Fragen und Aufgaben läuft als wiederkehrender, read-only Worker-Task. Sie legt Rückfragen als neue `status: offen`-Dateien an und archiviert nie ohne Chris' Tor (`status: beantwortet`/`erledigt` **und** gepusht). Kein Worker setzt diese Signale selbst.

## 10. OOM-Failsafe, QA-Gate und Kontingentschutz (Chris, 21.11.2026, verbindlich)

Dieser Abschnitt hat **vor jeder regulären Worker-Runde** Vorrang.

### 10.1 Prozesskontrolle (Punkt 1)

* Kein `git.exe`, `node.exe`, `npm`, `bedrock_server.exe` oder anderes Subprodukt
  darf verwaisst weiterlaufen. Jeder gestartete Prozess wird in
  `ops/runtime/pids.json` mit PID, Rolle und Port registriert und bei Ende
  wieder entfernt. Bei Timeout/Abbruch gilt immer
  `taskkill /PID <pid> /T /F` für den **kompletten Baum**, hartes Timeout
  standardmäßig 30 s (`ops/runtime/process-control.mjs`).
* **Alle** git-Aufrufe lauten `git --no-optional-locks …` mit 30-s-Timeout.
  Referenz: `runGit()` in `ops/runtime/process-control.mjs`. Verwaiste
  `git.exe`/`sh.exe`-Bäume werden über
  `node ops/runtime/runtime-guard.mjs reap` aktiv abgeräumt.
* Schwergewichtige Läufe (`npm run verify`, E2E, `npm test`) laufen **strikt
  serialisiert** über die Lock-Datei in `ops/runtime/locks/` (npm-Skripte
  `verify`, `e2e:*`). Nie zwei gleichzeitig. Vor jedem Start gilt
  `node ops/runtime/runtime-guard.mjs can-start`.
* RAM-Wächter (`node ops/runtime/runtime-guard.mjs watch`):
  **Maschinenspezifische Schwellen aus `ops/SYSTEM_SSOT.json` →
  `maschinen.<name>.ram`** (T-228): Hauptrechner Warnstufe > 80 %, FELDBOOK
  > 85 %, jeweils oder < 3 GB frei → keine neuen Worker-Tasks und keine
  neuen E2E-Läufe; Notbremse > 90 % belegt oder < 1,5 GB frei →
  Sofortstopp in fester Reihenfolge (1) hängende git-/Testprozesse,
  (2) E2E `19134`, (3) Show `19133`. Maschine per `--machine <name>` oder
  `MCBOT_MACHINE` wählen; Default `hauptrechner`. **Der Live-Server `19132`
  genießt höchsten Schutz und wird nie beendet.**
* Prozesse in Session 0/fremdem Konto liefern `Zugriff verweigert`; der Guard
  meldet das explizit. Die Freigabe dafür läuft über den privilegierten
  Dienst/Task Scheduler, nicht durch Abschwächen der Prüfung.

### 10.2 Verbindlicher QA-Standard (Punkt 3)

* Für **alle** Showcases und Meilensteine gilt `ops/WORLD_INVARIANTS.md` als
  hartes Gate im Test-Runner (`npm run verify -- --observer-gate` /
  `npm run verify:show`). Ohne grünen `observer`-Beweis kein Freigabestatus.
* Jeder E2E-Lauf (`19134`) und jeder Show-Abnahmelauf (`19133`) verbindet einen
  separaten `observer`-Bot. Blöcke gelten erst mit `update_block`-Paket,
  gehaltene Items erst mit `mob_equipment`-Paket des Akteurs an den Zielkoordinaten
  als bewiesen. Sichtbarkeit (`visible=true`), Oberflächenlage und der
  3D-ViewPort-/Voxel-Raycast-Check (Augenhöhe `y+1.62`, ±45° horizontal,
  ±35° vertikal, 3–32 Blöcke, keine undurchsichtige Blöcke auf der Sichtlinie)
  sind Pflicht.
* Produktions-Invarianten (begehbare Wege, Mindest-Raumhöhen, Gehen statt
  Teleport, kein Reconnect mitten in Szenen, Bühnen-Bounding-Box, Blickrichtung
  zum Publikum) sind Default; nur ein ausdrücklicher `intent`-Eintrag im
  Regieplan darf eine Ausnahme definieren.
* Selbstnoten durch Befehlssenden zählen nicht. Wer nicht aus
  `observer`-Sicht und Paketbeweis prüft, darf keinen Meilenstein als grün
  melden.
* **Autonome Nachbesserungs-Schleife bei rotem Gate (Chris, 22.11.2026):** Ein rotes QA-/Observer-Gate ist **kein** Grund für `blocked` oder eine Rückfrage. Der Worker/Agent nutzt die konkrete Fehlerdiagnose (z. B. `y≈32769` → Chunk-/Spawn-Init), setzt eine gezielte Korrektur um und wiederholt die Prüfung in einer Schleife von **maximal 5 Versuchen** je Gate-Loop, bis alle 4 Gate-Punkte grün sind. Erst nach Ausschöpfung sauber dokumentieren und mit anderen Tasks weitermachen; die finale Einladung zur Live-Präsentation erfolgt erst bei Grün (siehe §7 Punkt 4).

### 10.3 Kontingentschutz (Punkt 3, Kilo-Worker)

* Die OpenRouter-Kette von Kilo2 (T-199 – OpenRouter-Modellausfall: Preset prüfen und freigeben; `nemotron-3.5-lightning:free` usw.) bleibt unverändert.
* 95 % der QA-Prüfungen laufen über kostenlose Skripte / Kilo Auto Free
  (`kilo1`, `kilo3`, `kilo4`) mit **0 OpenRouter-Calls**.
* Ein Vision-Screenshot-Check ist ausschließlich ein optionales letztes Siegel
  nach grüner Stufe 1 und hart gedeckelt auf **max. 1 Call pro fertigem
  Meilenstein und max. 3 Calls pro Tag** (`ops/kilo-handler/vision-budget.mjs`).
  Bei Limits wird nicht blockiert, sondern gemeldet: „Stufe 1 grün,
  Vision-Call wegen Tagesbudget übersprungen (Screenshots abgelegt)".

### 10.4 Browser-E2E für UI-Änderungen (Chris, 22.11.2026, verbindlich)

* Jede Änderung an `ops/cockpit/public/**` oder an den Cockpit-APIs muss durch
  `npm run test:browser` (`tests/cockpit-browser-e2e.test.js`) laufen. Der Test
  startet einen echten Headless-Chromium gegen einen temporären Fixture-Server
  auf ephemeralem Port — **nie** gegen den Produktiv-Port 7070 und nie gegen
  Produktionspfade.
* Mindestabnahme aus Operator-Sicht: alle konfigurierten Worker erscheinen in
  **einem** Tab „Worker" mit sichtbarem Rechner-Badge (`Hauptrechner` /
  `FELDBOOK`), Typ, Task, Runde und Status; alte Einzel-Task-Balken und
  doppelte Host-Tabs dürfen nicht existieren; alle Tabs sind durchklickbar,
  alle Karten im ViewPort (`width > 0`, `height > 0`, keine Überlappungen).
* Harte Bedingungen: **0 Browser-Konsolenfehler** und **0 fehlgeschlagene
  API-Requests**. Der Lauf ist über `heavy-e2e` serialisiert und benötigt
  `can-start` gemäß §10.1; er ist zusätzlich in `npm run verify` verankert.
* Ein `npm run verify` ohne grünen Browser-Test gilt für UI-Änderungen nicht
  als abgeschlossen. API-/Quelltext-Tests ersetzen den Browser-Test nicht.


## 11. End-to-End-Produktdenken und simulierter Auftraggeber (dauerhaft, Chris-Vorgabe, 22.11.2026)

Dieser Grundsatz gilt **ausnahmslos für jeden Task, jede Änderung und jede Abnahme im gesamten System** — unabhängig vom Task-Thema. Er ist kein Sonderfall für einzelne Tasks und wird ab sofort automatisch auf jeden Task angewendet.

1. **Vollständiges Spezifikations-Mapping (Schritt 1, verbindlich):** Vor jeder größeren Umsetzung ist zu prüfen, ob die gesamte vorliegende Systemspezifikation (Vault inklusive aller weiterverarbeiteten Inbox-Originale, Referenzanlagen, `ops/WORLD_INVARIANTS.md`, Codebase, `ops/BOARD.md`) lückenlos gegen `ops/capabilities.json` und das Mapping in `ops/SPEC_MAPPING.md` abgebildet ist. Bestehen Lücken (Soll-Komponenten ohne Code, Code ohne Capability-Eintrag, offene Backlog-Tasks), wird das Mapping **zuerst** vervollständigt (Soll vs. Ist vs. Backlog), damit kein Agent blind rät oder mehrfach nach denselben Bausteinen sucht.
2. **Vollständige User-Journey herleiten:** Für jede Anforderung ist selbstständig herzuleiten, wie der echte Nutzer/Auftraggeber das System startet, bedient und erlebt — vom ersten Auslöser bis zum sichtbaren Ergebnis im operablen Gesamtsystem. Teil-Checks ohne durchgängige Journey gelten nicht als Abnahme.
3. **Keine Wegwerf-Testskripte, sondern reguläre Plattform-Bausteine:** Fehlt für den durchgängigen Ablauf eine Komponente (z. B. automatisierter Zuschauer-/Auftraggeber-Client, Chat-/Trigger-Orchestrierung, Abnahme-Modus, Bot-Fähigkeit), ist aus der gemappten Gesamtsystem-Spezifikation und `ops/capabilities.json`/`ops/SPEC_MAPPING.md` abzuleiten, was regulär ins Produkt/System gehört. Diese Komponente wird als **wiederverwendbarer System-Baustein** umgesetzt (kein einmaliges `_dbg`-/Ad-hoc-Skript als Dauerlösung), als Capability bzw. regulärer Aufrufpfad dokumentiert und direkt für die End-to-End-Abnahme genutzt.
4. **Chris niemals als manueller Test-Klicker:** Chris wird **nicht** aufgefordert, im Client manuell Befehle einzutippen oder Testschritte für den Agenten auszulösen. Ausnahmen sind ausschließlich die vier harten Tore in Abschnitt 7 (Live-`19132`, kostenpflichtige API-Calls, Windows-Firewall/Systemrechte, finale Einladung zur abgenommenen Live-Präsentation). Alles andere ist durch automatisierte/reguläre Bausteine abzudecken.
5. **Showcase-, Test- und Meilenstein-Abnahmen:** Trigger und Abnahme laufen über reguläre Bausteine (z. B. programmatischer Auftraggeber/Orchestrator), nie über „bitte tippe im Spiel“. Die finale Einladung an Chris erfolgt erst bei grünen Gate-Punkten gemäß Abschnitt 10.2 und `ops/WORLD_INVARIANTS.md`.

**Intake-/Task-Leitlinie:** Jeder neue oder überarbeitete Task bildet diesen Abschnitt in seiner Akzeptanz ab (`ops/templates/task.md` und der Intake-Generator in `ops/scripts/vault-intake.mjs` enthalten dazu Pflichtpunkte). Bei Konflikt gilt dieser Abschnitt vor der jeweiligen Taskspezifikation.


## 12. Regie-Leitplanken A–D: Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung (Chris, 23.11.2026, verbindlich)

Diese vier Leitplanken gelten zusätzlich zu §10 und §11 für jedes Vorhaben mit Welt-, Bühnen- oder Showcase-Bezug. Verbindlich ist die Fassung in `ops/SYSTEM_SSOT.json` → `regieLeitplanken`; die ausführliche Begründung steht im aktiven Handbuch `vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md`.

* **A — Capability- & Gap-Planner:** Vor jedem Vorhaben `ops/capabilities.json` prüfen („Kann ich schon?" vs. „Fehlt mir noch?"). Fehlende Grundfähigkeiten werden automatisch als **wiederverwendbare Bausteine** herausgeschnitten und über `ops/SPEC_MAPPING.md` dokumentiert — kein Ad-hoc-Skript als Dauerlösung (vgl. §11 Punkt 3).
* **B — Adaptive Scene Director (Gelände- & Sichtlinien-Regisseur):** Zufallswelten über `32×32`-Sektoren erkunden, geeignete Flächen finden und Bühnen **sanft an die Topografie anpassen** (Podest/Stufen statt Betonklotz). Verdeckende Blöcke oder den Zuschauer-Logenplatz korrigiert er automatisch über den 3D-ViewPort-Raycast (`ops/qa/world-invariants.mjs`, Augenhöhe `y+1,62`).
* **C — Dreifache Realitäts-Kopplung & Multi-Seed-Gate (`N ≥ 3`):** Ein Lauf gilt nur als bestanden, wenn **gleichzeitig** (1) Blockdaten, (2) freier 3D-Raycast und (3) bequeme Spieler-Erreichbarkeit (fester Boden, 2 Blöcke Kopffreiheit, begehbarer Pfad) bewiesen sind — und das auf `VoxelCrew:Ende` (`19134`) in **mindestens 3 unterschiedlichen Zufalls-Seeds** (z. B. Wald/Ebene, Hügelland, Küste/Wüste) selbstständig bestanden wurde. Alte Test-Platten werden nach jedem Lauf automatisch per atomarem Master-Reset bereinigt.
* **D — User-Empathy-Simulator & BehaviourPack↔Client-Symbiose:** Ein unprivilegierter Spieler-Simulator prüft auf `19134` aus echter 10-Jahre-Spieler-Perspektive Erreichbarkeit, Sichtfeld (`FOV 70°`) und ruhiges Vorführ-Tempo. BehaviourPack-Effekte/UI (`@minecraft/server`, `@minecraft/server-ui`) und Client-Bot-Aktionen (`SWARM_BOT`) müssen sauber verzahnt sein; `SWARM_BOT` bleibt Live-Profil und darf gemäß §2 nie auf `19133`/`19134` eingeloggt werden — die Symbiose läuft dort über das Bühnenprofil.

Die Umsetzung liegt im Bauplan `BP-007 (Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung)` mit Status `manual`; Freigabe und Worker-Dispatch bleiben bis auf Widerruf pausiert.


## 13. Verbindlicher 4-Stufen-Profi-UI-Prozess (Chris, 23.11.2026, verbindlich)

Für **jede** neue oder veränderte Oberfläche gilt: Erst die vier Stufen durchlaufen und dokumentieren, **dann** darf Code geschrieben werden. Verbindlich ist die Fassung in `ops/SYSTEM_SSOT.json` → `uiProzess`; das ausführliche Muster (Wireframe nach `anlage-07`, API-/IPC-Vertrag, Prüfliste) steht in `vault/handbuch/07_Vier_Stufen_UI_Prozess.md`. Geltungsbereich: VoxelCrew-Werkbank `7070`, VoxelCrew-2ndScreen-Leitstelle und In-Game-UI (`@minecraft/server-ui` + Pure-Client-Fallback).

1. **Job-to-be-Done:** Ein Satz in Sprache ab 10 Jahren — „Wenn …, will ich …, damit …". Maximal **3 Klicks** vom Auslöser zum sichtbaren Ergebnis. Kein Fachbegriff, kein Dateipfad im Satz.
2. **4-Zustands-Vertrag:** Jeder Bereich kennt explizit `leer` / `lädt` / `gefüllt (mit echten Daten)` / `handlungsbedarf`. **Kein rohes Markdown, kein Platzhalter, keine erfundenen Zahlen** im UI. Ein Zustand ohne Beweis darf nicht als „gefüllt" gelten.
3. **Wireframe + Vertrag vor dem Coding:** ASCII-Skizze nach `anlage-07` inkl. Klickzielen und Zustandsübergängen **und** exakter API-/IPC-Vertrag (Endpunkt, Methode, Request, Response, Fehlerfall). Ohne beides kein Implementierungsstart.
4. **Abnahme wie ein Profi:** Playwright-Klick-/Formular-Test mit Screenshot-Beleg, **0 Browser-Konsolenfehler, 0 fehlgeschlagene API-Requests**. 10-Jahre-Verständlichkeitscheck: Haupttext ohne Dateipfade und Tech-Kürzel. Pflicht ist der große **„Bot-Hilfe"-Notfallknopf** in der Leitstelle und im In-Game-Kompass; er erzeugt automatisch einen **60-s-Flugschreiber-Clip** und eine **Eskalationskarte** in Klartext (wiederverwendbarer Baustein `ops/runtime/bot-hilfe.mjs`).

Ein UI-Task ohne Nachweis aller vier Stufen ist nicht abnahmefähig. `npm run test:browser` bzw. der Werkbank-Browser-Test sind zusätzliche harte Bedingungen (§10.4). Die In-Game-Umsetzung des Kompass-Knopfs liegt im Bauplan `BP-011 (In-Game-UI & Pure-Client-Fallback)`; `server/**` bleibt bis zu dessen Freigabe unangetastet.



================================================================================
## DATEI: 2026-11-23__blueprint-registry.md
================================================================================
# Bauplan-Registry

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/registry.json
```json
{
  "schema": "blueprint-registry.v1",
  "stand": "2026-11-23",
  "approvalMode": null,
  "ids": [
    "BP-000",
    "BP-001",
    "BP-002",
    "BP-003",
    "BP-004",
    "BP-005",
    "BP-006",
    "BP-007",
    "BP-008",
    "BP-009",
    "BP-010",
    "BP-011",
    "BP-012",
    "BP-013",
    "BP-014",
    "BP-015"
  ]
}
```


================================================================================
## DATEI: 2026-11-23__BP-000.md
================================================================================
# Bauplan BP-000

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-000.json
```json
{
  "titel": "Werkstatt-Feinschliff & Dauerbetriebs-Härtung",
  "klartextziel": "Fünf dauerhafte Betriebs-Härtungen verankern, damit Baupläne klein, sicher und nachvollziehbar umgesetzt werden: klare Fertig-Kriterien, kleine Schritte, Verträge zuerst, ein kurzer Sicherheitscheck vor jedem Lauf und ein Lern-Tagebuch für künftige Planungen.",
  "weltvorlage": "FW-Base (freie Bau-Bühne) oder per level-seed generierte Gelände-/Biom-Welt, jeweils auf 19134 mit atomarem Master-Reset",
  "quellen": [
    "ops/SYSTEM_SSOT.json (Serverrollen, Freigabe manual, Ports 19132/19133/19134)",
    "ops/WORLD_INVARIANTS.md (Transport, Observer-Paketbeweise, ViewPort)",
    "AGENTS.md §10.1 Prozesskontrolle, §10.2 QA-Gate, §11 End-to-End-Produktdenken",
    "ops/LESSONS_LEARNED.md (Lern-Tagebuch)",
    "ops/capabilities.json und ops/SPEC_MAPPING.md"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "blueprint.pipeline (ops/blueprints/pipeline.mjs)",
      "werkbank.ui (ops/werkbank/public)",
      "werkbank.browserE2E (tests/werkbank-browser-e2e.test.js)",
      "qa.observerProof (ops/qa/observer-proof.mjs)",
      "runtime.processControl (ops/runtime/process-control.mjs)"
    ],
    "fehlend": [
      "definitionOfReady.lint als ausführbares Gate",
      "spectator.liveMode als wiederverwendbarer Baustein",
      "world.masterReset als atomarer Pipeline-Schritt"
    ],
    "hinweis": "BP-000 bleibt auf manual; die drei Lücken werden in M1, M3 und M4 adressiert und vor der Freigabe geprüft."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Definition-of-Ready-Lint",
      "inhalt": "Ein Bauplan-Schritt darf nur auf ready gehen, wenn Zieldateien, Welt-Art (FW-Base oder Gelände-Seed), Capabilities und ein Prüf-Kriterium benannt sind. Fehlende Felder blockieren den Übergang mit klarer Meldung."
    },
    {
      "id": "M2",
      "titel": "Atomic Diff & Minimal-Kontext",
      "inhalt": "Maximal 2–4 Dateien und unter 20 Minuten Laufzeit je Bauplan-Schritt; Worker bekommen ausschließlich den Kontext, den dieser Schritt wirklich braucht. Größere Schritte werden vorher zerlegt."
    },
    {
      "id": "M3",
      "titel": "Contract-First vor Parallel-Arbeit",
      "inhalt": "Bei Bauplänen mit Backend und Frontend wird zuerst ein kleiner Schritt 0 mit API-/Schema-Vertrag gemergt; parallele Worker starten erst danach."
    },
    {
      "id": "M4",
      "titel": "3-Sekunden-Pre-Flight-Check",
      "inhalt": "Vor jedem E2E- oder Show-Lauf werden Zielport (19133/19134), Welt-Reset, 0 Geister-Bots und RAM unter 78 % geprüft. Bei Rot startet kein Lauf."
    },
    {
      "id": "M5",
      "titel": "Lern-Tagebuch",
      "inhalt": "Am Ende jedes Bauplans werden gelöste bedrock-protocol- und Infrastruktur-Fallstricke automatisch in ops/LESSONS_LEARNED.md ergänzt, damit künftige Planungen darauf zurückgreifen können."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "Pre-Flight-Check auf 19134 grün, bevor ein visueller Lauf startet",
      "0 Geister-Bots und RAM unter 78 % aus ops/runtime/pids.json belegt",
      "SHOW_ACTOR/OBSERVER-Isolation unverändert",
      "Lern-Tagebuch-Eintrag nach Abschluss vorhanden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node tests/werkbank-browser-e2e.test.js",
      "npm test",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Freigabe bleibt manual — kein Agent setzt BP-000 selbst frei.",
    "Kein Eingriff in den Live-Server 19132 und keine Weltdaten-Löschung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls.",
    "Alle Härtungen sind organisatorisch und ändern nichts an SafetyGuard oder Auth."
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-000",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T02:19:35.781Z",
  "geaendertAm": "2026-11-23T02:19:35.781Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-001.md
================================================================================
# Bauplan BP-001

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-001.json
```json
{
  "titel": "Schatzjäger-Parcours (Showcase 19133)",
  "klartextziel": "Ein kindgerechter Abenteuer-Parcours auf der Showcase-Bühne: Der Spieler läuft selbst über den Co-Pilot-Relay-Modus, Schutz-Wächter und ein Leibwächter-Bot begleiten ihn, am Ziel wird ein Block-Muster per Partikel-Vorschau vervollständigt und der Lauf anschließend im Flugschreiber Frame für Frame nacherlebt.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "ShowWorld auf 127.0.0.2:19133; konkrete FW-*-Vorlage noch nicht benannt (offene Frage 2026-11-23).",
  "quellen": [
    "vault/konzept/2026-11-20 2026-11-20-showcase-schatzjaeger-parcours-md.md",
    "vault/konzept/00_README_KONZEPT_INDEX.md",
    "vault/konzept/01_architektur-briefing-multi-bot-bedrock-v14.md",
    "ops/SPEC_MAPPING.md",
    "ops/WORLD_INVARIANTS.md",
    "ops/SYSTEM_SSOT.json"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "bedrock.walkTo",
      "bedrock.setBlock",
      "bedrock.sendChat",
      "world.pathfinder.findPath",
      "world.worldGrid.isSolid",
      "memory.WorldMemory",
      "show.run"
    ],
    "fehlend": [
      "Co-Pilot-Relay als SHOW_ACTOR nicht in capabilities.json registriert",
      "Partikel-Bauvorschau nicht in capabilities.json registriert",
      "Flugschreiber-Wiedergabe nicht in capabilities.json registriert",
      "qa.acceptance (Backlog laut SPEC_MAPPING Abschnitt 4)"
    ],
    "hinweis": "Keine Block-IDs oder Rezepte erfunden; Rätselmuster und Schatztruhe sind Skizze der Quelle und müssen vor Umsetzung aus der Wissens-DB belegt werden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Parcours und Schutz",
      "inhalt": "Bühne, schmale Brücke, Überraschung, Zielpodest und Schatztruhe auf 19133 aufbauen; Schutz-Wächter und Leibwächter als SHOW_ACTOR einsetzen."
    },
    {
      "id": "M2",
      "titel": "Block-Muster-Rätsel und Replay",
      "inhalt": "Partikel-Vorschau, Weltgedächtnis-Mustererkennung und Flugschreiber-Wiedergabe als reguläre Bausteine; Parameter für Länge/Wächter/Muster anpassbar."
    },
    {
      "id": "M3",
      "titel": "Abnahme",
      "inhalt": "Observer-Paketbeweis und 3D-ViewPort auf 19133, danach Review und Chris-Freigabe."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "observer joined als OBSERVER auf 19133, level_name=ShowWorld, target_port=19133",
      "alle SHOW_ACTOR sichtbar und on_surface",
      "update_block am Zielpodest und mob_equipment des Begleit-Bots im Proof",
      "Bühne und Bots vollständig im 3D-ViewPort ohne undurchsichtige Blockierung"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19133,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "ops/runtime/latest-observer-proof.json",
      "npm run verify:show"
    ]
  },
  "risiken": [
    "FW-*-Vorlagenname und konkrete Rätselblöcke sind noch offen — nicht raten.",
    "Live-Port 19132 und UDP 7551 bleiben tabu.",
    "Live-Profile niemals auf 19133/19134; nur SHOW_ACTOR/OBSERVER.",
    "Vision/Screenshot nur als optionales Siegel nach grünem Stufe-1-Gate und im Budget."
  ],
  "freigabe": "manual",
  "id": "BP-001",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.382Z",
  "geaendertAm": "2026-11-23T00:48:40.382Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-002.md
================================================================================
# Bauplan BP-002

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-002.json
```json
{
  "titel": "Dankeschön-Oase (Showcase 19133)",
  "klartextziel": "Eine warme Überraschung für die Familie: Ein Kirschblüten-Pavillon mit Ruhe-Zone wird von einem höflichen Butler-Bot betreut, der per Blickkontakt und Emote begrüßt, Blumen und ein beschriebenes Dankes-Buch überreicht und auf Wunsch ein kleines Feuerwerk startet.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "ShowWorld auf 127.0.0.2:19133; konkrete FW-*-Vorlage noch nicht benannt (offene Frage 2026-11-23).",
  "quellen": [
    "vault/konzept/2026-11-20 showcase-dankeschoen-oase.md",
    "vault/konzept/03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md",
    "vault/konzept/02_ui-ux-architektur-briefing-leitstelle-v2.md",
    "ops/capabilities.json",
    "ops/WORLD_INVARIANTS.md",
    "ops/SYSTEM_SSOT.json"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "bedrock.sendEmote",
      "bedrock.holdItem",
      "bedrock.setBlock",
      "interaction.rotationTo",
      "interaction.handleEmote",
      "memory.WorldMemory"
    ],
    "fehlend": [
      "Semantic Zone 'Ruhe-Oase' nicht als Capability registriert",
      "Buchbeschreibung/Item-Übergabe nicht als Capability registriert",
      "Feuerwerk nicht als Capability registriert",
      "qa.acceptance (Backlog laut SPEC_MAPPING Abschnitt 4)"
    ],
    "hinweis": "Blumen, Buch und Feuerwerk sind Bühnenideen der Quelle; konkrete Items oder Rezepte dürfen erst nach Wissens-DB-Lookup verwendet werden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Oase und Ruhe-Zone",
      "inhalt": "Kirschblüten-Pavillon, Teich, Bank und Beleuchtung auf 19133; Block-Muster als geschützte Ruhe-Zone im Weltgedächtnis anlegen und per observer prüfen."
    },
    {
      "id": "M2",
      "titel": "Butler und Übergabe",
      "inhalt": "Blickkontakt, Höflichkeits-Emote, Blumenübergabe und vorbeschriebenes Dankes-Buch; Botschaft, Botname und Blumenauswahl bleiben anpassbare Parameter."
    },
    {
      "id": "M3",
      "titel": "Finale und Abnahme",
      "inhalt": "Feuerwerk nur nach grünem Stufe-1-Gate; Observer-Punktbeweise, ViewPort und Port 19133, danach Review."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "observer joined als OBSERVER auf 19133, level_name=ShowWorld, target_port=19133",
      "Butler-Bot sichtbar, on_surface und im Zuschauerbereich erkennbar",
      "update_block für Pavillon/Ruhe-Zone und mob_equipment für die Requisite im Proof",
      "Bühne und Butler vollständig im 3D-ViewPort ohne verdeckende Blöcke"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19133,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "ops/runtime/latest-observer-proof.json",
      "npm run verify:show"
    ]
  },
  "risiken": [
    "Nie Bots in die Ruhe-Zone laufen lassen, solange die Semantic-Zone-Regel nicht registriert und geprüft ist.",
    "Keine echten Konten, keine Screengrabs ohne Freigabe.",
    "Live-Port 19132 bleibt unangetastet.",
    "Emote-IDs nur aus der Wissens-DB, nicht raten."
  ],
  "freigabe": "manual",
  "id": "BP-002",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.393Z",
  "geaendertAm": "2026-11-23T00:48:40.393Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-003.md
================================================================================
# Bauplan BP-003

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-003.json
```json
{
  "titel": "Tanzshow-Mehrspieler (Showcase 19133)",
  "klartextziel": "Die vollständige Crew-Bot-Tanzshow: Anteroom mit beleuchtetem Weg, fertige Bühne vor dem Eintritt, Start per Chat !show/!tanzshow, vier Bots aus vier Richtungen, Jukebox-Ritual mit sichtbarer Schallplatte, koordinierte Choreografie, Verbeugung und geordneter Abgang.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "ShowWorld auf 127.0.0.2:19133 (levelName aus SSOT); Regieplan ops/qa/show-spec.tanzshow.json.",
  "quellen": [
    "vault/konzept/2026-11-21 tanzshow-regie-spezifikation-komplett.md",
    "vault/konzept/2026-11-21 tanzshow-familien-drehbuch.md",
    "ops/qa/show-spec.tanzshow.json",
    "ops/SPEC_MAPPING.md",
    "ops/WORLD_INVARIANTS.md",
    "ops/SYSTEM_SSOT.json"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "show.run",
      "bedrock.setBlock",
      "bedrock.useBlock",
      "bedrock.holdItem",
      "bedrock.sendEmote",
      "bedrock.walkTo",
      "interaction.rotationTo",
      "world.pathfinder.findPath",
      "memory.WorldMemory"
    ],
    "fehlend": [
      "stage.reset (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "observer.proof (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "qa.acceptance (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "bedrock.holdItemBroadcast (Backlog laut SPEC_MAPPING Abschnitt 4)"
    ],
    "hinweis": "music_disc_cat und Jukebox sind aus der Regie-Spezifikation belegt; Requisiten aus dem Familien-Drehbuch sind alternativ, nicht zusätzlich verbindlich."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Kulisse und Anteroom",
      "inhalt": "Bühnenpodest, Rückwand, Laternen, vier Kulissengassen und sicherer Anteroom mit beleuchtetem Weg zum Zuschauerbereich per setblock/fill; Bühne steht vor dem Betreten fertig."
    },
    {
      "id": "M2",
      "titel": "Vier-Richtungen-Auftritt und Jukebox-Ritual",
      "inhalt": "Vier Crew-Bots gleichzeitig aus N/O/S/W, DJ-Bot baut Jukebox, hält music_disc_cat sichtbar in der Hand und legt sie erst danach ein; kein Reconnect während der Szene."
    },
    {
      "id": "M3",
      "titel": "Choreografie, Finale, Abnahme",
      "inhalt": "Koordinierte Choreografie, Linie zum Publikum, Chat-Dank und geordneter Abgang; Wiederholbarkeit ohne Serverneustart; Observer-Proof und 3D-ViewPort als Tor."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "observer auf 19133 mit level_name=ShowWorld, target_port=19133, y-nahe Oberfläche (kein y≈32769)",
      "alle vier SHOW_ACTOR sichtbar, on_surface und in der Bühnen-Bounding-Box (Ausnahme nur intent.exceptions exit_stage)",
      "update_block für Bühne/Jukebox und mob_equipment für minecraft:music_disc_cat am DJ-Bot",
      "3D-ViewPort/Raycast grün für Bühne, Jukebox und alle vier Bots auf Fuß-/Rumpf-/Kopfhöhe"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25,
      "deckentoeheMin": 2.5
    },
    "port": 19133,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "ops/runtime/latest-observer-proof.json (Erfolgsbeleg aus SPEC_MAPPING: 254 update_block + 1 mob_equipment)",
      "npm run verify:show",
      "npm run show:accept"
    ]
  },
  "risiken": [
    "Reset-Wartezeit muss ausreichen, sonst überschreibt ein verspätetes air-Paket ein Kulissen-update_block (SPEC_MAPPING: 8000 ms).",
    "Vier Bots exakt; ältere 3-4-Bot-Fassung ist überholt.",
    "Vision/Screenshot nur optional nach grünem Stufe-1-Gate; ohne Freigabe kein Screengrab.",
    "19132/UDP 7551 nie berühren."
  ],
  "freigabe": "manual",
  "id": "BP-003",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.400Z",
  "geaendertAm": "2026-11-23T00:48:40.400Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-004.md
================================================================================
# Bauplan BP-004

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-004.json
```json
{
  "titel": "Flache Welt als Vorlage / FW-Namensschema + Reset (19134)",
  "klartextziel": "Flache Welten aus Chris' Minecraft-Installation als FW-*-Vorlagen in den zentralen Vorlagen-Pool übernehmen, auf dem E2E-Server 19134 wiederholt und bitgenau ausspielen und den Master-Reset inklusive Weltgedächtnis-Leerung und Event-Bus-Meldung beherrschbar machen.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "FW-<Name> (exakte Namen offen); Zielwelt auf 127.0.0.3:19134, nicht 19132.",
  "quellen": [
    "vault/konzept/Flache Welten als Vorlagen.md",
    "vault/konzept/Referenzanlage-B-Welt-Vorlagen-und-Reset.md",
    "vault/konzept/Referenzanlage-E-Welt-Gedaechtnis-und-Event-Bus.md",
    "vault/30 Szenarien/Weltvorlagen.md",
    "ops/WORLD_INVARIANTS.md",
    "ops/SYSTEM_SSOT.json"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "memory.WorldMemory",
      "world.worldGrid.isSolid"
    ],
    "fehlend": [
      "WorldTemplateManager.deployTemplate/resetToMaster nicht in capabilities.json registriert",
      "world.reset.completed Event-Bus-Meldung nicht als Capability registriert",
      "SHA256-Vorlagenprüfung nicht als Capability registriert",
      "qa.acceptance (Backlog laut SPEC_MAPPING Abschnitt 4)"
    ],
    "hinweis": "FW-Namen und Pfade sind aus der Quelle zu übernehmen; keine erfundenen Vorlagennamen oder Weltdateien anlegen."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Namensschema und Inventar",
      "inhalt": "FW-*-Namensschema und Parameter (Kreativ, Friedlich, Koordinaten an) dokumentieren; konkrete Vorlagennamen von Chris einholen."
    },
    {
      "id": "M2",
      "titel": "Staging-Deployment und Reset",
      "inhalt": "Kopie nach .stage-*, db/LOCK entfernen, levelname.txt setzen, atomarer rename, .world-origin.json; Reset aus Origin-Metadaten wiederholbar."
    },
    {
      "id": "M3",
      "titel": "E2E-Beweis auf 19134",
      "inhalt": "Weltgedächtnis leeren und world.reset.completed publizieren, bevor Bots joinen; Observer-Beweis und Port-Identität 19134 prüfen."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "observer joined als OBSERVER auf 19134, target_port=19134, korrekte Server-Identität",
      "alle SHOW_ACTOR sichtbar und on_surface nach dem Reset",
      "update_block der platzierten Vorlagenblöcke im Proof (nicht nur gesendeter Befehl)",
      "3D-ViewPort der Testwelt grün, Vorlagen-Hash vor/nach Reset dokumentiert"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "ops/runtime/latest-observer-proof.json",
      "SHA256 der Vorlage vor und nach dem Reset",
      "BDS-Log mit set_build_done"
    ]
  },
  "risiken": [
    "LevelDB-LOCK nur bei gestopptem Server entfernen; keine Weltdatei löschen.",
    "19132 ist tabu; UDP 7551 bleibt exklusiv dem Live-Server.",
    "Ohne Chris' Vorlagennamen keine echte FW-Vorlage ausspielen.",
    "Keine Weltdateien committen."
  ],
  "freigabe": "manual",
  "id": "BP-004",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.407Z",
  "geaendertAm": "2026-11-23T00:48:40.407Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-005.md
================================================================================
# Bauplan BP-005

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-005.json
```json
{
  "titel": "Observer-Proof + 3D-ViewPort E2E-Gate (19134)",
  "klartextziel": "Das harte Stufe-1-Gate für alle Showcases und Meilensteine maschinenlesbar absichern: separater observer-Bot, vier Gate-Punkte, 3D-ViewPort/Raycast und Port-Identität — ohne grünen Beweis kein Freigabestatus.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "E2E-Testwelt auf 127.0.0.3:19134; Gegenprobe ShowWorld auf 19133.",
  "quellen": [
    "ops/WORLD_INVARIANTS.md",
    "ops/qa/observer-proof.mjs",
    "ops/qa/world-invariants.mjs",
    "ops/SPEC_MAPPING.md",
    "vault/konzept/2026-11-21 dauerbetrieb-absturzschutz.md",
    "ops/SYSTEM_SSOT.json"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "show.run"
    ],
    "fehlend": [
      "observer.proof als eigener Capability-Eintrag (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "qa.acceptance als eigener Capability-Eintrag (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "Runtime-Truth-Abgleich der laufenden server.properties gegen die Port-Matrix (Gap laut SPEC_MAPPING Abschnitt 1)",
      "stage.reset als eigener Capability-Eintrag (Backlog laut SPEC_MAPPING Abschnitt 4)"
    ],
    "hinweis": "Gate-Parameter stammen aus WORLD_INVARIANTS §0–§2 und SSOT e2e_gate; Vision ist kein Ersatz für Stufe 1."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Vier Observer-Punkte",
      "inhalt": "Chunk-/Spawn-Init ohne Limbo, update_block- und mob_equipment-Paketbeweise, sichtbare Bots auf der Oberfläche, echte Server-Identität und Port (ShowWorld/19133 bzw. E2E/19134)."
    },
    {
      "id": "M2",
      "titel": "3D-ViewPort und Port-Gate",
      "inhalt": "Augenhöhe +1.62, ±45°/±35°, 3–32 Blöcke, Voxel-Raycast 0,25 Schritt, keine undurchsichtige Blockierung; False-Green gegen 19132 oder TestWorld hart ablehnen."
    },
    {
      "id": "M3",
      "titel": "Verankerung im Freigabeablauf",
      "inhalt": "Ergebnis an ops/blueprints/pipeline.mjs checkGates() anschließen; npm run verify:show als Pflicht, Vision nur optional im Kontingent (1/Meilenstein, 3/Tag)."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "observer joined als OBSERVER, target_port=19134, level_name der E2E-Welt (Gegenprobe 19133/ShowWorld)",
      "alle SHOW_ACTOR visible=true und on_surface=true",
      "update_block- und mob_equipment-Pakete im Proof, keine Spec-Seeding-Beweise",
      "3D-ViewPort inklusive Raycast ohne Röntgenblick"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "ops/runtime/latest-observer-proof.json",
      "npm run verify:show",
      "node ops/kilo-handler/vision-budget.mjs status"
    ]
  },
  "risiken": [
    "Ein Grün gegen 19132/TestWorld ist ein False-Green und sofortiger Abbruch.",
    "Maximal fünf Korrekturversuche je Gate-Loop, dann sauber dokumentieren.",
    "Kein Vision-Call vor grünem Stufe-1-Gate; bei Budget-Limit nicht blockieren, sondern melden.",
    "Schwere Läufe serialisiert, RAM-Grenzen laut SSOT."
  ],
  "freigabe": "manual",
  "id": "BP-005",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.414Z",
  "geaendertAm": "2026-11-23T00:48:40.414Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-006.md
================================================================================
# Bauplan BP-006

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-006.json
```json
{
  "titel": "VoxelCrew-Werkbank Bedien- und Freigabeablauf (7070)",
  "klartextziel": "Die Werkbank auf 127.0.0.1:7070 als klaren, kindgerechten Bedien- und Freigabeablauf nutzbar machen: Baupläne ansehen, E2E-Report einreichen, Review-Zustand anzeigen und Freigabe ausschließlich über den in der SSOT festgelegten Modus manual/auto_if_clear.",
  "abnahmeHinweis": "Die endgültige Meilenstein-Reihenfolge und die Abnahmekriterien werden erst gemeinsam mit Chris über die offenen Fragen und Handlungen festgelegt.",
  "weltvorlage": "keine (GUI); E2E-Belege werden auf 19134 geführt.",
  "quellen": [
    "ops/SYSTEM_SSOT.json (werkbank: Port 7070, login=false, sse=true, freigabeStandard=manual)",
    "ops/blueprints/pipeline.mjs",
    "ops/SPEC_MAPPING.md",
    "vault/konzept/02_ui-ux-architektur-briefing-leitstelle-v2.md",
    "vault/konzept/Referenzanlage-F-Leitstellen-UI-Konzept.md",
    "AGENTS.md §10.4 und §11"
  ],
  "capabilityAbgleich": {
    "vorhanden": [],
    "fehlend": [
      "Werkbank-Serverdatei ops/werkbank/server.mjs im Stand 2026-11-23 nicht verifiziert",
      "blueprint.pipeline als Capability noch nicht in capabilities.json registriert",
      "qa.acceptance (Backlog laut SPEC_MAPPING Abschnitt 4)",
      "Browser-E2E der Werkbank noch nicht als Capability dokumentiert"
    ],
    "hinweis": "package.json nennt den Aufrufpfad werkbank; eine existierende Implementierung wurde nicht angenommen und nicht verändert."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Registry und Freigabemodus",
      "inhalt": "Baupläne aus registry.json/BP-XXX.json listen, Freigabemodus aus SSOT lesen, Moduswechsel nur mit gültigem Wert und nachvollziehbarem Event."
    },
    {
      "id": "M2",
      "titel": "Einreichen und Freigeben",
      "inhalt": "E2E-Report an checkGates() binden, Status review → wartet_auf_freigabe oder freigegeben, manuelle Freigabe nur mit by (Mastermind/Chris); keine Worker-Dispatches."
    },
    {
      "id": "M3",
      "titel": "Bedien- und Browserabnahme",
      "inhalt": "Klickbare Karten, Ampelstatus und 0 Browser-Konsolenfehler/0 fehlgeschlagene API-Requests gemäß AGENTS §10.4; Gegenprobe der Gates auf 19134."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "Gates des Bauplans BP-005 auf 19134 grün, bevor ein Werkbank-Status freigegeben wird",
      "SHOW_ACTOR/OBSERVER-Isolation unverändert",
      "Paketbeweise aus latest-observer-proof.json",
      "ViewPort-Check des E2E-Gates übernommen"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "npm run werkbank:e2e",
      "npm run test:browser",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Kein Login laut SSOT — keine Auth-, Token- oder Kontoänderung.",
    "UI-Änderungen brauchen den echten Browser-Test, nicht nur Quelltext-Tests.",
    "Kein Productionsserver, kein Port 7070 ohne eigenen Freigabe-Task starten.",
    "Freigabe niemals durch den Agenten selbst setzen; Modus Manual bleibt Default."
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-006",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T00:48:40.420Z",
  "geaendertAm": "2026-11-23T00:48:40.420Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-007.md
================================================================================
# Bauplan BP-007

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-007.json
```json
{
  "titel": "Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung",
  "klartextziel": "Vier wiederverwendbare Bausteine schaffen, mit denen das System Zufallswelten selbstständig erkundet, Bühnen sanft an das Gelände anpasst, drei gekoppelte Realitätsbeweise über mindestens drei Seeds führt und die Vorführung aus echter Spielersicht prüft — weiterhin auf manual, ohne Eingriff in 19132.",
  "weltvorlage": "Per level-seed generierte Gelände-/Biom-Welten auf 19134 (mindestens 3 Seeds), atomarer Master-Reset mit automatischer Platten-Bereinigung nach jedem Lauf",
  "quellen": [
    "ops/SYSTEM_SSOT.json → regieLeitplanken (A–D), welten.masterReset, e2e_gate",
    "AGENTS.md §12 Regie-Leitplanken A–D, §10 QA-Gate, §11 End-to-End-Produktdenken",
    "vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md",
    "ops/WORLD_INVARIANTS.md §0 Transportmatrix und §Observer-Paketbeweise",
    "ops/capabilities.json und ops/SPEC_MAPPING.md (Abschnitt 5b)"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "capabilities.registry (ops/capabilities.json)",
      "world.chooseAndReset / masterReset (ops/wissenanalyse/pipeline.mjs)",
      "qa.voxelRaycast (ops/qa/world-invariants.mjs)",
      "qa.observerProof (ops/qa/observer-proof.mjs)",
      "actor.profileGuard (ops/lib/actor-profile-guard.cjs)",
      "blueprint.pipeline (ops/blueprints/pipeline.mjs)"
    ],
    "fehlend": [
      "A: cap.gapPlanner als ausführbarer Vorab-Check",
      "B: director.adaptiveStage als 32×32-Sektor-Regiebaustein",
      "C: qa.tripleRealityGate + seedOrchestrator (N ≥ 3)",
      "D: empathy.playerSimulator und behaviourPack.clientBridge"
    ],
    "hinweis": "Die vier fehlenden Grundfähigkeiten werden in M1–M4 als wiederverwendbare Bausteine herausgeschnitten. BP-007 bleibt status entwurf und freigabe manual."
  },
  "meilensteine": [
    {
      "id": "A",
      "titel": "Capability- & Gap-Planner",
      "inhalt": "Vor jedem Vorhaben ops/capabilities.json automatisiert gegen die Anforderung prüfen („Kann ich schon\" vs. „Fehlt mir noch\"). Fehlende Grundfähigkeiten als wiederverwendbare Bausteine herausschneiden und mit source/begruendung in ops/SPEC_MAPPING.md dokumentieren. Beleg: lauffähiger Vorab-Check mit klarer Gap-Liste, kein Ad-hoc-Skript."
    },
    {
      "id": "B",
      "titel": "Adaptive Scene Director (32×32)",
      "inhalt": "Zufallswelten in 32×32-Sektoren erkunden, tragfähige Flächen finden und Bühnen sanft an die Topografie anpassen (Podest/Stufen statt Betonklotz). Verdeckende Blöcke und die Zuschauer-Loge per 3D-ViewPort-Raycast automatisch korrigieren (Augenhöhe y+1,62, ±45°/±35°, 3–32 Blöcke). Beleg: Observer-Proof vor/nach der Korrektur."
    },
    {
      "id": "C",
      "titel": "Dreifache Realitäts-Kopplung & Multi-Seed-Gate (N ≥ 3)",
      "inhalt": "Ein Lauf gilt nur bei gleichzeitigen Beweisen: Blockdaten, freier 3D-Raycast und bequeme Erreichbarkeit (fester Boden, 2 Blöcke Kopffreiheit, begehbarer Pfad). Auf VoxelCrew:Ende (19134) selbstständig in mindestens 3 unterschiedlichen Zufalls-Seeds bestehen (z. B. Wald/Ebene, Hügelland, Küste/Wüste). Alte Test-Platten nach jedem Lauf automatisch per atomarem Master-Reset bereinigen. Beleg: Gate-Report mit 3 Seed-IDs und 3/3-Beweisen."
    },
    {
      "id": "D",
      "titel": "User-Empathy-Simulator & BehaviourPack↔Client-Symbiose",
      "inhalt": "Unprivilegierter Spieler-Simulator auf 19134 prüft Erreichbarkeit, Sichtfeld (FOV 70°) und ruhiges Vorführ-Tempo aus 10-Jahre-Spieler-Perspektive. BehaviourPack-Effekte/UI (@minecraft/server, @minecraft/server-ui) mit Client-Bot-Aktionen (SWARM_BOT) sauber verzahnen; SWARM_BOT bleibt Live-Profil und nie auf 19133/19134 eingeloggt — dort SHOW_ACTOR/OBSERVER. Beleg: Symbiose-Nachweis ohne Akteurs-Isolationsverstoß."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "A: Gap-Liste aus ops/capabilities.json mit Quellen und wiederverwendbarem Baustein",
      "B: 32×32-Sektor-Protokoll und Raycast-Korrektur der Loge mit Observer-Proof",
      "C: Blockdaten + freier Raycast + bequeme Erreichbarkeit gleichzeitig in ≥3 Seeds auf 19134",
      "D: Empathy-Simulator mit FOV 70° und BP↔Client-Symbiose ohne Akteursverstoß",
      "Kein grünes Gate ohne alle drei C-Beweise; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-227.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Freigabe bleibt manual — kein Agent setzt BP-007 selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (PID 18356, UDP 7551, LAN true); keine Weltdaten- oder Auth-Cache-Löschung.",
    "Multi-Seed-Läufe brauchen atomare Platten-Bereinigung, sonst Verfälschung durch alte Test-Welten.",
    "SWARM_BOT ist Live-Profil und darf nie auf 19133/19134 eingeloggt werden.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-007",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T09:37:14.497Z",
  "geaendertAm": "2026-11-23T09:37:14.497Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-008.md
================================================================================
# Bauplan BP-008

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-008.json
```json
{
  "titel": "Sensorik & Wahrnehmung — 64×36-Tiefenkarte und Gesten",
  "klartextziel": "Wenn ein Bot zum ersten Mal in eine neue Welt schaut, will ich, dass er jede halbe Sekunde sieht, was direkt vor ihm ist — damit er nicht gegen Blöcke rennt und versteht, wenn ich winke, klatschen oder „Folge mir\" zeige.",
  "weltvorlage": "E2E-Testserver 19134, frische level-seed-Welten (Wald/Ebene, Hügelland, Küste), atomarer Master-Reset nach jedem Lauf",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/01_architektur-briefing-multi-bot-bedrock-v14.md (64×36-ToF alle 500 ms)",
    "vault/konzept/06_referenz-fast-voxel-raycaster-tof.md (Amanatides & Woo, Zero-Allocation)",
    "vault/konzept/Anlage-03-revision-v1-chris-protokoll-matrix.md"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "qa.voxelRaycast (ops/qa/world-invariants.mjs)",
      "interaction.resolveGesture / interaction.handleEmote",
      "bedrock.sendEmote",
      "world.worldGrid.isSolid"
    ],
    "fehlend": [
      "percept.tofDepth64x36 (500 ms, Zero-Allocation)",
      "percept.gestureRingbuffer (≤12 Blöcke, 2,5 s)",
      "drei Emote-UUIDs live erfassen (Winken/Klatschen/Folge-mir — nicht raten)"
    ],
    "hinweis": "Der QA-Gate-Raycast ist bewährt, aber nicht der kontinuierliche Wahrnehmungssensor. Emote-UUIDs werden live erhoben, nie erfunden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Tiefenkarte 64×36",
      "inhalt": "Zero-Allocation-Raycaster mit Augenhöhe als Startpunkt, alle 500 ms, Reichweite aus der Referenz — kein geratener Wert."
    },
    {
      "id": "M2",
      "titel": "Gesten-Ringpuffer",
      "inhalt": "Erkannte Gesten ≤12 Blöcke in einem 2,5-s-Ringpuffer sammeln und an den Executive Loop weitergeben."
    },
    {
      "id": "M3",
      "titel": "Emote-UUIDs live erfassen",
      "inhalt": "Winken, Klatschen und „Folge mir\" aus dem echten Client protokollieren; fehlende Einträge als offen markieren."
    },
    {
      "id": "M4",
      "titel": "Wahrnehmungs-Beweis",
      "inhalt": "Observer-Proof auf 19134: Tiefenkarte + Gestenereignis mit echtem Paketbeweis, kein Befehlssenden."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: 64×36-Tiefenkarte mit Zeitstempel 500 ms und Augenhöhe",
      "M2: Gestenereignis aus dem 2,5-s-Ringpuffer (≤12 Blöcke)",
      "M3: drei live erfasste Emote-UUIDs mit Quelle",
      "M4: Observer-Paketbeweis auf 19134",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "BP-007 bleibt manual; BP-008 baut auf dessen Regie-Leitplanken auf, greift aber nicht in 19132 ein.",
    "Keine Reichweiten, Emote-UUIDs oder Block-IDs raten — fehlende Werte bleiben offen.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-007"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-008",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.168Z",
  "geaendertAm": "2026-11-23T10:15:55.168Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-009.md
================================================================================
# Bauplan BP-009

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-009.json
```json
{
  "titel": "Executive Loop — Sense→Think→Act→Verify und sieben Handlungsgruppen",
  "klartextziel": "Wenn ich dem Bot einen Auftrag gebe, will ich, dass er ihn in kleinen Schritten erledigt und nach jedem Schritt selbst nachschaut, ob es wirklich passiert ist — damit er nicht „fertig\" sagt, obwohl nichts passiert ist.",
  "weltvorlage": "E2E-Testserver 19134, wiederholbare Szenarien mit atomarem Master-Reset",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/Anlage-03-revision-v1-chris-protokoll-matrix.md (PRE_CHECK→TIMING→APPROACH→EXECUTE 50 ms→VERIFY_AND_COMMIT)",
    "vault/konzept/00_README_KONZEPT_INDEX.md (Autonomous End-to-End Execution)"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "qa.definitionOfReady / atomicDiffBudget / contractFirst / preflight",
      "bedrock.sendChat / sendEmote / commandInteraction / walkTo / holdItem / useBlock / setBlock",
      "interaction.handleEmote"
    ],
    "fehlend": [
      "exec.hfsm50ms als laufender Sense→Think→Act→Verify-Zyklus",
      "exec.handlungsgruppen7 als generische Gruppierung",
      "exec.verificationCommit mit echter Server-Quittung (UpdateBlock/MobEquipment)"
    ],
    "hinweis": "Nur registrierte Capabilities mit grünem Beleg zählen als vorhanden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "HFSM-Grundtakt",
      "inhalt": "50-ms-Zyklus mit den fünf Phasen aus Anlage 03; jede Phase hat einen klaren Zustandsübergang."
    },
    {
      "id": "M2",
      "titel": "Sieben Handlungsgruppen",
      "inhalt": "Die vorhandenen Primitives in sieben wiederverwendbare Gruppen fassen, ohne neue Abhängigkeiten."
    },
    {
      "id": "M3",
      "titel": "Echte Quittierung",
      "inhalt": "Erfolg erst nach echter Server-Quittung, nie nach gesendetem Befehl (Payload-Beweis)."
    },
    {
      "id": "M4",
      "titel": "Loop-Beweis",
      "inhalt": "Observer-Proof auf 19134: vollständige Journey vom Auftrag bis zur quittierten Änderung."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: 50-ms-Taktprotokoll mit Phasenfolge",
      "M2: sieben Gruppen gegen die registrierten Primitives",
      "M3: UpdateBlock/MobEquipment im echten Paketstrom",
      "M4: Journey ohne Reconnect mitten in der Szene",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Keine Selbstbenotung durch Befehlssenden — nur echte Quittung zählt.",
    "Kein Reconnect mitten in Szenen (WORLD_INVARIANTS).",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-008"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-009",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.191Z",
  "geaendertAm": "2026-11-23T10:15:55.191Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-010.md
================================================================================
# Bauplan BP-010

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-010.json
```json
{
  "titel": "Shared Weltgedächtnis, Blockmuster und 2.5D-Weltkarte",
  "klartextziel": "Wenn mehrere Bots gleichzeitig bauen, will ich, dass sie sich gemeinsam merken, was schon da ist — damit keiner kaputt macht, was ein anderer gerade gebaut hat.",
  "weltvorlage": "E2E-Testserver 19134, kuratierte Blockmuster aus vault/20 Wissen/Minecraft/Daten/kuratiert/**",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/2026-11-19 block-muster-und-2-5d-weltkarte.md",
    "vault/konzept/Referenzanlage-E-Welt-Gedaechtnis-und-Event-Bus.md",
    "vault/20 Wissen/Minecraft/README.md (SSOT, Vertrauensstufen)"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "memory.WorldMemory (SQLite, lokal, Marker überleben Neustart)",
      "ops/qa/world-invariants.mjs (Blockdaten + Sichtlinie)",
      "vault-Hygiene: source und begruendung je Kuratierung"
    ],
    "fehlend": [
      "memory.sharedWorldModel (eine Instanz pro Welt, alle Bots)",
      "memory.blockPatternLibrary (nur kuratierte Muster)",
      "map.twoPoint5D (Weltkarte für die Leitstelle)"
    ],
    "hinweis": "Keine Block-IDs, Härtewerte oder Rezepte raten; fehlende Daten werden als offener Punkt markiert."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Gemeinsames Weltmodell",
      "inhalt": "Bestehendes WorldMemory auf eine geteilte, pro Welt isolierte Instanz erweitern (world_context_id)."
    },
    {
      "id": "M2",
      "titel": "Blockmuster-Bibliothek",
      "inhalt": "Nur kuratierte Muster mit source und begruendung; kein geratenes Rezept."
    },
    {
      "id": "M3",
      "titel": "2.5D-Weltkarte",
      "inhalt": "Weltkarte für die Leitstelle aus dem gemeinsamen Modell speisen — echte Daten, keine Platzhalter."
    },
    {
      "id": "M4",
      "titel": "Gedächtnis-Beweis",
      "inhalt": "Observer-Proof auf 19134: zwei Bots, ein gemeinsames Modell, kein Überschreiben."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: world_context_id-Isolation mit zwei Bots",
      "M2: Blockmuster mit source+begruendung",
      "M3: 2.5D-Karte mit echten Blockdaten (4-Zustands-Vertrag)",
      "M4: geteilter Beweis ohne Datenverlust",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Vault-Schreibbereich nur kuratiert/**; Daten/extrahiert/**, Index/**, Schema.md tabu.",
    "Bei Master-Reset Gedächtnis leeren und world.reset.completed publizieren, bevor Bots joinen.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-008",
    "BP-009"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-010",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.227Z",
  "geaendertAm": "2026-11-23T10:15:55.227Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-011.md
================================================================================
# Bauplan BP-011

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-011.json
```json
{
  "titel": "In-Game-UI & Pure-Client-Fallback",
  "klartextziel": "Wenn ich im Spiel neben dem Bot stehe, will ich mit einem großen Hilfeknopf und höchstens drei Klicks sehen, was er gerade macht — damit ich auch ohne Computer schnell helfen kann, wenn etwas schiefgeht.",
  "weltvorlage": "E2E-Testserver 19134, Bühnenprofil SHOW_ACTOR/OBSERVER (SWARM_BOT bleibt Live-Profil)",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/02_ui-ux-architektur-briefing-leitstelle-v2.md",
    "vault/handbuch/07_Vier_Stufen_UI_Prozess.md (anlage-07-Wireframe)",
    "ops/SYSTEM_SSOT.json → uiProzess"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "uiProzess (4 Pflichtstufen samt 4-Zustands-Vertrag) in AGENTS.md §13/SSOT/Handbuch 07",
      "hilfe.botHilfe (60-s-Clip + Eskalationskarte) in Werkbank und 2ndScreen-Leitstelle",
      "tests/cockpit-browser-e2e.test.js + tests/werkbank-browser-e2e.test.js (0 Konsolenfehler, 0 API-Fehler)"
    ],
    "fehlend": [
      "ui.ingameServerUi (@minecraft/server-ui)",
      "ui.pureClientFallback ohne Script-API",
      "ui.kompassNotfallknopf im Spiel mit 60-s-Clip"
    ],
    "hinweis": "Erst die vier Stufen (Job-to-be-Done, 4-Zustands-Vertrag, Wireframe+Vertrag, Profi-Abnahme) dokumentieren — dann Code schreiben."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Stufen 1–3 vor dem Coding",
      "inhalt": "Ein-Satz-Job ab 10 Jahren, 4-Zustands-Vertrag und ASCII-Wireframe nach anlage-07 mit exaktem API-/IPC-Vertrag."
    },
    {
      "id": "M2",
      "titel": "In-Game-Oberfläche",
      "inhalt": "@minecraft/server-ui für das Bühnenprofil; keine Live-Profile auf 19133/19134."
    },
    {
      "id": "M3",
      "titel": "Pure-Client-Fallback",
      "inhalt": "Gleiche vier Zustände ohne Script-API, damit die Oberfläche nie rohes Markdown zeigt."
    },
    {
      "id": "M4",
      "titel": "Stufe-4-Abnahme",
      "inhalt": "Playwright-Test mit Screenshot-Beleg, 10-Jahre-Check, großer Bot-Hilfe-Knopf mit 60-s-Clip auf 19134."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: Wireframe + Vertrag vor dem Coding abgelegt",
      "M2: Widget aus @minecraft/server-ui im Paketstrom",
      "M3: Fallback zeigt gefüllt/leer/lädt/handlungsbedarf",
      "M4: Screenshot-Beleg, 0 Konsolenfehler, 0 API-Fehler",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "server/** bleibt bis zur ausdrücklichen Freigabe dieses Bauplans unangetastet.",
    "SWARM_BOT ist Live-Profil und darf nie auf 19133/19134 eingeloggt werden.",
    "Keine Dateipfade oder Tech-Kürzel im Haupttext der Oberfläche.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-009"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-011",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.240Z",
  "geaendertAm": "2026-11-23T10:15:55.240Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-012.md
================================================================================
# Bauplan BP-012

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-012.json
```json
{
  "titel": "MSA & Co-Pilot-Relay (nur nach Chris-Tor)",
  "klartextziel": "Wenn ich von einem zweiten Gerät zuschauen will, will ich, dass die Bots nur bei uns zu Hause sicher miteinander reden — damit niemand von außen die Steuerung übernehmen kann.",
  "weltvorlage": "Lokales Relay auf dem Hauptrechner/FELDBOOK, strikt getrennte Betriebsmodi und world_context_id",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md",
    "vault/konzept/2026-11-19 bot-relay-und-copilot-modus.md",
    "vault/konzept/01_architektur-briefing-multi-bot-bedrock-v14.md (vier Betriebsmodi)"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "ops/lib/actor-profile-guard.cjs (Akteurs-Isolation)",
      "ops/SYSTEM_SSOT.json (Modi, Maschinen, Serverrollen)",
      "ops/runtime/vault-git-sync.mjs (lokaler Git-Write-Lock)"
    ],
    "fehlend": [
      "relay.localWsRest (nur Loopback, keine öffentliche Erreichbarkeit)",
      "relay.copilotStufen",
      "relay.modusIsolation im Nachrichtenpfad"
    ],
    "hinweis": "Nur registrierte Capabilities mit grünem Beleg zählen als vorhanden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Lokaler Transport",
      "inhalt": "WebSocket/REST nur auf Loopback, ohne Öffnung nach außen und ohne neue Abhängigkeit."
    },
    {
      "id": "M2",
      "titel": "Co-Pilot-Stufen",
      "inhalt": "Stufen der Unterstützung klar begrenzt; jede Nachricht trägt Modus und world_context_id."
    },
    {
      "id": "M3",
      "titel": "Isolations-Beweis",
      "inhalt": "Nachricht aus Modus A erreicht nie Modus B; Actor-Profile-Guard bleibt vor jedem Verbindungsaufbau aktiv."
    },
    {
      "id": "M4",
      "titel": "Relay-Beweis",
      "inhalt": "Observer-Proof auf 19134: Relay-Nachricht → echte Bot-Quittung, kein externer Call."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: nur Loopback-Bindings nachgewiesen",
      "M2: Modus und Kontext in jeder Nachricht",
      "M3: Isolationsverstoß bleibt rot",
      "M4: Paketbeweis ohne öffentlichen Endpunkt",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Öffentliche Erreichbarkeit, Ports und Firewall sind ein hartes Außen-Risiko (AGENTS.md §7) — nur mit Chris-Tor.",
    "Keine echten Konten, Tokens oder Zugangsdaten.",
    "Freigabe bleibt manual; kein heimlicher Start des Relays.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-010",
    "BP-011"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-012",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.253Z",
  "geaendertAm": "2026-11-23T10:15:55.253Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-013.md
================================================================================
# Bauplan BP-013

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-013.json
```json
{
  "titel": "Ikonischer Resource-Pack-Generator",
  "klartextziel": "Wenn die Bots etwas bauen, will ich, dass die Dinge im Spiel so aussehen wie auf unseren kleinen Bildern — damit ich auf einen Blick sehe, was gemeint ist.",
  "weltvorlage": "Kuratiertes Pack aus vault/20 Wissen/Minecraft/Daten/kuratiert/**, getestet auf 19134",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/Anlage-08-resource-pack-und-namensgebung.md",
    "vault/konzept/Anlage-09-resource-pack-generierung.md",
    "vault/20 Wissen/Minecraft/README.md"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "memory.blockPatternLibrary (aus BP-010, kuratiert)",
      "ops/capabilities.json (Block-IDs nur mit Beleg)",
      "werkbank icons aus echten Daten (4-Zustands-Vertrag)"
    ],
    "fehlend": [
      "pack.iconGenerator",
      "pack.textureAtlas",
      "pack.quellenPflicht (source + begruendung je Asset)"
    ],
    "hinweis": "Jedes Asset braucht source und begruendung. Fehlende Rezepte/IDs bleiben offen — kein Raten."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Kurateliste",
      "inhalt": "Nur kuratierte Blockmuster mit Quelle; unklare Fälle als offener Punkt markieren."
    },
    {
      "id": "M2",
      "titel": "Icon-Generator",
      "inhalt": "Aus der Kurateliste ikonische Symbole erzeugen, die zum Bühnenstil passen."
    },
    {
      "id": "M3",
      "titel": "Pack-Beweis",
      "inhalt": "Auf 19134 laden und per Observer-Paketbeweis prüfen; kein Screengrab ohne Freigabe."
    },
    {
      "id": "M4",
      "titel": "Rückfluss",
      "inhalt": "Neue kuratierte Muster mit source+begruendung in den Vault (nur kuratiert/**)."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: Kurateliste mit source+begruendung",
      "M2: erzeugte Icons gegen die Liste",
      "M3: geladenes Pack auf 19134 mit Paketbeweis",
      "M4: Vault-Kuratierung ohne Schema-/Index-Änderung",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Keine Rezepte, IDs oder Härtewerte raten.",
    "Keine Weltdateien oder Screengrabs committen.",
    "Vision-Screenshot nur als optionales Siegel nach grünem Stufe-1-Gate.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-010"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-013",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.264Z",
  "geaendertAm": "2026-11-23T10:15:55.264Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-014.md
================================================================================
# Bauplan BP-014

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-014.json
```json
{
  "titel": "Tribes/Frontier & Weltgedächtnis",
  "klartextziel": "Wenn viele Bots in verschiedenen Welten spielen, will ich, dass jede Gruppe ihre eigene Welt und ihr eigenes Gedächtnis hat — damit nichts durcheinanderkommt.",
  "weltvorlage": "Eine Instanz pro Welt auf 19134, SQLite-Weltgedächtnis, atomarer Master-Reset",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md",
    "vault/entscheidungen/ADR-005-Mehrbot-Betrieb.md",
    "vault/konzept/Modul-und-Vision-Tribes.md"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "memory.WorldMemory (SQLite pro Welt)",
      "ADR-005 (geteiltes WorldMemory, max-players deckt Bot-Anzahl)",
      "world.chooseAndReset (Master-Reset)"
    ],
    "fehlend": [
      "tribes.sqliteWorldMemory (eine Instanz pro Welt)",
      "tribes.frontierBoundaries",
      "tribes.zahlenUndIds offen — live klären, nicht raten"
    ],
    "hinweis": "Alle Tribes-/Frontier-Zahlen und IDs bleiben offen, bis sie aus einer kuratierten Quelle oder dem echten System belegt sind."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Instanz pro Welt",
      "inhalt": "Jede Welt bekommt genau eine SQLite-Instanz; world_context_id trennt sie strikt."
    },
    {
      "id": "M2",
      "titel": "Frontier-Grenzen",
      "inhalt": "Grenzen aus echten Weltdaten ableiten; keine erfundenen Koordinaten."
    },
    {
      "id": "M3",
      "titel": "Relay-Anbindung",
      "inhalt": "Gruppen über das lokale Relay aus BP-012 bedienen, ohne neue Ports nach außen."
    },
    {
      "id": "M4",
      "titel": "Tribes-Beweis",
      "inhalt": "Observer-Proof auf 19134: zwei Welten, kein Gedächtnis-Überlauf, Reset sauber."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: eine Instanz pro Welt im Report",
      "M2: Grenzen aus echten Blockdaten",
      "M3: Relay-Nachricht mit korrekter world_context_id",
      "M4: zwei Seeds ohne Gedächtnisvermischung",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Offene Zahlen/IDs nicht raten — als offene Punkte dokumentieren.",
    "Kein Eingriff in 19132 oder echte Konten.",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-010",
    "BP-012"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-014",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.274Z",
  "geaendertAm": "2026-11-23T10:15:55.274Z"
}
```


================================================================================
## DATEI: 2026-11-23__BP-015.md
================================================================================
# Bauplan BP-015

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/blueprints/BP-015.json
```json
{
  "titel": "Showcase-Regie & Review-Rückfluss",
  "klartextziel": "Wenn die Vorführung vorbei ist, will ich, dass die Bots selbst aufschreiben, was den Zuschauern gefallen hat — damit die nächste Show automatisch besser wird, ohne dass ich im Spiel tippen muss.",
  "weltvorlage": "Trockenlauf auf 19134, erlebbarer Abnahme-Meilenstein auf 19133",
  "quellen": [
    "ops/capabilities.json (Inventurstand 2026-11-23) und ops/SPEC_MAPPING.md §11",
    "vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md",
    "AGENTS.md §10 QA-Gate, §11 End-to-End-Produktdenken, §12 Regie-Leitplanken",
    "vault/konzept/10_referenz-showcase-regie-und-review-schema.md",
    "ops/PRAESENTATION.md",
    "ops/SPEC_MAPPING.md §9 (vollständige User-Journey)"
  ],
  "capabilityAbgleich": {
    "vorhanden": [
      "show.run (programmatischer Trigger POST /api/show/run)",
      "qa.acceptance (npm run show:accept)",
      "ops/qa/observer-proof.mjs + world-invariants.mjs (4 Gates grün)",
      "npm run verify:show (Observer-Gate)"
    ],
    "fehlend": [
      "regie.showcaseScript (stage/cast/acts + Kamera-Cues)",
      "qa.reviewIngestion (Feedback → Bauplan-Rückfluss)",
      "wizard.suite aus UI/UX v2"
    ],
    "hinweis": "Nur registrierte Capabilities mit grünem Beleg zählen als vorhanden."
  },
  "meilensteine": [
    {
      "id": "M1",
      "titel": "Trockenlauf",
      "inhalt": "showcase_script.json vorab auf 19134 ausführen, Beweise nur aus Observer-Sicht."
    },
    {
      "id": "M2",
      "titel": "Abnahme auf 19133",
      "inhalt": "Erlebbarer Meilenstein mit 4 grünen Gates; Trigger programmatisch, kein menschlicher Klicker."
    },
    {
      "id": "M3",
      "titel": "Review-Rückfluss",
      "inhalt": "Feedback aus Maske/In-Game-UI in review_submissions schreiben und als neue Bauplan-Version sichtbar machen."
    },
    {
      "id": "M4",
      "titel": "Wizard-Suite",
      "inhalt": "Leitstellen-Wizards aus UI/UX v2 mit 4-Stufen-Prozess und Bot-Hilfe-Knopf ergänzen."
    }
  ],
  "e2eKriterien": {
    "observerPunkte": 4,
    "observerBelege": [
      "M1: Trockenlauf-Beweis auf 19134 (update_block/mob_equipment)",
      "M2: 4 Gates grün auf 19133, target_port=19133",
      "M3: Review-Eintrag erzeugt neue Bauplan-Version",
      "M4: Wizard-Journey mit Screenshot-Beleg, 0 Konsolenfehler",
      "Kein grünes Gate ohne echten Paketbeweis; keine Selbstbenotung durch Befehlssenden"
    ],
    "viewPort": {
      "augenhoeheOffset": 1.62,
      "horizontalGrad": 45,
      "vertikalGrad": 35,
      "distanzBlocks": [
        3,
        32
      ],
      "raycastSchritt": 0.25
    },
    "port": 19134,
    "showcasePort": 19133,
    "e2ePort": 19134,
    "belege": [
      "node ops/scripts/check-e2e-server.mjs",
      "npm test",
      "npm run verify -- --out ops/results/T-228.verify.json",
      "node ops/scripts/rebuild-verify.mjs --live",
      "ops/blueprints/events.json"
    ]
  },
  "risiken": [
    "Die finale Einladung zur fertigen Live-Präsentation ist ausschließlich Chris vorbehalten (AGENTS.md §7 Punkt 4).",
    "Keine Screengrabs/externen Bilder ohne Freigabe; Vision nur als optionales Siegel nach grünem Stufe-1-Gate.",
    "19132 bleibt tabu; nur 19133 (Show) und 19134 (E2E).",
    "Freigabe bleibt manual — kein Agent setzt den Bauplan selbst frei.",
    "Kein Eingriff in den Live-Server 19132 (UDP 7551, LAN true); keine Weltdaten-/Auth-/Secret-Änderung.",
    "Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine Safety-/Auth-Änderung."
  ],
  "haengtVon": [
    "BP-011",
    "BP-014",
    "BP-007"
  ],
  "bedienPort": 7070,
  "freigabe": "manual",
  "id": "BP-015",
  "status": "entwurf",
  "version": 1,
  "erstelltAm": "2026-11-23T10:15:55.285Z",
  "geaendertAm": "2026-11-23T10:15:55.285Z"
}
```


================================================================================
## DATEI: 2026-11-23__capabilities.md
================================================================================
# Fähigkeits-Inventur

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/capabilities.json
```json
{
  "schema": "capability-registry.v1",
  "stand": "2026-11-23",
  "capabilities": [
    {
      "id": "bedrock.sendChat",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "sendChat",
      "zeile": 1356,
      "parameter": [
        {
          "name": "text",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Zu sendende Chat-Nachricht"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Schritt-3A-Tests 6, 10, 11, 12), BedrockConnector.js:1356"
    },
    {
      "id": "bedrock.sendEmote",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "sendEmote",
      "zeile": 1443,
      "parameter": [
        {
          "name": "gestureName",
          "typ": "string (wave|simple_clap|follow_me|over_there|double_jump)",
          "pflicht": true,
          "beschreibung": "Gestenname, der über data/interaction.yaml in eine Emote-ID aufgelöst wird"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Emotes.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 8), BedrockConnector.js:1443"
    },
    {
      "id": "bedrock.commandInteraction",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "commandInteraction",
      "zeile": 1503,
      "parameter": [
        {
          "name": "action",
          "typ": "string (follow|stop|escort|guard|attention)",
          "pflicht": true,
          "beschreibung": "Interaktionsaktion aus der Web-/Chat-Steuerung"
        },
        {
          "name": "payload",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "Zusatzdaten (playerName, x, y, z für escort)"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 5, 9), BedrockConnector.js:1503"
    },
    {
      "id": "bedrock.executeInteractionCommand",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "executeInteractionCommand",
      "zeile": 1495,
      "parameter": [
        {
          "name": "command",
          "typ": "string (follow|stop|escort|guard|attention)",
          "pflicht": true,
          "beschreibung": "Internes Interaktionskommando"
        },
        {
          "name": "payload",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "Payload mit playerName, player, lookTarget, center"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 5, 6, 7, 9), BedrockConnector.js:1495"
    },
    {
      "id": "bedrock.walkTo",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "walkTo",
      "zeile": 1845,
      "parameter": [
        {
          "name": "targetPos",
          "typ": "{x:number, y:number, z:number}",
          "pflicht": true,
          "beschreibung": "Zielkoordinaten"
        },
        {
          "name": "stopDistance",
          "typ": "number",
          "pflicht": false,
          "standard": 3,
          "beschreibung": "Mindestabstand zum Ziel, ab dem als erreicht gilt"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/step3a.test.js (Schritt-3A-Tests 4, 5, 8, 13), BedrockConnector.js:1845"
    },
    {
      "id": "bedrock.holdItem",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "holdItem",
      "zeile": 1972,
      "parameter": [
        {
          "name": "itemName",
          "typ": "string (z. B. minecraft:music_disc_cat)",
          "pflicht": true,
          "beschreibung": "Item-Name, der in der Haupt-Hand gehalten wird"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 10), tests/scenarios/theater-erster-schnitt.yaml (hold_item), BedrockConnector.js:1972"
    },
    {
      "id": "bedrock.useBlock",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "useBlock",
      "zeile": 2043,
      "parameter": [
        {
          "name": "target",
          "typ": "{x:number, y:number, z:number}",
          "pflicht": true,
          "beschreibung": "Zielblock-Koordinaten"
        },
        {
          "name": "face",
          "typ": "number 0-5",
          "pflicht": false,
          "standard": 0,
          "beschreibung": "Block-Seite/Face (0=unten, 1=oben, 2=norden, 3=sueden, 4=westen, 5=osten)",
          "block_state": "facing"
        }
      ],
      "block_states": [
        "facing"
      ],
      "doku": "vault/20 Wissen/Minecraft/README.md",
      "verified": true,
      "beleg": "tests/cockpit-handlung-e2e.test.js (use_block face 0 in theater-erster-schnitt), BedrockConnector.js:2043"
    },
    {
      "id": "bedrock.setBlock",
      "modul": "app/backend/bedrock/BedrockConnector.js",
      "funktion": "setBlock",
      "zeile": 2082,
      "parameter": [
        {
          "name": "position",
          "typ": "{x:number, y:number, z:number}",
          "pflicht": true,
          "beschreibung": "Blockposition"
        },
        {
          "name": "blockName",
          "typ": "string (z. B. minecraft:quartz_block)",
          "pflicht": true,
          "beschreibung": "Zu setzender Block-Name"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Minecraft/README.md",
      "verified": true,
      "beleg": "tests/scenario-runner.test.js (Szenario-Test 8: theater-erster-schnitt set_block), BedrockConnector.js:2082"
    },
    {
      "id": "interaction.resolveGesture",
      "modul": "app/backend/interaction/InteractionEngine.js",
      "funktion": "resolveGesture",
      "zeile": 289,
      "parameter": [
        {
          "name": "emoteId",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Emote-ID, die in einen Gestenname aufgelöst wird"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Emotes.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 3, 4, 8), InteractionEngine.js:289"
    },
    {
      "id": "interaction.handleEmote",
      "modul": "app/backend/interaction/InteractionEngine.js",
      "funktion": "handleEmote",
      "zeile": 328,
      "parameter": [
        {
          "name": "now",
          "typ": "number",
          "pflicht": true,
          "beschreibung": "Zeitstempel für Combo-Fenster"
        },
        {
          "name": "player",
          "typ": "object",
          "pflicht": true,
          "beschreibung": "Spieler mit name und position"
        },
        {
          "name": "emoteId",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Erfasste Emote-ID"
        },
        {
          "name": "botPosition",
          "typ": "{x,y,z}",
          "pflicht": true,
          "beschreibung": "Aktuelle Bot-Position"
        },
        {
          "name": "lookTarget",
          "typ": "{x,y,z}",
          "pflicht": false,
          "beschreibung": "Blickziel für Gaze-Pruefung"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Emotes.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 3, 4, 8, 9), InteractionEngine.js:328"
    },
    {
      "id": "interaction.rotationTo",
      "modul": "app/backend/interaction/InteractionEngine.js",
      "funktion": "rotationTo",
      "zeile": 107,
      "parameter": [
        {
          "name": "from",
          "typ": "{x,y,z}",
          "pflicht": true,
          "beschreibung": "Startposition (Bot-Augen)"
        },
        {
          "name": "to",
          "typ": "{x,y,z}",
          "pflicht": true,
          "beschreibung": "Zielposition (Spieler-Augen)"
        },
        {
          "name": "pitchPositiveDown",
          "typ": "boolean",
          "pflicht": false,
          "standard": true,
          "beschreibung": "Minecraft-Pitch-Konvention (positiv = nach unten)"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Befehle-und-Gesten.md",
      "verified": true,
      "beleg": "tests/interaction.test.js (Interaktions-Test 2, 2b), InteractionEngine.js:107"
    },
    {
      "id": "world.pathfinder.findPath",
      "modul": "app/backend/world/Pathfinder.js",
      "funktion": "findPath",
      "zeile": null,
      "parameter": [
        {
          "name": "startPos",
          "typ": "{x,y,z}",
          "pflicht": true,
          "beschreibung": "Startposition"
        },
        {
          "name": "target",
          "typ": "{x,y,z}",
          "pflicht": true,
          "beschreibung": "Zielposition"
        },
        {
          "name": "stopDistance",
          "typ": "number",
          "pflicht": false,
          "standard": 3,
          "beschreibung": "Mindestabstand zum Ziel"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Minecraft/README.md",
      "verified": true,
      "beleg": "tests/step3a.test.js (Schritt-3A-Tests 2, 5, 8, 13), Pathfinder.js A*"
    },
    {
      "id": "world.worldGrid.isSolid",
      "modul": "app/backend/world/WorldGrid.js",
      "funktion": "isSolid",
      "zeile": null,
      "parameter": [
        {
          "name": "x",
          "typ": "number",
          "pflicht": true,
          "beschreibung": "Block-X"
        },
        {
          "name": "y",
          "typ": "number",
          "pflicht": true,
          "beschreibung": "Block-Y"
        },
        {
          "name": "z",
          "typ": "number",
          "pflicht": true,
          "beschreibung": "Block-Z"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Minecraft/README.md",
      "verified": true,
      "beleg": "tests/step3a.test.js (Schritt-3A-Test 2: Geofence), WorldGrid.js Block-Raster"
    },
    {
      "id": "memory.WorldMemory",
      "modul": "app/backend/memory/WorldMemory.js",
      "funktion": "WorldMemory",
      "zeile": null,
      "parameter": [
        {
          "name": "welt",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Welt-Name für SQLite-Datenbank"
        }
      ],
      "block_states": [],
      "doku": "vault/20 Wissen/Minecraft/README.md",
      "verified": true,
      "beleg": "tests/step3a.test.js (Schritt-3A-Test 8: Marker ueberleben Neustart), WorldMemory.js sqlite"
    },
    {
      "id": "werkbank.server",
      "modul": "ops/werkbank/server.mjs",
      "funktion": "createWerkbankServer",
      "zeile": null,
      "parameter": [
        {
          "name": "options",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "port, host, dataDir, fixtures, root"
        }
      ],
      "block_states": [],
      "doku": "ops/werkbank/README.md",
      "verified": true,
      "beleg": "tests/werkbank-browser-e2e.test.js (6 Tabs, SSE, 0 Konsolenfehler), ops/SYSTEM_SSOT.json werkbank"
    },
    {
      "id": "werkbank.freigabe",
      "modul": "ops/blueprints/pipeline.mjs",
      "funktion": "setApprovalMode/approveBlueprint",
      "zeile": null,
      "parameter": [
        {
          "name": "modus",
          "typ": "string (manual|auto_if_clear)",
          "pflicht": true,
          "beschreibung": "Freigabemodus der Bauplan-Pipeline"
        }
      ],
      "block_states": [],
      "doku": "vault/handbuch/04_Bedienung_und_Ablaeufe.md",
      "verified": true,
      "beleg": "ops/wissenanalyse/pipeline.test.js, ops/SYSTEM_SSOT.json werkbank.freigabeStandard"
    },
    {
      "id": "monitor.machine",
      "modul": "ops/runtime/machine-monitor.mjs",
      "funktion": "localMachineSnapshot/startHausmeister",
      "zeile": null,
      "parameter": [
        {
          "name": "options",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "pidsFile, workerStatusFile, intervalMs"
        }
      ],
      "block_states": [],
      "doku": "ops/runtime/machine-monitor.mjs",
      "verified": true,
      "beleg": "node ops/runtime/machine-monitor.mjs snapshot (echte PID-Registry, 15-s-Hausmeister)"
    },
    {
      "id": "monitor.feldbook",
      "modul": "ops/runtime/feldbook-telemetry.mjs",
      "funktion": "captureSnapshot/pushSnapshot/startAsyncRefresh",
      "zeile": null,
      "parameter": [
        {
          "name": "event",
          "typ": "string",
          "pflicht": false,
          "beschreibung": "task_start|round_change|task_end|heartbeat"
        }
      ],
      "block_states": [],
      "doku": "ops/runtime/feldbook-telemetry.mjs",
      "verified": true,
      "beleg": "node ops/runtime/feldbook-telemetry.mjs read (origin/monitor/alt-laptop, Legacy-Fallback)"
    },
    {
      "id": "vault.housekeeping",
      "modul": "ops/runtime/vault-housekeeping.mjs",
      "funktion": "runHousekeeping",
      "zeile": null,
      "parameter": [
        {
          "name": "options",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "vaultRoot, dryRun, intervalMs"
        }
      ],
      "block_states": [],
      "doku": "ops/runtime/vault-housekeeping.mjs",
      "verified": true,
      "beleg": "geteilter Git-Write-Lock, pull --rebase, push, credential.interactive=never"
    },
    {
      "id": "show.run",
      "modul": "app/backend/show/ShowDirector.js",
      "funktion": "run",
      "zeile": 195,
      "parameter": [],
      "block_states": [],
      "doku": "ops/SPEC_MAPPING.md (Abschnitt 2: Show-/Tanzshow-Fluss)",
      "verified": true,
      "beleg": "ops/runtime/latest-observer-proof.json (result.ok=true, 254 update_block + 1 mob_equipment), npm run verify:show (Observer-Gate gruen), npm run show:accept (Auftraggeber/Orchestrator)"
    },
    {
      "id": "bds.manager",
      "modul": "ops/runtime/bds-manager-registry.mjs",
      "funktion": "createBdsManagers/attachLive/start/stop",
      "zeile": null,
      "parameter": [
        {
          "name": "port",
          "typ": "number (19133|19134)",
          "pflicht": false,
          "beschreibung": "Zielserver; 19132 ist attach-only und fuer Start/Stop gesperrt"
        }
      ],
      "block_states": [],
      "doku": "ops/SYSTEM_SSOT.json (bdsManager, Serverrollen)",
      "verified": true,
      "beleg": "node ops/runtime/bds-manager-registry.mjs: Live-PID 18356 attachiert (protected, kein Neustart), 19133/19134 in Manager-Hoheit"
    },
    {
      "id": "world.chooseAndReset",
      "modul": "ops/wissenanalyse/pipeline.mjs",
      "funktion": "chooseWorld/exploreWorld/masterReset",
      "zeile": null,
      "parameter": [
        {
          "name": "plan",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "Bauplan mit weltArt oder Gelände-Hinweisen"
        },
        {
          "name": "serverDir",
          "typ": "string",
          "pflicht": false,
          "beschreibung": "BDS-Verzeichnis; der Pfad server/ und Port 19132 sind gesperrt"
        }
      ],
      "block_states": [],
      "doku": "ops/SPEC_MAPPING.md (Abschnitt 8: T-226 Bausteine)",
      "verified": true,
      "beleg": "tests/wissenanalyse-pipeline.test.js (11/11 gruen): Weltwahl, Erkundung, LOCK-Entfernung, Origin, Reset-Marker, 8000-ms-Schutzfenster"
    },
    {
      "id": "spectator.liveMode",
      "modul": "ops/wissenanalyse/pipeline.mjs",
      "funktion": "spectatorTiming",
      "zeile": null,
      "parameter": [],
      "block_states": [],
      "doku": "ops/SYSTEM_SSOT.json (spectator)",
      "verified": true,
      "beleg": "tests/wissenanalyse-pipeline.test.js: 12 s Vorlauf, 3 s Schlussbild, Pausenfaktor 1.5–2, nur bei echten visuellen Laeufen"
    },
    {
      "id": "qa.definitionOfReady",
      "modul": "ops/wissenanalyse/pipeline.mjs",
      "funktion": "definitionOfReady/atomicDiffBudget/contractFirst/preflight",
      "zeile": null,
      "parameter": [
        {
          "name": "step",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "Bauplan-Schritt mit zieldateien, weltArt, capabilities, pruefKriterium"
        }
      ],
      "block_states": [],
      "doku": "ops/blueprints/BP-000.json (fuenf Haertungen)",
      "verified": true,
      "beleg": "tests/wissenanalyse-pipeline.test.js: Ready-Lint, 2–4 Dateien/<20 Min, Contract-First, 3-s-Pre-Flight"
    },
    {
      "id": "sync.werkbankAuto",
      "modul": "ops/runtime/werkbank-sync.mjs",
      "funktion": "syncWerkbankAktion",
      "zeile": null,
      "parameter": [
        {
          "name": "aktion",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Auslösende Werkbank-Aktion (idee|freigabe|frage|handlung|showcase|nachbesserung)"
        },
        {
          "name": "options",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "root, leitstelleRepo, vaultRepo, timeoutMs; Default 30 000 ms Prozessbaum-Timeout"
        }
      ],
      "block_states": [],
      "doku": "ops/werkbank/README.md, vault/handbuch/04_Bedienung_und_Ablaeufe.md",
      "verified": true,
      "beleg": "tests/werkbank-auto-sync.test.mjs (25 Prüfungen grün): gemeinsamer Git-Write-Lock für Leitstelle + Vault, credential.interactive=never, pushTimeouts [30000], Fehler-UI wiederholbar"
    },
    {
      "id": "export.notebookSync",
      "modul": "ops/scripts/export-notebook-sync.mjs",
      "funktion": "exportNotebook",
      "zeile": null,
      "parameter": [
        {
          "name": "out",
          "typ": "string",
          "pflicht": false,
          "beschreibung": "Zielordner; Default <root>/.local-notebook-sync/ (gitignoriert)"
        }
      ],
      "block_states": [],
      "doku": "ops/werkbank/README.md",
      "verified": true,
      "beleg": "npm run notebook:export: read-only Export von SSOT, AGENTS.md, allen Handbuchkapiteln, capabilities.json und allen BP-*; einheitliches Datums-Präfix; autom. bei Werkbank-Start und Bauplan-Abschluss"
    },
    {
      "id": "hilfe.botHilfe",
      "modul": "ops/runtime/bot-hilfe.mjs",
      "funktion": "erstelleHilfe",
      "zeile": null,
      "parameter": [
        {
          "name": "grund",
          "typ": "string",
          "pflicht": true,
          "beschreibung": "Klartextproblem aus der 10-Jahre-Sicht, ohne Dateipfade/Tech-Kürzel"
        },
        {
          "name": "options",
          "typ": "object",
          "pflicht": false,
          "beschreibung": "root, clipSekunden (Default 60), dataDir (Fixture-Isolation)"
        }
      ],
      "block_states": [],
      "doku": "vault/handbuch/07_Vier_Stufen_UI_Prozess.md",
      "verified": true,
      "beleg": "tests/werkbank-browser-e2e.test.js + tests/cockpit-browser-e2e.test.js: 60-s-Flugschreiber-Clip, Eskalationskarte mit 3 Optionen, Screenshot-Beleg, 0 Konsolenfehler"
    },
    {
      "id": "ram.maschinenSchwellen",
      "modul": "ops/runtime/runtime-guard.mjs",
      "funktion": "ramSchwellenFuer",
      "zeile": null,
      "parameter": [
        {
          "name": "machine",
          "typ": "string",
          "pflicht": false,
          "beschreibung": "maschinen.<name> aus der SSOT; Default hauptrechner, alternativ MCBOT_MACHINE"
        }
      ],
      "block_states": [],
      "doku": "ops/SYSTEM_SSOT.json → maschinen.<name>.ram",
      "verified": true,
      "beleg": "tests/runtime-guard-ram.test.mjs (13 Prüfungen grün): Hauptrechner 80/90, FELDBOOK 85/90 aus SSOT; generierte worker/runner-Configs ohne Drift; Live-19132 nie beendet"
    }
  ],
  "inventur": {
    "stand": "2026-11-23",
    "methode": "Read-only Inventur aller Inbox-Originale und weiterverarbeiteter Artefakte (vault/konzept/**, vault/30 Szenarien/**, vault/20 Wissen/**), Referenzanlagen 01–10, Handbuch 05 und Codebase. Trennung strikt nach Beweisstand: 'nachgewiesen grün' nur mit registrierter Capability + reproduzierbarem Testbeleg, alles Übrige 'fehlt' mit Bauplan-Zuordnung. Keine geratenen IDs/Härtewerte/Rezepte.",
    "nachgewiesen_gruen": [
      {
        "bereich": "Wahrnehmung & 64×36-Raycaster",
        "capability": "qa.voxelRaycast",
        "beleg": "ops/qa/world-invariants.mjs, Augenhöhe y+1,62, ±45°/±35°, 3–32 Blöcke; Referenz vault/konzept/06_referenz-fast-voxel-raycaster-tof.md",
        "hinweis": "Nur der Gate-Strahl ist grün, nicht der 64×36-ToF-Depth-Sensor selbst."
      },
      {
        "bereich": "Sense→Think→Act→Verify",
        "capability": "qa.definitionOfReady",
        "beleg": "ops/wissenanalyse/pipeline.mjs (5-Phasen-Vertrag anlage-03, 50-ms-EXECUTE, echte Server-Quittung); tests/wissenanalyse-pipeline.test.js 11/11",
        "hinweis": "Phasen-Vertrag als Regel grün; laufender HFSM-Sense→Think→Act→Verify-Loop fehlt."
      },
      {
        "bereich": "7 Handlungsgruppen",
        "capability": "bedrock.* (sendChat/sendEmote/commandInteraction/walkTo/holdItem/useBlock/setBlock)",
        "beleg": "ops/capabilities.json Einträge bedrock.* + interaction.*; tests/interaction.test.js, tests/step3a.test.js",
        "hinweis": "Die Low-Level-Primitives sind grün; die generische Gruppierungs-/Orchestrierungsschicht fehlt."
      },
      {
        "bereich": "Gestenerkennung",
        "capability": "interaction.resolveGesture / interaction.handleEmote / bedrock.sendEmote",
        "beleg": "tests/interaction.test.js (Combo-Fenster, Gaze, Emote-Mapping data/interaction.yaml)",
        "hinweis": "Drei Emote-UUIDs (Winken/Klatschen/Folge-mir) sind live zu erfassen — nicht geraten."
      },
      {
        "bereich": "Shared Weltgedächtnis/Blockmuster/2.5D",
        "capability": "memory.WorldMemory",
        "beleg": "tests/step3a.test.js (Marker überleben Neustart), WorldMemory.js sqlite",
        "hinweis": "Lokales Gedächtnis grün; geteiltes Gedächtnis, Blockmuster-Bibliothek und 2.5D-Karte fehlen."
      },
      {
        "bereich": "Leitstellen-Screens/Wizards",
        "capability": "werkbank.server / werkbank.freigabe",
        "beleg": "tests/werkbank-browser-e2e.test.js, tests/cockpit-browser-e2e.test.js (Second Screen, 6 Bereiche, 0 Konsolenfehler)",
        "hinweis": "Werkbank + 2ndScreen grün; vollständige Wizard-Suite aus UI/UX v2 noch nicht."
      },
      {
        "bereich": "In-Game-UI + Fallback",
        "capability": "hilfe.botHilfe",
        "beleg": "vault/handbuch/07_Vier_Stufen_UI_Prozess.md; AGENTS.md §13; Bot-Hilfe in Werkbank + Leitstelle, 4-Stufen-Prozess verankert",
        "hinweis": "Prozess und Notfallknopf grün; @minecraft/server-ui + Pure-Client-Fallback im Spiel fehlt (server/** tabu bis BP-011)."
      },
      {
        "bereich": "BDS-Werkstatt",
        "capability": "bds.manager / world.chooseAndReset / spectator.liveMode",
        "beleg": "ops/runtime/bds-manager-registry.mjs (19132 attach-only, protected), tests/wissenanalyse-pipeline.test.js 11/11",
        "hinweis": "Serverhoheit, Weltwahl und Reset grün; Feinschliff bleibt BP-000 (manual)."
      },
      {
        "bereich": "Adaptive Scene Director + Multi-Seed-Gate",
        "capability": "regieLeitplanken (SSOT) + qa.voxelRaycast + world.chooseAndReset",
        "beleg": "ops/SYSTEM_SSOT.json → regieLeitplanken A–D, AGENTS.md §12, Handbuch 05, BP-007 (manual)",
        "hinweis": "Verbindlichkeits- und Beweisregeln grün; ausführende Regie-Bausteine in BP-007 (manual) offen."
      },
      {
        "bereich": "User-Empathy-Simulator",
        "capability": "hilfe.botHilfe (10-Jahre-Check)",
        "beleg": "AGENTS.md §13 Stufe 4, Browser-E2E mit 10-Jahre-Verständlichkeitscheck ohne Dateipfade/Tech-Kürzel",
        "hinweis": "UI-Verständlichkeitscheck grün; echter unprivilegierter FOV-70°-Spieler-Simulator fehlt (BP-007/D)."
      },
      {
        "bereich": "Showcases",
        "capability": "show.run",
        "beleg": "ops/runtime/latest-observer-proof.json (result.ok=true, 254 update_block + 1 mob_equipment), npm run verify:show, npm run show:accept",
        "hinweis": "Vollständige Show-Journey mit Observer-Gate und programmatischem Auftraggeber grün."
      },
      {
        "bereich": "Zero-Terminal-Betrieb",
        "capability": "sync.werkbankAuto / export.notebookSync / ram.maschinenSchwellen",
        "beleg": "tests/werkbank-auto-sync.test.mjs (25), tests/runtime-guard-ram.test.mjs (13), npm run notebook:export",
        "hinweis": "Automatischer Commit/Push, git-freier Export und maschinenspezifische RAM-Schranken grün (T-228)."
      }
    ],
    "fehlt": [
      {
        "bereich": "Wahrnehmung & 64×36-Raycaster",
        "soll": "64×36-ToF-Tiefenkarte alle 500 ms (Amanatides & Woo), Zero-Allocation, als Bot-Wahrnehmungssensor",
        "bauplan": "BP-008",
        "grund": "Nur der QA-Gate-Raycast ist umgesetzt; der kontinuierliche Tiefen-Sensor fehlt. Keine geratenen Reichweiten/Parameter."
      },
      {
        "bereich": "Sense→Think→Act→Verify",
        "soll": "Laufender 50-ms-HFSM mit Sense→Think→Act→Verify und echter Quittierung pro Schritt",
        "bauplan": "BP-009",
        "grund": "Phasen-Vertrag ist Regel, aber kein ausführbarer Executive-Loop."
      },
      {
        "bereich": "7 Handlungsgruppen",
        "soll": "Generische Gruppierung und Orchestrierung der sieben Handlungsgruppen über den Primitives",
        "bauplan": "BP-009",
        "grund": "Primitives vorhanden, Gruppierungs-/Planungsschicht fehlt."
      },
      {
        "bereich": "MSA & Co-Pilot-Relay",
        "soll": "Lokales WebSocket/REST-Relay, Co-Pilot-Stufen, sichere Trennung der Betriebsmodi",
        "bauplan": "BP-012",
        "grund": "Schnittstellenspezifikation v2 inventarisiert, kein Baustein; Ports/Öffentlichkeit nur nach Chris-Tor."
      },
      {
        "bereich": "Shared Weltgedächtnis/Blockmuster/2.5D",
        "soll": "Geteiltes Weltgedächtnis über Bots, kuratierte Blockmuster-Bibliothek, 2.5D-Weltkarte",
        "bauplan": "BP-010",
        "grund": "Lokales WorldMemory grün, gemeinsame Schicht und Blockmuster/2.5D fehlen."
      },
      {
        "bereich": "Ikonischer Resource-Pack-Generator",
        "soll": "Ikonisches Resource-Pack aus kuratierten Blockmustern erzeugen",
        "bauplan": "BP-013",
        "grund": "Keine registrierte Capability; IDs/Rezepte nicht raten — kuratierte Quelle nötig."
      },
      {
        "bereich": "In-Game-UI + Fallback",
        "soll": "@minecraft/server-ui + Pure-Client-Fallback, Kompass-Notfallknopf im Spiel",
        "bauplan": "BP-011",
        "grund": "server/** bleibt bis zu dessen Freigabe unangetastet; Prozess ist verankert, Umsetzung fehlt."
      },
      {
        "bereich": "Adaptive Scene Director + Multi-Seed-Gate",
        "soll": "Ausführender 32×32-Sektor-Regisseur, N≥3-Seed-Gate mit dreifacher Realitätskopplung",
        "bauplan": "BP-007",
        "grund": "Regeln grün, ausführende Bausteine bewusst manual/backlog — kein Vorgriff auf Freigabe."
      },
      {
        "bereich": "User-Empathy-Simulator",
        "soll": "Unprivilegierter FOV-70°-Spieler-Simulator mit ruhigem Tempo auf 19134",
        "bauplan": "BP-007",
        "grund": "Nur UI-Verständlichkeitscheck grün; Simulator fehlt."
      },
      {
        "bereich": "Showcase-Regie & Review-Rückfluss",
        "soll": "Showcase-Skript trocken auf 19134, Abnahme auf 19133, Feedback in review_submissions zurück in Baupläne",
        "bauplan": "BP-015",
        "grund": "Show.run grün, aber Regie-Skript/Review-Rückfluss als eigener Baustein fehlt."
      },
      {
        "bereich": "Tribes/Frontier",
        "soll": "Tribes/SQLite-Weltgedächtnis, Frontier-Grenzen, eine Instanz pro Welt",
        "bauplan": "BP-014",
        "grund": "Schnittstelle v2 inventarisiert, keine Umsetzung; Zahlen/IDs offen (nicht raten)."
      }
    ]
  }
}
```


================================================================================
## DATEI: 2026-11-23__Handbuch-01_Systemueberblick_und_Architektur.md
================================================================================
# Handbuch: 01_Systemueberblick_und_Architektur.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/01_Systemueberblick_und_Architektur.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json
auto_aktualisierung: bauplan_abschluss
---

# 01 — Systemüberblick und Architektur

## 1. Einzige Quelle der Wahrheit

`ops/SYSTEM_SSOT.json` (Stand 2026-11-23) ist die **einzige** maßgebliche Quelle für Worker, Maschinen, Server, Akteure, Vision, Prozessschutz und Freigaberegeln. Aus ihr abgeleitet, aber nicht eigenständig pflegbar:

- `ops/runner.json`, `ops/runner.alt-laptop.json`, `ops/workers.json` — erzeugt mit `node ops/scripts/system-config-sync.mjs`, **nie manuell editieren**. Der Dateiname `runner.alt-laptop.json` bleibt aus Kompatibilitätsgründen erhalten; die Maschine heißt fachlich **FELDBOOK**.
- `AGENTS.md` (Spielregeln), `ops/WORLD_INVARIANTS.md` (QA-Standard), `ops/capabilities.json` (Fähigkeitsregister), `ops/BOARD.md` (aktueller Task-Stand).

Bei Widersprüchen gilt immer die SSOT. Dieses Handbuch ist eine kompakte Lesefassung, keine zweite SSOT. Weitere Quellen: `AGENTS.md`, `ops/WORLD_INVARIANTS.md`, `ops/capabilities.json`, `ops/BOARD.md`.

## 2. Aufbau

| Ebene | Aufgabe |
| --- | --- |
| Werkbank | Browser-UI auf `127.0.0.1:7070`, SSE-Livefeed, **kein Login**; Standard-Freigabe `manual`. |
| Leitstelle (Node.js 26.x) | Queue, Worker-Dispatch, Cockpit, Vault-Intake, Hausmeister, QA und Berichte. |
| Worker | Fünf Kilos auf zwei Maschinen; Gateway/OpenRouter als Werkzeug, kein Chris-Tor. |
| Minecraft | Drei getrennte Bedrock-Instanzen: Live, Showcase, E2E (siehe Handbuch 02). |
| Vault | Git-versionierter Wissens- und Ablaufspeicher; Tore ausschließlich bei Chris. |

Node.js 26.x ist Pflicht (`node --version`); bei altem PATH `nvm use 26.10.0` oder absoluten Node-26-Pfad nutzen. Keine neuen Abhängigkeiten ohne Freigabe im Task.

## 3. Worker und Maschinen

| Worker | Host | Typ | Modell | Besonderheit |
| --- | --- | --- | --- | --- |
| `kilo1` | Hauptrechner | `gateway` | `kilo-auto/free` | Standardpfad, 0 € |
| `kilo2` | Hauptrechner | `openrouter` | `openrouter/free` + Fallback-Kette | **T-199 (OpenRouter-Modellausfall: Preset prüfen und freigeben)**; `kiloAutoFinalTry: false` |
| `kilo3` | FELDBOOK | `gateway` | `kilo-auto/free` | 0 € |
| `kilo4` | FELDBOOK | `gateway` | `kilo-auto/free` | 0 € |
| `kilo5` | Hauptrechner | `gateway` | `kilo-auto/free` | 0 € |

**Es gibt genau fünf Worker kilo1–kilo5; Kilo6 entfällt.** `maxParallelByHost = 2` gilt auf beiden Maschinen (Hauptrechner: kilo1/kilo2/kilo5; FELDBOOK: kilo3/kilo4). Reihenfolge: OpenRouter zuerst, dann Gateway, dann Mastermind; innerhalb eines Typs deterministische Rotation nach Task-Nummer. Ein Kilo-Lauf hat maximal **3 Runden inklusive Erstlauf**; technische Startwiederholungen (429/Credit/Startfehler) zählen nicht als Runden, `maxAttempts: 16` begrenzt sie. Sind alle Worker für einen Task erfolglos, pausiert nur dieser Task 30 Minuten; die Queue läuft weiter.

Der Dispatch ist derzeit pausiert (`ops/runner.stop`): Freigabe erst nach grünem Test und Live-Laden der Werkbank. Ein leeres Board, eine Stundenlimite oder eine task-lokale Blockade beenden den Dauerbetrieb nicht.

## 4. Kosten, Vision und Kontingent

- Standardpfad `kilo-auto/free` (0 €); erlaubt sind außerdem `openrouter/free`-Fallbacks (0 €). **Kostenpflichtige API-Calls ohne Chris-Freigabe sind verboten.**
- Ca. 95 % der QA-Prüfungen laufen als kostenlose Skripte / Kilo Auto Free (`kilo1`, `kilo3`, `kilo4`) mit 0 OpenRouter-Calls.
- Vision ist ausschließlich ein **optionales letztes Siegel** nach vollständig grüner Stufe 1 (Observer-Beweis + 3D-ViewPort). Hartes Budget: **max. 1 Call pro fertigem Meilenstein und max. 3 Calls pro Tag**, **getrennt von kilo2** und dessen OpenRouter-Fallbackkette.
- Bei Erreichen des Budgets wird **nicht blockiert**, sondern gemeldet: „Stufe 1 grün, Vision-Call wegen Tagesbudget übersprungen (Screenshots abgelegt)". Status mit `node ops/kilo-handler/vision-budget.mjs status`.

## 5. Prozess- und Ressourcenschutz

| Regel | Wert / Verhalten |
| --- | --- |
| Hartes Prozess-Timeout | 30 000 ms; bei Ablauf `taskkill /PID <pid> /T /F` für den kompletten Baum |
| Git-Aufrufe | immer `--no-optional-locks`, `credential.interactive=never`, `GIT_TERMINAL_PROMPT=0`, 30 s Timeout |
| RAM-Warnstufe | > 80 % belegt oder < 3 GB frei → keine neuen Worker-Tasks / E2E-Läufe |
| Notbremse | > 90 % belegt oder < 1,5 GB frei → geordneter Stopp; Live-Server `19132` wird nie beendet |
| Verify / E2E | strikt serialisiert über `ops/runtime/locks/`; vor Start `node ops/runtime/runtime-guard.mjs can-start` |
| 429 / Credit | **kein Task-Level-Cooldown**; Queue läuft weiter |
| Fehlstarts | Quarantäne nach **2 Fehlstarts** (task-lokal) |
| PID-Registry | jeder gestartete Prozess in `ops/runtime/pids.json`, bei Ende wieder entfernt |

## 6. Wissenspflege

Dieses Handbuch hat `auto_aktualisierung: bauplan_abschluss`: Bei abgeschlossenen oder freigegebenen Bauplänen aktualisiert `ops/wissenanalyse/handbuch-sync.mjs` den Bauplan-Katalog in Handbuch 03 und die Ereigniszeile in Handbuch 04 (idempotent pro Bauplan-ID, Markdown UTF-8 ohne BOM). Die Architektur-Abschnitte bleiben SSOT-basiert und werden nur bei einer SSOT-Änderung überarbeitet.



================================================================================
## DATEI: 2026-11-23__Handbuch-02_Server_Welten_und_Akteure.md
================================================================================
# Handbuch: 02_Server_Welten_und_Akteure.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/02_Server_Welten_und_Akteure.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json
auto_aktualisierung: bauplan_abschluss
---

# 02 — Server, Welten und Akteure

Verbindlich nach `ops/SYSTEM_SSOT.json`, `ops/WORLD_INVARIANTS.md` §0 und `AGENTS.md` §2. BDS 1.26.63.1 unterstützt **ausschließlich** NetherNet (`transport=nethernet`); ein `raknet`-Eintrag erzeugt den `TRANSPORT TYPE ERROR` (Meldung: „NetherNet is the only supported transport type"). Alle drei Server fahren NetherNet; nur die Live-Welt hat LAN-Visibility `true` und den exklusiven UDP-Port 7551.

## 1. Drei Welten, drei Ports

| Rolle | Adresse | IPv6-Port | Transport | LAN-Visibility | UDP | Besonderheit |
| --- | --- | --- | --- | --- | --- | --- |
| Live-Welt | `127.0.0.1:19132` | `19142` | `nethernet` | `true` | **7551 (exklusiv)** | höchster Schutz; Tests, Showcases und Notbremse **strikt tabu** |
| Showcase-Bühne | `127.0.0.2:19133` | `19143` | `nethernet` | `false` | `19133` | Level `ShowWorld`; Start **nur** per Ingame-Chat-Befehl `!show`, nie blind vorab |
| E2E-Testserver | `127.0.0.3:19134` | `19144` | `nethernet` | `false` | `19134` | direkter Endpunkt für E2E-Läufe |

Es gibt keine Port-, Firewall-, UDP- oder LAN-Visibility-Änderung am Live-Server `19132`. Auch `19133`/`19134` nutzen NetherNet, aber mit `enable-lan-visibility=false` und eigenen UDP-Ports (`server-udp-ports=19133` bzw. `19134`); Bots, `observer` und Chris' echter Client verbinden direkt und port-exakt. NetherNet-Discovery-Broadcasts über UDP 7551 bleiben exklusiv der Live-Welt vorbehalten; die drei Server teilen sich keinen UDP-Port. Der Start-Check `node ops/scripts/check-e2e-server.mjs` prüft im frischen BDS-Log ausdrücklich, dass `"transport":"nethernet"` und `Server started.` vorliegen und kein `TRANSPORT TYPE ERROR` auftritt.

## 2. Akteurs-Isolation

- Live-Profile: `WaechterBot`, `BauBot`, `SWARM_BOT` — **niemals** auf `19133`/`19134`.
- Bühnen-/Test-Profile: `SHOW_ACTOR` und `OBSERVER` — **ausschließlich** auf `19133`/`19134`, **niemals** auf `19132`.
- Erzwungen vor jedem Verbindungsaufbau durch `ops/lib/actor-profile-guard.cjs`; Verstöße sind Abbruch, kein „Grün".

Der Observer-Beweis prüft die **tatsächlich verbundene** Server-Identität und den echten Port (`level_name = ShowWorld`, Port `19133`), nicht nur das angeforderte Ziel. Abweichungen (z. B. `TestWorld` oder `19132`) sind sofortiger Abbruch mit `result=RED` — ein False-Green gegen die Live-Welt zählt nie.

## 3. E2E-Gate: 4 Observer-Punkte + 3D-ViewPort

Jeder E2E-Lauf (`19134`) und jeder Show-Abnahmelauf (`19133`) verbindet einen separaten Client `observer` mit normalem Spielerstatus im Zuschauerbereich (kein Spectator-Modus, keine Admin-Rechte). Ein Meilenstein ist erst grün, wenn **beides** vorliegt: die vier Observer-Punkte und der 3D-ViewPort/Raycast.

| Nr. | Observer-Punkt | Beweis |
| --- | --- | --- |
| 1 | Blöcke (Bühne, Jukebox, Kulisse) | `update_block`-Paket (oder äquivalente Chunk-Daten) an den Zielkoordinaten — Befehlssenden allein genügt nicht |
| 2 | Gehaltene Items | `mob_equipment`-Paket des Akteur-Bots mit korrekter Actor-Kennung, Item-ID und Hand-Slot |
| 3 | Sichtbarkeit, Oberflächenlage, Identität | `visible=true`, `on_surface=true`, tatsächliche Server-Identität und Port |
| 4 | 3D-ViewPort / Voxel-Raycast | Augenhöhe `position + (0, 1.62, 0)`, ±45° horizontal, ±35° vertikal, 3–32 Blöcke, kein undurchsichtiger Block auf der Sichtlinie |

Der Raycast läuft in 0,25-Block-Schritten (`ops/qa/world-invariants.mjs` → `voxelRaycast`); der Zielblock selbst zählt nicht als Hindernis, Glas und Glasfensterarten schon. Bots müssen auf Fußhöhe `y`, Rumpfhöhe `y + 0.9` und Kopfhöhe `y + 1.6` im Sichtfeld liegen. „Röntgenblick" ist ausgeschlossen: Ein Bot hinter einer Wand ist auch bei passenden Koordinaten nicht sichtbar. Der Nachweis liegt in `ops/runtime/latest-observer-proof.json` (Erzeugung: `node ops/qa/observer-proof.mjs`).

## 4. Produktions-Invarianten

Default bei Umsetzung **und** Prüfung; Ausnahmen nur, wenn der Regieplan sie als `intent.exceptions` mit kurzem `reason` benennt:

| Invariante | Default | Ausnahme-Typ |
| --- | --- | --- |
| Begehbare Wege | Empfang → Zuschauerbereich → Bühne ohne Blockaden auf Fuß-/Körperhöhe | `blocked_path` |
| Mindest-Raumhöhe | ≥ 2,5 Blöcke über jedem begehbaren Weg | `low_ceiling` |
| Fortbewegung | Gehen statt Teleportieren | `teleport` |
| Szenenstabilität | kein Reconnect mitten in einer Szene | `reconnect_in_scene` |
| Tanzfläche | Tänzer bleiben in der Bühnen-Bounding-Box | `exit_stage` (nur Abgang) |
| Präsentation / Verbeugung | Blickrichtung zum Publikum | `face_away` |
| Sichtbarkeit | `visible=true`, Oberflächenlage | `invisible` |

Selbstnoten durch Befehlssenden zählen nicht. Wer nicht aus `observer`-Sicht und mit Paketbeweis prüft, darf keinen Meilenstein als grün melden. Ein rotes Gate führt zur autonomen Nachbesserung in maximal 5 Versuchen je Gate-Loop, nicht zu `blocked` oder Rückfrage.



================================================================================
## DATEI: 2026-11-23__Handbuch-03_Faehigkeiten_und_Bauplan_Katalog.md
================================================================================
# Handbuch: 03_Faehigkeiten_und_Bauplan_Katalog.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/03_Faehigkeiten_und_Bauplan_Katalog.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json
auto_aktualisierung: bauplan_abschluss
---

# 03 — Fähigkeiten und Bauplan-Katalog

## 1. Fähigkeitsregister

`ops/capabilities.json` ist das Registry aller umgesetzten Fähigkeiten: `id`, `modul`, `funktion`, `zeile`, `parameter`, `block_states`, `doku`, `verified` und `beleg`. `ops/SPEC_MAPPING.md` bildet die Specifications (Vault inklusive Inbox-Originale, `ops/WORLD_INVARIANTS.md`, Codebase, `ops/BOARD.md`) lückenlos auf Soll / Ist / Backlog ab. Vor größeren Umsetzungen wird dieses Mapping zuerst vervollständigt, damit kein Agent rät oder mehrfach nach Bausteinen sucht.

Auswahl registrierter Fähigkeiten:

| ID | Modul / Funktion | Zweck |
| --- | --- | --- |
| `bedrock.sendChat` | `BedrockConnector.js` / `sendChat` | Chat-Nachricht senden |
| `bedrock.sendEmote` | `BedrockConnector.js` / `sendEmote` | Gesten über `data/interaction.yaml` |
| `bedrock.commandInteraction` | `BedrockConnector.js` / `commandInteraction` | follow / stop / escort / guard / attention |
| `bedrock.walkTo` | `BedrockConnector.js` / `walkTo` | Pfadgeführung mit Mindestabstand |
| `bedrock.holdItem` | `BedrockConnector.js` / `holdItem` | Item in der Haupthand halten |
| `bedrock.useBlock` / `bedrock.setBlock` | `BedrockConnector.js` | Block Interaktion / Setzen inkl. `facing` |
| `interaction.resolveGesture` / `handleEmote` | `InteractionEngine.js` | Emote-Auflösung und Combo-Fenster |
| `world.pathfinder.findPath` | `Pathfinder.js` | A*-Wegfindung |
| `world.worldGrid.isSolid` | `WorldGrid.js` | Block-Raster / Geofence |
| `memory.WorldMemory` | `WorldMemory.js` | SQLite-Weltgedächtnis |
| `show.run` | `ShowDirector.js` / `run` | Show-Ablauf mit Observer-Nachweis |
| `werkbank.freigabe` | `ops/blueprints/pipeline.mjs` / `setApprovalMode`, `approveBlueprint` | Freigabe-Modus der Bauplan-Pipeline |
| `monitor.machine` / `monitor.feldbook` | `machine-monitor.mjs`, `feldbook-telemetry.mjs` | Laufzeit- und FELDBOOK-Telemetrie |

Keine IDs, Härtewerte oder Rezepte raten. Fehlendes wird als offener Punkt markiert; jede Kuratierung braucht `source` und `begruendung`. Nie schreiben: `Daten/extrahiert/**`, `Index/**`, `Schema.md`. Vor Item-, Block- oder Rezeptarbeit `wissen/minecraft/README.md` und die genannten Datensätze lesen.

## 2. Freigabe-Modus der Bauplan-Pipeline

Der Modus von `ops/blueprints/pipeline.mjs` (`werkbank.freigabe`) ist nach `ops/SYSTEM_SSOT.json` standardmäßig **`manual`**; alternativ **`auto_if_clear`**.

- `manual`: Ein Bauplan wird erst nach ausdrücklicher Freigabe in der Werkbank sichtbar und in die Board-/Result-Welt überführt.
- `auto_if_clear`: Automatische Überführung **nur**, wenn alle Gates grün sind (Observer-Beweis, 3D-ViewPort, keine offenen Chris-Tore). Ein rotes Gate oder ein fehlender Pusht sperrt.

Beide Modi setzen **niemals** die Vault-Tore `status: bereit|beantwortet|erledigt` — die gehören ausschließlich Chris. `status: begonnen` (Handlungen) ist Chris' Startsignal und kein Tor.(`ops/BOARD.md`, Stand 2026-11-23: neue Arbeiten entstehen ausschließlich über Baupläne und werden erst nach Freigabe sichtbar.)

## 3. Bauplan-Katalog

Aktualisierung bei abgeschlossenen/freigegebenen Bauplänen durch `ops/wissenanalyse/handbuch-sync.mjs` (idempotent pro Bauplan-ID). Zwischen den Markern liegt die Maschinenlesezone — bitte von Hand nicht verändern.

<!-- bauplan-katalog:begin -->
| ID | Titel | Status | Freigabemodus | Quelle |
| --- | --- | --- | --- | --- |
| — | Noch keine freigegebenen Baupläne | — | — | — |
<!-- bauplan-katalog:end -->

## 4. Sync-Regeln

`syncHandbookFromBlueprint(blueprint, options)` schreibt nur bei `abgeschlossen`/`freigegeben` und nur UTF-8 ohne BOM. Ein vorhandener Eintrag derselben ID wird aktualisiert (Katalog) bzw. unverändert gelassen (Ereigniszeile in Handbuch 04); es entstehen keine Doppelzeilen. `HANDBUCH_DATEIEN` nennt die fünf Dateien dieses Handbuchs (inkl. `05_Autonome_Weltanpassung_und_Regie.md`).



================================================================================
## DATEI: 2026-11-23__Handbuch-04_Bedienung_und_Ablaeufe.md
================================================================================
# Handbuch: 04_Bedienung_und_Ablaeufe.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/04_Bedienung_und_Ablaeufe.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json
auto_aktualisierung: bauplan_abschluss
---

# 04 — Bedienung und Abläufe

## 1. Start und Werkbank

- Node.js **26.x** Pflicht: `node --version`; bei falschem PATH `nvm use 26.10.0` oder absoluter Node-26-Pfad.
- Werkbank: `http://127.0.0.1:7070` — **ohne Login**, SSE-Livefeed, nur lokal/LAN-Tailscale, keine öffentliche Erreichbarkeit. Standard-Freigabe `manual`.
- Jede Änderung an `ops/cockpit/public/**` oder den Cockpit-APIs braucht zusätzlich `npm run test:browser` gegen einen temporären Fixture-Server — nie gegen den Produktiv-Port 7070 oder Produktionspfade. Mindestabnahme: 0 Browser-Konsolenfehler, 0 fehlgeschlagene API-Requests.
- Tests: `npm test`; Einzeldatei `node tests/<datei>.test.js`; Verifikation `npm run verify`, Show-Gate `npm run verify:show`.

## 2. Chris-Tore (niemals automatisch setzen)

| Vault-Bereich | Chris-Tor | Bedeutung |
| --- | --- | --- |
| `konzept/ideen/` | `status: bereit` | Idee freigegeben |
| `fragen/` | `status: beantwortet` | Rückfrage beantwortet |
| `handlungen/` | `status: erledigt` | Handlung abgeschlossen |

`status: begonnen` bei Handlungen ist Chris' **Startsignal**, kein Tor. Kein Worker, kein Agent und kein Skript setzt diese Signale. Der Intake verarbeitet eine Datei erst, wenn das Tor gesetzt **und** der Vault committet und gepusht ist (`## main...origin/main` ohne Änderungen). `ready` ist ausschließlich der Task-Status in `ops/tasks/` und nie ein Vault-Status. Dateien in `Beispielen/` bleiben grundsätzlich unangetastet.

## 3. Vault-Sync und Housekeeping

Alle Git-Wege laufen über `ops/runtime/vault-git-sync.mjs`: gemeinsamer Lock, `--no-optional-locks`, `credential.interactive=never`, `GIT_TERMINAL_PROMPT=0`, 30 s Timeout, kein Force-Push, kein History-Rewrite.

```bash
node ops/runtime/vault-housekeeping.mjs dry   [--root <vault>]  # nur Plan, keine Verschiebung
node ops/runtime/vault-housekeeping.mjs once  [--root <vault>]  # ein Lauf
node ops/runtime/vault-housekeeping.mjs watch [--root <vault>]  # sofort + alle 60 s
```

Default-Root: `../mc-bot-vault` relativ zum Repo. Ablauf eines echten Laufs: Vault-Lock → `git pull --rebase origin main` → Kandidaten verschieben → Commit+Push mit allen alten und neuen Pfaden → Bericht `ops/monitor/vault-housekeeping.json` → Lock immer freigeben.

Kandidaten sind ausschließlich Dateien mit dem Chris-Tor der Domäne: Inbox `bereit`, Fragen `beantwortet`, Handlungen `erledigt`; `begonnen` ist kein Kandidat. Verschoben wird per `rename` **innerhalb desselben Ordners** nach `bearbeitet/`; bei Namenskonflikt wird eine Zahl angehängt. Es wird nie gelöscht, überschrieben oder Inhalt geändert. Bei Pull-/Push-Fehler: Bericht schreiben und `ok: false` — keine Tore setzen, keine Verschiebung rückgängig machen. Der Bericht enthält `at`, `candidates`, `moved`, `skipped`, `pushed`, `error`.

## 4. Bauplan-Freigabe und Ablauf

Neue Arbeiten entstehen über Baupläne in `ops/blueprints/` und werden erst nach Freigabe in der Werkbank sichtbar (`ops/BOARD.md`). Die Pipeline kennt `manual` (Standard) und `auto_if_clear` (nur bei vollständig grünen Gates). Nach Abschluss/Freigabe eines Bauplans aktualisiert `ops/wissenanalyse/handbuch-sync.mjs` den Katalog in Handbuch 03 und die Ereigniszeile unten; gleiche Bauplan-IDs werden nicht doppelt eingetragen.

Der übliche Weg: Idee mit `status: entwurf` → Chris setzt `bereit` → Intake erzeugt Task/Frage/Handlung → Worker arbeitet mit maximal 3 Runden → QA-Gate (4 Observer-Punkte + 3D-ViewPort, siehe Handbuch 02) → Chris gibt frei → Ergebnis, Board und Log werden gepusht. Bei `opencode`-Task-Ende zusätzlich `npm run verify -- --out ops/results/<ID>.verify.json`.

## 5. Vision-Budget im Ablauf

Vision ist kein Pflichtschritt: nur als letztes Siegel nach grüner Stufe 1, max. 1 Call pro Meilenstein, max. 3 pro Tag, getrennt von kilo2/OpenRouter. Bei Limit nicht blockieren, sondern melden: „Stufe 1 grün, Vision-Call wegen Tagesbudget übersprungen (Screenshots abgelegt)". Kontrolle: `node ops/kilo-handler/vision-budget.mjs status`.

## 6. Stop-Bedingungen (nur Außen-Risiken)

Nur bei diesen vier Lagen wird gestoppt und Chris gefragt:

1. Eingriff in den Live-Port `19132` oder Löschen/Überschreiben von Weltdaten.
2. Kostenpflichtige API-Calls ohne bestätigte Freigabe.
3. Windows-System- oder Firewall-Änderungen.
4. Finale Einladung zur fertigen Live-Präsentation (erst bei allen grünen Gates).

Alles andere — Merge-Konflikte in `ops/BOARD.md`, `ops/LOG.md`, `ops/QUESTIONS.md`, rote QA-Gates, beantwortete Rückfragen — läuft autonom weiter. Die Notbremse beendet nie den Live-Server `19132`.

## 7. Ereignisprotokoll (Baupläne)

Maschinenlesezone von `ops/wissenanalyse/handbuch-sync.mjs`; von Hand nicht verändern.

<!-- bauplan-ereignisse:begin -->
| Zeit | Bauplan (ID) | Ereignis | Freigabemodus |
| --- | --- | --- | --- |
| — | — | Noch keine Ereignisse | — |
<!-- bauplan-ereignisse:end -->



================================================================================
## DATEI: 2026-11-23__Handbuch-05_Autonome_Weltanpassung_und_Regie.md
================================================================================
# Handbuch: 05_Autonome_Weltanpassung_und_Regie.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json -> regieLeitplanken (Chris, 23.11.2026, T-227)
auto_aktualisierung: bauplan_abschluss
status: aktiv
---

# 05 — Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung

Dieses Kapitel ist die verbindliche, menschenlesbare Fassung der vier Regie-Leitplanken. Maschinenlesbar liegen sie in `ops/SYSTEM_SSOT.json` unter `regieLeitplanken`; die Umsetzung ist im Bauplan `BP-007 (Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung)` mit Freigabemodus `manual` beschrieben. Es gilt zusätzlich zu `AGENTS.md` §10 (QA-Gate) und §11 (End-to-End-Produktdenken).

**Übergeordneter Grundsatz:** Das System rät nicht, ändert nicht die Anforderung und macht Chris nicht zum manuellen Test-Klicker. Jede Prüfung läuft über reguläre, wiederverwendbare Bausteine; die finale Einladung zur Live-Präsentation bleibt Chris vorbehalten (AGENTS.md §7 Punkt 4).

## A — Capability- & Gap-Planner

Vor jedem Vorhaben wird `ops/capabilities.json` geprüft: *Kann ich das schon?* vs. *Fehlt mir noch etwas?* Fehlende Grundfähigkeiten werden automatisch als **wiederverwendbare Bausteine** herausgeschnitten und über `ops/SPEC_MAPPING.md` als Soll / Ist / Backlog dokumentiert. Ein einmaliges `_dbg`- oder Ad-hoc-Skript ist keine Dauerlösung (AGENTS.md §11 Punkt 3).

- **Anker:** `ops/capabilities.json`, `ops/SPEC_MAPPING.md`, `vault/wissen/Handbuch/03_Faehigkeiten_und_Bauplan_Katalog.md`
- **source:** Chris, 23.11.2026 (T-227); `ops/SYSTEM_SSOT.json` → `regieLeitplanken.A_capabilityGapPlanner`
- **begruendung:** Ohne vorherigen Fähigkeitsabgleich bauen Agenten doppelt oder raten IDs/Rezepte. Der Planner macht Lücken sichtbar, bevor Code entsteht.

## B — Adaptive Scene Director (Gelände- & Sichtlinien-Regisseur)

Zufallswelten werden über **`32×32`-Sektoren** erkundet. Der Director sucht geeignete Flächen und passt Bühnen **sanft an die Topografie an**: Podest und Stufen statt Betonklotz. Verdeckende Blöcke oder den Platz der Zuschauer-Loge korrigiert er automatisch über den 3D-ViewPort-Raycast.

- **Beweis:** Augenhöhe `y + 1,62`, ±45° horizontal, ±35° vertikal, 3–32 Blöcke, keine undurchsichtigen Blöcke auf der Sichtlinie (`ops/qa/world-invariants.mjs`, `ops/qa/observer-proof.mjs`).
- **Anker:** `ops/WORLD_INVARIANTS.md`, Handbuch 02 §3/§4
- **source:** Chris, 23.11.2026 (T-227); `ops/SYSTEM_SSOT.json` → `regieLeitplanken.B_adaptiveSceneDirector`
- **begruendung:** Eine Show muss auf jeder Zufallswelt begehbar und sichtbar sein. Starre Bühnen scheitern an Hügeln oder Bäumen; die sanfte Anpassung verhindert Nachbesserungen von Hand.

## C — Dreifache Realitäts-Kopplung & Multi-Seed-Gate (`N ≥ 3`)

Ein Lauf gilt **nur dann** als bestanden, wenn gleichzeitig present sind:

1. **Blockdaten** — `update_block`-Pakete bzw. Chunk-Daten an den Zielkoordinaten,
2. **freier 3D-Raycast** — Sichtlinie ohne undurchsichtige Blöcke,
3. **bequeme Spieler-Erreichbarkeit** — fester Boden, 2 Blöcke Kopffreiheit, begehbarer Pfad.

Zusätzlich muss das Ergebnis auf `VoxelCrew:Ende` (`19134`) in **mindestens 3 unterschiedlichen Zufalls-Seeds** selbstständig bestanden werden (Beispiele: Wald/Ebene, Hügelland, Küste/Wüste). Alte Test-Platten werden nach jedem Lauf automatisch per atomarem Master-Reset bereinigt (`.stage-<welt>` → `.world-origin.json` → `world.reset.completed`, `db/LOCK` entfernt).

- **Gate:** `N ≥ 3`, alle drei Beweise gleichzeitig, kein Grün bei nur Teilnachweis.
- **Anker:** `ops/SYSTEM_SSOT.json` → `welten.masterReset`, `e2e_gate`; `ops/WORLD_INVARIANTS.md`
- **source:** Chris, 23.11.2026 (T-227); `ops/SYSTEM_SSOT.json` → `regieLeitplanken.C_dreifacheRealitaetsKopplung`
- **begruendung:** Ein Einzelfall oder ein reiner Befehlsnachweis kann Zufall oder „Röntgenblick" sein. Drei Seeds plus drei gekoppelte Beweise machen das Ergebnis belastbar und wiederholbar.

## D — User-Empathy-Simulator & BehaviourPack↔Client-Symbiose

Ein **unprivilegierter Spieler-Simulator** prüft auf `19134` aus echter 10-Jahre-Spieler-Perspektive Erreichbarkeit, **Sichtfeld `FOV 70°`** und ruhiges Vorführ-Tempo. BehaviourPack-Effekte und UI (`@minecraft/server`, `@minecraft/server-ui`) müssen sauber mit den Client-Bot-Aktionen (`SWARM_BOT`) verzahnt sein.

- **Akteurs-Isolation:** `SWARM_BOT` bleibt Live-Profil und wird gemäß Handbuch 02 §2 **nie** auf `19133`/`19134` eingeloggt; auf den Testwelten läuft die Symbiose über `SHOW_ACTOR`/`OBSERVER`.
- **Anker:** `ops/SYSTEM_SSOT.json` → `akteure`, `spectator`; Handbuch 02 §2
- **source:** Chris, 23.11.2026 (T-227); `ops/SYSTEM_SSOT.json` → `regieLeitplanken.D_userEmpathy`
- **begruendung:** Technisch korrekte Blöcke allein sind kein gutes Erlebnis. Erst die Perspektive eines echten Spielers deckt unerreichbare Wege, zu schnelle Schnitte oder eine gerissene BP↔Client-Kopplung auf.

## Umsetzung und Sichtbarkeit

Die vier Bausteine liegen als Meilensteine A–D im Bauplan `BP-007`; Status `entwurf`, Freigabe `manual`. Der Plan erscheint im Bauplan-Inspektor der Werkbank (`http://127.0.0.1:7070`) und wird erst nach Chris' Freigabe umgesetzt. Das Kapitel ist in der Werkbank unter **Maschinen & Handbuch** lesbar (`/api/handbuch`, Datei `05_Autonome_Weltanpassung_und_Regie.md`).

## Verkehrsmittel der Testwelten

Seit BDS 1.26.63.1 nutzen **alle drei** Server `transport=nethernet`; `19133`/`19134` haben `enable-lan-visibility=false` und eigene UDP-Ports. Nur `19132` behält UDP 7551, LAN `true` und PID 18356 und bleibt für Tests und Shows strikt tabu. Details in Handbuch 02.



================================================================================
## DATEI: 2026-11-23__Handbuch-06_Gesamt_Entwicklungsplan_und_Meilensteine.md
================================================================================
# Handbuch: 06_Gesamt_Entwicklungsplan_und_Meilensteine.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/capabilities.json, ops/SPEC_MAPPING.md §11, ops/blueprints/registry.json (Chris, 23.11.2026, T-228)
auto_aktualisierung: bauplan_abschluss
status: aktiv
---

# 06 — Gesamt-Entwicklungsplan und Meilensteine

Dieses Kapitel ist die menschenlesbare Gesamtübersicht des VoxelCrew-Plans. Es sagt in einfachen Worten: **Was können wir schon? Was fehlt noch? In welcher Reihenfolge bauen wir es — und wie merken wir, dass es wirklich fertig ist?** Die technische Fassung steht in `ops/capabilities.json` (Inventur) und `ops/SPEC_MAPPING.md` (Soll / Ist / Backlog); die einzelnen Baupläne liegen unter `ops/blueprints/BP-000.json` … `BP-015.json`.

**Grundhaltung:** Ein Kind ab 10 Jahren soll das System bedienen können. Nichts wird geraten, nichts wird „einfach fertig" genannt, und Chris wird nie gebeten, im Spiel Testschritte von Hand einzutippen. Wir prüfen selbst — und zwar so, dass man es sehen kann.

- **source:** Chris, 23.11.2026 (T-228); `vault/00 Inbox/bearbeitet/00_README_KONZEPT_INDEX.md`, `01_architektur-briefing-multi-bot-bedrock-v14.md`, `02_ui-ux-architektur-briefing-leitstelle-v2.md`, `03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md`, Anlagen 01–10, `Handbuch/05`
- **begruendung:** Vorher wusste niemand auf einen Blick, welcher Baustein schon da ist und welcher als Nächstes kommt. Das führte zu doppelter Suche und zu Schritten, die man falsch „grün" nannte. Der Plan macht Stand, Reihenfolge und Beweis an einem Ort sichtbar — für Chris, seinen Sohn und alle Worker.

---

## 1. Wie das Ganze zusammenpasst

Stell dir eine kleine Theatertruppe vor. Die Bots sind die Schauspieler, die Werkbank ist der Regietisch und das Weltgedächtnis ist das Buch, in dem steht, was schon gebaut wurde.

1. **Wahrnehmen:** Ein Bot schaut sich um und sieht jede halbe Sekunde ein kleines Bild der Welt vor sich (64×36 Punkte). Dazu erkennt er Winken, Klatschen und „Folge mir".
2. **Denken und Handeln:** Er macht einen Schritt, schaut nach, ob es geklappt hat, und erst dann den nächsten. Sieben Arten von Handlungen stehen ihm bereit.
3. **Merken:** Alles, was gebaut wird, kommt in ein gemeinsames Buch. Alle Bots lesen darin, damit keiner die Arbeit des anderen kaputt macht.
4. **Zeigen:** Auf der Bühne und im Spiel sieht man, was passiert. Ein großer Hilfeknopf holt Hilfe, wenn etwas schiefgeht — mit einem **60 Sekunden langen Film** vom Geschehen.
5. **Verbessern:** Nach der Vorführung schreiben die Bots selbst auf, was gefallen hat. Die nächste Show wird automatisch besser.

Aus jeder dieser Fähigkeiten ist ein **Bauplan** geworden. Ein Bauplan ist wie ein Rezept: Erst die einfache Beschreibung, dann die Schritte, dann die technischen Details, ganz unten.

---

## 2. Was schon klappt — nachgewiesen grün

„Grün" heißt bei uns: Es gibt einen echten Test und einen echten Beweis. Nur zu sagen „ich habe den Befehl geschickt", zählt **nicht**.

| Bereich | Was wir schon können | Beweis |
| --- | --- | --- |
| Bühne und Show | Bots führen die Tanzshow auf, Observer sehen Blöcke und gehaltene Items wirklich | `show.run`, Observer-Proof: 254 `update_block` + 1 `mob_equipment`, `npm run verify:show` grün |
| Sichtlinie prüfen | Wir können prüfen, ob die Bühne frei sichtbar ist (Augenhöhe 1,62; 3–32 Blöcke) | `ops/qa/world-invariants.mjs`, `qa.voxelRaycast` |
| Server und Welten | Drei sauber getrennte Server; die Live-Welt wird nie angefasst | `bds.manager`, `world.chooseAndReset`, Master-Reset mit 8-s-Schutzfenster |
| Werkbank ohne Terminal | Jede Aktion wird im Hintergrund selbst committet und gepusht; Notizbuch-Export ohne git; FELDBOOK schaltet bei 85/90 ab | `sync.werkbankAuto` (25 Prüfungen), `export.notebookSync`, `ram.maschinenSchwellen` (13 Prüfungen) |
| Bedienung | Werkbank und zweite Leitstelle funktionieren ohne Konsolenfehler; 4-Stufen-Prozess und Bot-Hilfe sind überall | `hilfe.botHilfe`, zwei Browser-Tests mit Screenshot-Beleg |
| Gedächtnis (lokal) | Merker überleben einen Neustart | `memory.WorldMemory` (SQLite) |
| Gesten (Grundlagen) | Emotes werden erkannt und aufgelöst | `interaction.resolveGesture`, `interaction.handleEmote` |
| Regeln für Regie | Vier Leitplanken A–D sind überall verankert | `ops/SYSTEM_SSOT.json` → `regieLeitplanken`, `AGENTS.md` §12, Handbuch 05 |

**Wichtig:** Bei der Wahrnehmung ist nur der *Prüfstrahl* grün, nicht der dauerhafte 64×36-Sensor. Bei den Gesten fehlen noch drei echte Emote-Nummern — die werden vom echten Client aufgeschrieben, nicht erfunden.

---

## 3. Die Bauer-Kette — was von was abhängt

Ein Pfeil bedeutet: „Das muss erst fertig sein, bevor das nächste anfangen kann."

```
BP-007  Regie-Leitplanken (manual, unverändert — Grundlage)
   │
   └─► BP-008  Sensorik & Wahrnehmung (64×36, Gesten)
            │
            ├─► BP-009  Executive Loop (Denken→Handeln→Prüfen, 7 Handlungsgruppen)
            │        │
            │        ├─► BP-010  Gemeinsames Weltgedächtnis, Blockmuster, 2.5D-Karte
            │        │        ├─► BP-013  Ikonischer Resource-Pack-Generator
            │        │        └─► BP-012  Lokales Relay & Co-Pilot
            │        │                 └─► BP-014  Tribes/Frontier (eine Welt, ein Buch)
            │        └─► BP-011  In-Game-UI + Notfallknopf im Spiel
            └─► (BP-010 wie oben)

BP-015  Showcase-Regie & Review-Rückfluss
        braucht: BP-011 + BP-014 + BP-007
```

Alle neuen Baupläne `BP-008` bis `BP-015` sind **`status: entwurf`** und **`freigabe: manual`**. Das heißt: Sie sind fertig geplant, aber niemand startet sie ohne Chris. `BP-000` bis `BP-007` bleiben **unverändert** — keine Überschreibung, kein heimlicher Start.

---

## 4. Die acht neuen Baupläne — kurz erklärt

### BP-008 — Sensorik & Wahrnehmung
**Wenn** ich zum ersten Mal in eine neue Welt schaue, **will ich**, dass der Bot jede halbe Sekunde sieht, was vor ihm ist, **damit** er nicht gegen Blöcke rennt und meine Gesten versteht.
Schritte: Tiefenkarte 64×36 → Gesten-Ringpuffer (≤12 Blöcke, 2,5 Sekunden) → drei Emote-Nummern live erfassen → Beweis auf `19134`.

### BP-009 — Executive Loop
**Wenn** ich dem Bot einen Auftrag gebe, **will ich**, dass er ihn in kleinen Schritten macht und nach jedem Schritt selbst nachschaut, **damit** er nicht „fertig" sagt, obwohl nichts passiert ist.
Schritte: 50-ms-Takt mit fünf Phasen → sieben Handlungsgruppen → echte Server-Quittung → Journey-Beweis.

### BP-010 — Gemeinsames Weltgedächtnis
**Wenn** mehrere Bots bauen, **will ich**, dass sie sich merken, was schon da ist, **damit** keiner die Arbeit des anderen kaputt macht.
Schritte: eine Instanz pro Welt → nur kuratierte Blockmuster → 2.5D-Karte mit echten Daten → Zwei-Bot-Beweis.

### BP-011 — In-Game-UI & Pure-Client-Fallback
**Wenn** ich im Spiel neben dem Bot stehe, **will ich** mit höchstens drei Klicks sehen, was er macht, **damit** ich ohne Computer helfen kann.
Schritte: erst die vier Stufen (Satz, vier Zustände, Wireframe, Profi-Test) → Oberfläche im Spiel → gleiche Oberfläche ohne Script-API → großer Hilfeknopf mit 60-s-Film. **`server/**` bleibt bis zur Freigabe unangetastet.**

### BP-012 — Lokales Relay & Co-Pilot
**Wenn** ich von einem zweiten Gerät zuschauen will, **will ich**, dass die Bots nur bei uns zu Hause reden, **damit** niemand von außen steuern kann.
Schritte: nur Loopback → klare Co-Pilot-Stufen → Modus-Trennung → Beweis ohne externen Call. **Öffnung nach außen oder Firewall-Änderungen nur mit Chris' Tor.**

### BP-013 — Ikonischer Resource-Pack-Generator
**Wenn** die Bots etwas bauen, **will ich**, dass es im Spiel so aussieht wie auf unseren Bildern, **damit** ich auf einen Blick weiß, was gemeint ist.
Schritte: Kurateliste mit Quelle und Grund → Icons erzeugen → auf `19134` laden und beweisen → neue Muster sauber zurück in den Vault.

### BP-014 — Tribes/Frontier
**Wenn** viele Bots in verschiedenen Welten spielen, **will ich**, dass jede Gruppe ihr eigenes Buch hat, **damit** nichts durcheinanderkommt.
Schritte: eine SQLite-Instanz pro Welt → Grenzen aus echten Daten → Relay anbinden → Beweis mit zwei Seeds. **Tribes-Zahlen und IDs bleiben offen, bis sie belegt sind.**

### BP-015 — Showcase-Regie & Review-Rückfluss
**Wenn** die Vorführung vorbei ist, **will ich**, dass die Bots selbst aufschreiben, was gefallen hat, **damit** die nächste Show besser wird, ohne dass ich tippen muss.
Schritte: Trockenlauf auf `19134` → Abnahme auf `19133` mit vier grünen Toren → Feedback wird neue Bauplan-Version → Wizard-Suite in der Leitstelle. **Die Einladung zur fertigen Live-Show lädt ausschließlich Chris ein.**

---

## 5. Wie wir prüfen — die vier Tore

Ein Meilenstein ist erst fertig, wenn **alle vier** Punkte gleichzeitig grün sind:

1. **Observer dabei:** Ein eigener Zuschauer-Bot sitzt auf der Bühne, alle Akteure an der richtigen Stelle (keine „y≈32769"-Geister).
2. **Echte Pakete:** Blöcke und gehaltene Dinge kommen als `update_block` bzw. `mob_equipment` an — nicht nur als Befehl.
3. **Freie Sicht + 3D-Check:** Auf Augenhöhe (y + 1,62), ±45° waagerecht, ±35° senkrecht, 3–32 Blöcke weit, nichts Undurchsichtiges auf der Linie.
4. **Richtiger Ort:** Die Welt heißt wie erwartet und der Port stimmt (`19133` Show, `19134` Test).

Dazu kommt bei `BP-007`/Regie die **dreifache Realitäts-Kopplung**: Blockdaten **und** freier Raycast **und** bequemer Weg (fester Boden, zwei Blöcke Kopffreiheit) — und das auf **mindestens drei verschiedenen Zufalls-Seeds**. Nach jedem Lauf wird die Testplatte automatisch sauber zurückgesetzt.

Der komplette Ablauf läuft über reguläre Bausteine (`npm run show:accept`, `npm run verify:show`, `npm run verify`). **Chris tippt nichts.** Screenshots sind nur ein optionales Siegel nach grünem Stufe-1-Gate.

---

## 6. Bedienung ohne Terminal — für Chris und seinen Sohn

1. Werkbank öffnen → Idee einwerfen oder Bauplan ansehen.
2. Prüfpunkte und einfache Beschreibung lesen; bei Bedarf den großen **🆘 Bot-Hilfe**-Knopf drücken. Es entsteht automatisch ein 60-Sekunden-Film und eine Karte in Klartext mit drei möglichen nächsten Schritten.
3. Bauplan freigeben oder Frage beantworten — der Hintergrund speichert **und pusht** alles selbstständig in beide Repos. Schlägt ein Push fehl, steht es oben rot in der Werkbank und der Knopf kann erneut gedrückt werden.
4. Das Notizbuch (`npm run notebook:export`, automatisch beim Start und nach Bauplan-Abschlüssen) liegt als saubere Markdown-Dateien im Ordner `.local-notebook-sync/` — kein git nötig.

Oben rechts bleibt der **Not-Aus**; er pausiert nur die Anzeige des Worker-Dispatch. Die Live-Welt `19132` läuft weiter und wird nie beendet.

---

## 7. Regeln, die nie gebrochen werden

- `19132` ist die Live-Welt: strikt tabu für Shows, Tests und Notbremse; keine Weltdaten, keine Auth-Caches.
- Live-Profile (`WaechterBot`, `BauBot`, `SWARM_BOT`) nie auf `19133`/`19134`; dort nur `SHOW_ACTOR`/`OBSERVER`.
- Keine neuen Abhängigkeiten, keine kostenpflichtigen API-Calls, keine echten Konten, keine Firewall-/Systemänderungen ohne Chris.
- Nicht raten: Emote-Nummern, FW-Vorlagennamen, Rätselblöcke und Tribes-Zahlen/IDs bleiben offen, bis sie aus einer echten Quelle kommen.
- Kein Worker-Dispatch, bis Chris den Plan in der Werkbank geprüft hat. Keine zweite Rückfrage nötig — der Auftrag ist erteilt.

**Offene Punkte:** drei Emote-UUIDs live erfassen (BP-008); konkrete FW-Vorlagennamen (BP-004) und Rätselblöcke (BP-001); Tribes-Zahlen/IDs (BP-014). Sie sind bewusst als offen markiert und werden nicht erfunden.



================================================================================
## DATEI: 2026-11-23__Handbuch-07_Vier_Stufen_UI_Prozess.md
================================================================================
# Handbuch: 07_Vier_Stufen_UI_Prozess.md

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: vault/handbuch/07_Vier_Stufen_UI_Prozess.md

---
typ: handbuch
stand: 2026-11-23
quelle: ops/SYSTEM_SSOT.json -> uiProzess (Chris, 23.11.2026, T-228)
auto_aktualisierung: bauplan_abschluss
status: aktiv
---

# 07 — Verbindlicher 4-Stufen-Profi-UI-Prozess

Dieses Kapitel ist die menschenlesbare Fassung des Prozesses, der **vor jedem Coding** einer Oberfläche durchlaufen wird. Maschinenlesbar liegt er in `ops/SYSTEM_SSOT.json` → `uiProzess`, verbindlich verankert in `AGENTS.md` §13. Er gilt für die VoxelCrew-Werkbank `7070`, die VoxelCrew-2ndScreen-Leitstelle und die In-Game-UI (`@minecraft/server-ui` mit Pure-Client-Fallback).

**Grundhaltung:** Ein Kind ab 10 Jahren soll das System ohne Anleitung bedienen können. Wenn eine Oberfläche Erklärungen, Dateipfade oder Fachbegriffe braucht, ist sie noch nicht fertig. Chris wird niemals gebeten, im Client manuell Testschritte auszulösen (AGENTS.md §11 Punkt 4).

- **source:** Chris, 23.11.2026 (T-228); Referenz `2026-11-20 anlage-07-referenz-minecraft-ui-design-system-und-wireframes.md`
- **begruendung:** Bisher konnten Oberflächen direkt implementiert werden, ohne dass Ziel, Zustände und Vertrag vorab standen. Das erzeugte Nachbesserungen, Platzhalter im UI und Tests, die nur Code prüften, nicht die Journey. Die vier Stufen machen das vorher sichtbar.

---

## Stufe 1 — Job-to-be-Done in einem Satz

**Pflicht:** Genau ein Satz nach dem Muster

> „Wenn ______, will ich ______, damit ______."

Regeln:

- Sprache ab 10 Jahren; kein Fachbegriff, kein Dateipfad, kein Kürzel im Satz.
- **Maximal 3 Klicks** vom Auslöser bis zum sichtbaren Ergebnis. Der Klickpfad wird als Pfeilkette notiert: `Start → Knopf → Ergebnis`.
- Der Satz steht im Task und im Result; er ist die Ablehnungsgrenze für alles, was danach gebaut wird.

**Beispiel (Bot-Hilfe):** „Wenn der Bot etwas Falsches macht, will ich auf den großen Hilfeknopf drücken, damit er mir sofort sagt, was los ist und was ich tun kann." — Klickpfad: `Leitstelle → 🆘 Bot-Hilfe → Eskalationskarte` (2 Klicks).

**Beweis:** Ein-Satz-Text + Klickpfad im Task/Result. Fehlt er, gibt es keinen Implementierungsstart.

---

## Stufe 2 — 4-Zustands-Vertrag

Jeder Datenbereich einer Oberfläche muss vier explizite Zustände haben. Es gibt **keinen** fünften Zustand und keinen „irgendwie"-Zustand.

| Zustand | Bedeutung im UI | Erlaubte Darstellung | Verbot |
| :-- | :-- | :-- | :-- |
| `leer` | Noch nichts vorhanden | Freundlicher Satz + ein großer nächster Schritt | Leere Fläche, `-`, `null` |
| `laedt` | Anfrage läuft | Spinner + Kurztext „Ich schaue nach …" | Alte Daten als neu ausgeben |
| `gefuellt` | Echte Daten da | Werte aus der API/IPC-Antwort mit Quelle und Zeitpunkt | Platzhalter, erfundene Zahlen, rohes Markdown |
| `handlungsbedarf` | Etwas blockiert oder fehlt | Klartext + genau eine empfohlene Aktion | Stiller Fehler, Consolenfehler, Tech-Fehlercode im Haupttext |

**Harte Regel:** `gefuellt` ist nur erlaubt, wenn ein Beweis in der API-Antwort oder im Paketbeweis liegt. Ein fehlgeschlagener Request überschreibt nie den letzten guten Stand.

**Beweis:** Zustandstabelle je Bereich im Wireframe (Stufe 3), mit Spalte „Woher kommt der Wert?".

---

## Stufe 3 — ASCII-Wireframe nach anlage-07 + exakter Vertrag

**Pflicht vor dem Coding:** Eine ASCII-Skizze mit Klickzielen und Zustandsübergängen **und** ein exakter API-/IPC-Vertrag. Beides gehört in Task/Handbuch, bevor die erste Zeile UI-Code entsteht.

### Wireframe-Muster (Bot-Hilfe in der Leitstelle)

```
+--------------------------------------------------------------+
| VoxelCrew-Leitstelle        [ Server-Ampeln ]   [ 🆘 Bot-Hilfe ]|  <- Klickziel 1
+--------------------------------------------------------------+
| Zustand: gefuellt | Stand: 12:04 | Quelle: Werkbank           |
+--------------------------------------------------------------+
|                                                              |
|   🆘 Ich brauche Hilfe                                        |
|   Der letzte Minuten-Film ist gespeichert.                    |
|                                                              |
|   Was ist passiert?   Der Bot hat die Treppe nicht gesehen.   |
|   Was brauche ich?    Bitte schau auf den Film.               |
|                                                              |
|   [ Erneut versuchen ]  [ Auftrag stoppen ]  [ Mensch holen ] |  <- Klickziel 2
+--------------------------------------------------------------+
```

Zustandsübergänge: `leer` → (Klick) → `laedt` → (Antwort ok) → `gefuellt` / (Antwort fehler) → `handlungsbedarf`. Jeder Übergang ist im Wireframe beschriftet.

### API-/IPC-Vertrag (Beispiel Bot-Hilfe)

| Feld | Wert |
| :-- | :-- |
| Endpunkt | `POST /api/bot-hilfe` |
| Request | `{ "grund": "string (optional, max 200)", "kontext": "object (optional)" }` |
| Response 200 | `{ ok: true, hilfeId, clip: { von, bis, sekunden: 60, frames }, eskalation: { titel, wasPassiert, wasBraucheIch, optionen[] } }` |
| Response Fehler | `{ ok: false, error: "Klartext ohne Dateipfad" }` — UI zeigt `handlungsbedarf` mit Wiederholen-Knopf |
| In-Game (IPC) | Kompass → `ActionFormData` „🆘 Bot-Hilfe" → `hilfe.request` → Antwort `hilfe.card`; bei fehlendem BehaviourPack identische Journey per Chat `/hilfe` → Bot-Feedback → Second Screen |

Ein Vertrag ohne Fehlerfall ist unvollständig. Für die In-Game-Variante wird zusätzlich der Pure-Client-Fallback beschrieben; es werden keine IDs oder Effekte geraten (AGENTS.md §5).

---

## Stufe 4 — Abnahme wie ein Profi

**Pflichtnachweise:**

1. **Playwright-Klick-/Formular-Test** der kompletten Journey vom Auslöser bis zum sichtbaren Ergebnis — nicht nur Unit-Tests gegen Quelltext.
2. **Screenshot-Beleg** je kritischem Zustand (`gefuellt`, `handlungsbedarf`), abgelegt im Testlauf.
3. **0 Browser-Konsolenfehler** und **0 fehlgeschlagene API-Requests** (AGENTS.md §10.4).
4. **10-Jahre-Verständlichkeitscheck:** Haupttext ohne Dateipfade, ohne `API`/`IPC`/`IPC-Vertrag`-Kürzel, ohne Englisch. Ein Test liest die sichtbaren Texte und prüft die Verbotsliste.
5. **Bot-Hilfe-Nachweis:** Der große Notfallknopf erzeugt im Test einen 60-s-Clip und eine Eskalationskarte mit Klartext; beide sind im Beleg sichtbar.

Der Lauf ist über den `heavy-e2e`-Lock serialisiert. Für Werkbank-Änderungen gilt zusätzlich `tests/werkbank-browser-e2e.test.js`, für Leitstellen-Änderungen `npm run test:browser` (`tests/cockpit-browser-e2e.test.js`). Ein `npm run verify` ohne grünen Browser-Test gilt für UI-Änderungen nicht als abgeschlossen.

---

## Der große „Bot-Hilfe"-Notfallknopf

Der Baustein `ops/runtime/bot-hilfe.mjs` ist **eine** wiederverwendbare Plattformkomponente für alle drei Oberflächen (kein Wegwerf-Skript):

- **Auslöser:** Großer, gut sichtbarer Knopf mit 🆘 in der VoxelCrew-Werkbank und der 2ndScreen-Leitstelle. Im Spiel liegt er im In-Game-Kompass (Umsetzung in `BP-011 (In-Game-UI & Pure-Client-Fallback)`; `server/**` bleibt bis zur Freigabe unangetastet).
- **Automatik:** Beim Drücken wird der letzte **60-Sekunden-Flugschreiber-Clip** eingesammelt und abgelegt. Kein Nutzer muss etwas einstellen oder tippen.
- **Eskalationskarte:** Klartext mit vier Feldern — „Was ist passiert?", „Was habe ich erwartet?", „Was brauche ich?" und genau eine empfohlene Aktion. Keine Dateipfade, keine Tech-Kürzel, keine Schuldzuweisung.
- **Ablage:** `ops/runtime/bot-hilfe/` (gitignored). Kein Clip, kein Geheimnis und keine Weltdatei wird committet.

**Klickpfad:** `Leitstelle → 🆘 Bot-Hilfe → Eskalationskarte` — 2 Klicks, erfüllt Stufe 1.

## Geltungsbereich im Überblick

| Oberfläche | Stufe 1–3 dokumentiert | Stufe 4 Browser-Test | Bot-Hilfe-Status |
| :-- | :-- | :-- | :-- |
| Werkbank `7070` | ja (T-228) | `tests/werkbank-browser-e2e.test.js` | umgesetzt |
| 2ndScreen-Leitstelle | ja (T-228) | `npm run test:browser` | umgesetzt |
| In-Game-Kompass | ja (T-228 / BP-011) | E2E auf `19134` nach Freigabe | vertraglich festgelegt, Umsetzung in BP-011 |

**Offen (nicht geraten):** Die konkreten Emote-UUIDs für Winken/Klatschen/Folge-mir sind im Vault ausdrücklich „live erfassen"; sie werden nicht erfunden. Die In-Game-Dialoge entstehen erst mit Freigabe von `BP-011`.



================================================================================
## DATEI: 2026-11-23__INDEX.md
================================================================================
# Notizbuch-Export — Inhalt

Zeitpunkt: 2026-11-23T12:29:45.735Z
Datums-Präfix: `2026-11-23`

- System-SSOT: ja
- AGENTS.md: ja
- Handbuchkapitel: 7
- Baupläne: 16
- capabilities.json: ja
- System-Chronik 23.11.2026: ja (`2026-11-23_SYSTEM_CHRONIK_UMBAU.md`)

## Dateien

- 2026-11-23__SYSTEM_SSOT.md
- 2026-11-23__AGENTS.md
- 2026-11-23__capabilities.md
- 2026-11-23__Handbuch-01_Systemueberblick_und_Architektur.md
- 2026-11-23__Handbuch-02_Server_Welten_und_Akteure.md
- 2026-11-23__Handbuch-03_Faehigkeiten_und_Bauplan_Katalog.md
- 2026-11-23__Handbuch-04_Bedienung_und_Ablaeufe.md
- 2026-11-23__Handbuch-05_Autonome_Weltanpassung_und_Regie.md
- 2026-11-23__Handbuch-06_Gesamt_Entwicklungsplan_und_Meilensteine.md
- 2026-11-23__Handbuch-07_Vier_Stufen_UI_Prozess.md
- 2026-11-23__BP-000.md
- 2026-11-23__BP-001.md
- 2026-11-23__BP-002.md
- 2026-11-23__BP-003.md
- 2026-11-23__BP-004.md
- 2026-11-23__BP-005.md
- 2026-11-23__BP-006.md
- 2026-11-23__BP-007.md
- 2026-11-23__BP-008.md
- 2026-11-23__BP-009.md
- 2026-11-23__BP-010.md
- 2026-11-23__BP-011.md
- 2026-11-23__BP-012.md
- 2026-11-23__BP-013.md
- 2026-11-23__BP-014.md
- 2026-11-23__BP-015.md
- 2026-11-23__blueprint-registry.md
- 2026-11-23_SYSTEM_CHRONIK_UMBAU.md


================================================================================
## DATEI: 2026-11-23__SYSTEM_SSOT.md
================================================================================
# System-SSOT

> Exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: ops/SYSTEM_SSOT.json
```json
{
  "schema": "voxelcrew-ssot.v1",
  "stand": "2026-11-23",
  "konzept_revision": "v1 von Chris",
  "zweck": "Einzige widerspruchsfreie Quelle für Worker, Maschinen, Server, Akteure und Schutzgrenzen. ops/runner.json, ops/runner.alt-laptop.json, ops/workers.json, ops/WORLD_INVARIANTS.md und AGENTS.md leiten sich hieraus ab.",
  "dispatch": {
    "paused": true,
    "grund": "Neuaufbau läuft; Freigabe erst nach grünem Test und Live-Laden der Werkbank",
    "pause_datei": "ops/runner.stop"
  },
  "maschinen": {
    "hauptrechner": {
      "label": "Hauptrechner",
      "allowedWorkers": [
        "kilo1",
        "kilo2",
        "kilo5"
      ],
      "maxParallelByHost": 2,
      "ram": {
        "warn_used_pct": 80,
        "warn_free_gb": 3,
        "emergency_used_pct": 90,
        "emergency_free_gb": 1.5
      }
    },
    "feldbuch": {
      "label": "FELDBOOK (Alt-Laptop)",
      "allowedWorkers": [
        "kilo3",
        "kilo4"
      ],
      "maxParallelByHost": 2,
      "ram": {
        "warn_used_pct": 85,
        "warn_free_gb": 3,
        "emergency_used_pct": 90,
        "emergency_free_gb": 1.5
      }
    }
  },
  "ramSchutz": {
    "quelle": "maschinen.<name>.ram",
    "maschineErkennung": "MCBOT_MACHINE=hauptrechner|feldbuch (Default hauptrechner); --machine ueberschreibt",
    "hinweis": "FELDBOOK warnt ab 85 % belegt, stoppt ab 90 %. Hauptrechner warnt ab 80 %, stoppt ab 90 %. Der Live-BDS 19132 ist von der Notbremse ausgenommen."
  },
  "workers": [
    {
      "name": "kilo1",
      "worker": "gateway",
      "modell": "kilo-auto/free",
      "host": "hauptrechner",
      "phase": 1,
      "label": "Kilo1 — kilo-auto/free (gateway)"
    },
    {
      "name": "kilo2",
      "worker": "openrouter",
      "modell": "openrouter/free + Fallback-Kette aus T-199",
      "host": "hauptrechner",
      "phase": 1,
      "label": "Kilo2 — OpenRouter mit Fallback-Kette",
      "kiloAutoFinalTry": false
    },
    {
      "name": "kilo3",
      "worker": "gateway",
      "modell": "kilo-auto/free",
      "host": "feldbuch",
      "phase": 1,
      "label": "Kilo3 — kilo-auto/free (gateway, FELDBOOK)"
    },
    {
      "name": "kilo4",
      "worker": "gateway",
      "modell": "kilo-auto/free",
      "host": "feldbuch",
      "phase": 1,
      "label": "Kilo4 — kilo-auto/free (gateway, FELDBOOK)"
    },
    {
      "name": "kilo5",
      "worker": "gateway",
      "modell": "kilo-auto/free",
      "host": "hauptrechner",
      "phase": 1,
      "label": "Kilo5 — kilo-auto/free (gateway)"
    }
  ],
  "kosten": {
    "standardpfad": "kilo-auto/free (0 €)",
    "erlaubt": [
      "gateway kilo-auto/free",
      "openrouter/free-Fallback (0 €)"
    ],
    "verboten": [
      "kostenpflichtige API-Calls ohne Chris-Freigabe"
    ]
  },
  "vision": {
    "modus": "nur_als_letztes_siegel",
    "voraussetzung": "Stufe-1-Observer-Proof und 3D-ViewPort vollständig grün",
    "maxCallsProMeilenstein": 1,
    "maxCallsProTag": 3,
    "getrenntVon": "kilo2 (OpenRouter-Fallbackkette)",
    "beiLimit": "Meldung 'Stufe 1 grün, Vision-Call wegen Budget übersprungen (Screenshots abgelegt)' — kein Blockieren"
  },
  "prozessschutz": {
    "hartesTimeoutMs": 30000,
    "prozessbaumKill": "taskkill /F /T /PID <pid>",
    "gitAufrufe": [
      "--no-optional-locks",
      "credential.interactive=never"
    ],
    "gitWriteLock": true,
    "ram": {
      "pauseAbPct": 80,
      "pauseUnterFreieGb": 3,
      "notbremseAbPct": 90,
      "notbremseUnterFreieGb": 1.5
    },
    "verifyUndE2Eserialisiert": true,
    "keinTaskLevelCooldownBei429": true,
    "quarantaeneNachFehlstarts": 2,
    "liveServerGeschuetzt": 19132
  },
  "server": [
    {
      "rolle": "live",
      "label": "Live-Welt",
      "name": "VoxelCrew:Oberwelt",
      "host": "127.0.0.1",
      "port": 19132,
      "portV6": 19142,
      "transport": "nethernet",
      "enableLanVisibility": true,
      "udp": 7551,
      "managerRolle": "bds-live-19132",
      "levelName": "Oberwelt",
      "betrieb": "bestehender Prozess wird nur beobachtet (attach), nie neu gestartet",
      "testsUndShowcases": "strikt tabu",
      "notbremse": "nie beenden"
    },
    {
      "rolle": "showcase",
      "label": "Showcase-Bühne",
      "name": "VoxelCrew:Nether",
      "anzeigeName": "VoxelCrew:Nether 127.0.0.2:19133",
      "host": "127.0.0.2",
      "port": 19133,
      "portV6": 19143,
      "transport": "nethernet",
      "enableLanVisibility": false,
      "endpunkt": "direkt",
      "startNurDurchChatbefehl": "!show",
      "levelName": "ShowWorld",
      "managerRolle": "bds-show-19133"
    },
    {
      "rolle": "e2e",
      "label": "E2E-Testserver",
      "name": "VoxelCrew:Ende",
      "anzeigeName": "VoxelCrew:Ende 127.0.0.3:19134",
      "host": "127.0.0.3",
      "port": 19134,
      "portV6": 19144,
      "transport": "nethernet",
      "enableLanVisibility": false,
      "endpunkt": "direkt",
      "levelName": "E2EWorld",
      "managerRolle": "bds-e2e-19134"
    }
  ],
  "bdsManager": {
    "modus": "attach_oder_start",
    "registry": "ops/runtime/pids.json",
    "regeln": [
      "19132 wird ausschliesslich attachiert: kein Start, kein Stopp, kein Neustart, keine Weltdaten-Beruehrung.",
      "19133 und 19134 starten nur ueber den BDS-Manager und werden mit PID in der Registry gefuehrt.",
      "Show-Beginn auf 19133 erfolgt ausschliesslich per Ingame-Chatbefehl !show.",
      "Alle Manager-Aktionen laufen ueber ops/runtime/process-control.mjs mit hartem Timeout und Prozessbaum-Kill."
    ],
    "serverVerzeichnisse": {
      "19132": "server",
      "19133": "C:/ProgramData/MCBot/presentation-server",
      "19134": "C:/ProgramData/MCBot/e2e-server"
    }
  },
  "welten": {
    "vorlagenVerzeichnis": "data/world-templates",
    "standardVorlage": "FW-Base",
    "geländeModus": "level-seed",
    "weltAuf19134": "E2EWorld",
    "masterReset": {
      "lockDatei": "db/LOCK",
      "vorbereitungsOrdner": ".stage-<welt>",
      "originDatei": ".world-origin.json",
      "abschlussDatei": "world.reset.completed",
      "schutzfensterMs": 8000
    },
    "vorabErkundung": {
      "topografie": true,
      "sichtlinien": true,
      "augenhoeheOffset": 1.62,
      "beweis": "ops/qa/observer-proof.mjs"
    }
  },
  "akteure": {
    "regeln": [
      "Live-Bots (WaechterBot, BauBot, SWARM_BOT) dürfen niemals auf 19133/19134 eingeloggt werden.",
      "Auf 19133/19134 laufen ausschließlich SHOW_ACTOR- und OBSERVER-Profile.",
      "Umgekehrt dürfen SHOW_ACTOR/OBSERVER niemals auf 19132 eingeloggt werden.",
      "Die Prüfung erfolgt zentral in ops/lib/actor-profile-guard.mjs vor jedem Verbindungsaufbau."
    ],
    "live": [
      "WaechterBot",
      "BauBot",
      "SWARM_BOT"
    ],
    "showcase": [
      "SHOW_ACTOR",
      "OBSERVER"
    ],
    "e2e": [
      "SHOW_ACTOR",
      "OBSERVER"
    ]
  },
  "e2e_gate": {
    "observerProofPunkte": 4,
    "viewPortRaycast": true,
    "port": 19134,
    "showAbnahmePort": 19133,
    "keinGruenOhneBeides": true
  },
  "regieLeitplanken": {
    "stand": "2026-11-23",
    "quelle": "Chris, 23.11.2026 (T-227); vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md",
    "verbindlich": true,
    "A_capabilityGapPlanner": {
      "name": "Capability- & Gap-Planner",
      "regel": "Vor jedem Vorhaben ops/capabilities.json pruefen ('Kann ich schon' vs. 'Fehlt mir noch') und fehlende Grundfaehigkeiten automatisch als wiederverwendbare Bausteine herausschneiden.",
      "capabilities": "ops/capabilities.json",
      "mapping": "ops/SPEC_MAPPING.md"
    },
    "B_adaptiveSceneDirector": {
      "name": "Adaptive Scene Director (Gelaende- & Sichtlinien-Regisseur)",
      "regel": "Zufallswelten ueber 32x32-Sektoren erkunden, geeignete Flachen finden, Buehnen sanft an die Topografie anpassen (Podest/Stufen statt Betonklotz) und verdeckende Bloecke oder den Zuschauer-Logenplatz automatisch ueber den 3D-ViewPort-Raycast korrigieren.",
      "sektorGroesse": "32x32",
      "raycast": "ops/qa/world-invariants.mjs (voxelRaycast)"
    },
    "C_dreifacheRealitaetsKopplung": {
      "name": "Dreifache Realitaets-Kopplung & Multi-Seed-Gate",
      "beweise": [
        "Blockdaten (update_block / Chunk-Daten)",
        "freier 3D-Raycast (Sichtlinie ohne undurchsichtige Bloecke)",
        "bequeme Spieler-Erreichbarkeit (fester Boden, 2 Bloecke Kopffreiheit, begehbarer Pfad)"
      ],
      "alleDreiGleichzeitig": true,
      "multiSeedGate": {
        "port": 19134,
        "minSeeds": 3,
        "seedBeispiele": [
          "Wald/Ebene",
          "Huegelland",
          "Kueste/Wueste"
        ],
        "plattenBereinigung": "automatisch nach jedem Testlauf (atomarer Master-Reset)"
      }
    },
    "D_userEmpathy": {
      "name": "User-Empathy-Simulator & BehaviourPack↔Client-Symbiose",
      "port": 19134,
      "perspektive": "unprivilegierter 10-Jahre-Spieler",
      "fovGrad": 70,
      "tempo": "ruhig",
      "symbiose": [
        "@minecraft/server",
        "@minecraft/server-ui",
        "SWARM_BOT"
      ]
    }
  },
  "spectator": {
    "port": 19134,
    "banner": "🎬 E2E-Szene läuft auf VoxelCrew:Ende (127.0.0.3:19134)",
    "nurBeiEchtenVisuellenLaeufen": true,
    "vorlaufSekunden": 12,
    "schlussbildSekunden": 3,
    "schrittPausenFaktor": [
      1.5,
      2
    ],
    "joinBefehle": [
      "gamemode spectator",
      "effect night_vision 999 255"
    ],
    "zuschauerPlatz": {
      "modus": "frei",
      "sichtlinieZu": "Szene-Zentrum aus ops/WORLD_INVARIANTS.md",
      "logplatzPruefung": "3D-ViewPort und Voxel-Raycast"
    },
    "akteur": "SPECTATOR (nur 19134; nie 19132)"
  },
  "werkbank": {
    "port": 7070,
    "host": "127.0.0.1",
    "login": false,
    "sse": true,
    "freigabeStandard": "manual"
  },
  "uiProzess": {
    "verbindlich": true,
    "quelle": "T-228; AGENTS.md §13; vault/handbuch/07_Vier_Stufen_UI_Prozess.md",
    "regel": "Vier Stufen vor dem Coding nachweisen; ohne Nachweis nicht abnahmefaehig.",
    "geltungsbereich": [
      "werkbank:7070",
      "cockpit:2ndScreen",
      "ingame:@minecraft/server-ui+pure-client-fallback"
    ],
    "stufen": [
      {
        "nr": 1,
        "id": "jobToBeDone",
        "titel": "Job-to-be-Done",
        "pflicht": "Ein Satz 'Wenn ..., will ich ..., damit ...' in Sprache ab 10 Jahren; maximal 3 Klicks; keine Dateipfade und keine Tech-Kuerzel.",
        "beweis": "Ein-Satz-Text + Klickpfad im Task/Result"
      },
      {
        "nr": 2,
        "id": "zustandsvertrag",
        "titel": "4-Zustands-Vertrag",
        "zustaende": [
          "leer",
          "laedt",
          "gefuellt_mit_echten_daten",
          "handlungsbedarf"
        ],
        "pflicht": "Kein rohes Markdown, kein Platzhalter, keine erfundenen Zahlen; 'gefuellt' nur mit echtem Datenbeweis.",
        "beweis": "Zustandstabelle je Bereich im Wireframe"
      },
      {
        "nr": 3,
        "id": "wireframeUndVertrag",
        "titel": "ASCII-Wireframe + API-/IPC-Vertrag",
        "pflicht": "Skizze nach anlage-07 mit Klickzielen und Zustandsuebergaengen sowie exaktem Endpunkt, Methode, Request, Response und Fehlerfall vor dem Coding.",
        "beweis": "Wireframe + Vertrag im Task/Handbuch vor Implementierung"
      },
      {
        "nr": 4,
        "id": "abnahme",
        "titel": "Playwright-Abnahme + Bot-Hilfe",
        "pflicht": "Klick-/Formular-Test, Screenshot-Beleg, 0 Browser-Konsolenfehler, 0 fehlgeschlagene API-Requests, 10-Jahre-Verstaendlichkeitscheck; grosser Bot-Hilfe-Knopf mit 60-s-Clip und Eskalationskarte.",
        "beweis": "tests/*.browser.test.js + Screenshot + Bot-Hilfe-Beleg"
      }
    ],
    "botHilfe": {
      "baustein": "ops/runtime/bot-hilfe.mjs",
      "clipSekunden": 60,
      "eskalationskarte": "Klartext ohne Dateipfade und Tech-Kuerzel",
      "orte": [
        "werkbank:7070",
        "cockpit:2ndScreen",
        "ingame:kompass (Umsetzung in BP-011)"
      ],
      "ablage": "ops/runtime/bot-hilfe/ (gitignored)"
    }
  }
}
```


================================================================================
## DATEI: 2026-11-23_SYSTEM_CHRONIK_UMBAU.md
================================================================================
# System-Chronik: Der große Umbau vom 23.11.2026

> Historischer Schnitt · exportiert am 2026-11-23T12:29:45.735Z · schreibgeschützte Kopie für dein Notizbuch.
> Quellen: `ops/_archiv/pre-rebuild-2026-11-23/rebuild-archive-report.json`, `ops/SYSTEM_SSOT.json`, `ops/blueprints/registry.json`, `ops/capabilities.json`, `ops/SPEC_MAPPING.md`

## Vorher → Nachher

| Vorher (bis 22.11.2026) | Nachher (ab 23.11.2026) |
| --- | --- |
| Alte Tasks, Results, Fragen und viele Worktrees lagen im Arbeitsbaum | **Tabula Rasa:** Altlasten archiviert, keine Daten gelöscht (205 Tasks, 174 Results) |
| Runner-/Worker-Dateien konnten auseinanderlaufen | Eine SSOT (`voxelcrew-ssot.v1`) als Wahrheit; generierte Configs ohne Drift |
| Transport war uneindeutig (RakNet/NetherNet-Drift) | NetherNet auf **allen drei** Servern; nur 19132 mit LAN `true` und UDP 7551 |
| Keine zentrale Bedienstelle ohne Terminal | Neue **Werkbank `127.0.0.1:7070`**, login-frei, SSE, Freigabe `manual` |
| Baupläne und Fähigkeiten standen verstreut | Inventur in `ops/capabilities.json` + Mapping in `ops/SPEC_MAPPING.md`; Baupläne in der Registry |
| Oberflächen konnten ohne Plan entstehen | Verbindlicher **4-Stufen-Profi-UI-Prozess** mit Bot-Hilfe-Knopf und 60-s-Clip |
| Worker liefen automatisch weiter | Dispatch pausiert, bis Chris den Gesamtplan in der Werkbank geprüft hat |
| Änderungen brauchten ein Terminal | Auto-Commit/Push im Hintergrund und git-freier Notizbuch-Export |

## Archivierung (nichts weggeworfen)

- `ops/_archiv/pre-rebuild-2026-11-23/` — Archiv-Report mit Zweck „Tabula Rasa: Altlasten archiviert, keine Daten gelöscht"; alte `BOARD.md`, `LOG.md`, `QUESTIONS.md`, `REVIEWS.md`, Freigabe- und Report-Dateien; Worktrees als Patches + Untracked-Snapshots gesichert; vier Branch-Bundles; zwei offene Vault-Fragen (`Fragen-T-199`, `Fragen-T-200`) verschoben statt gelöscht.
- `2026-11-23-spuelgang/` — der zweite Durchgang („Spülgang") des Umbaus, wie von Chris benannt; als historischer Pfad festgehalten. Detailinhalte liegen im aktuellen Arbeitsbaum nicht vor (Stand: dieser Export).

## Drei Server, ein Transport

| Rolle | Adresse | Transport | LAN | UDP | Schutz |
| --- | --- | --- | --- | --- | --- |
| Live-Welt | 127.0.0.1:19132 | nethernet | an | 7551 | nie anfassen |
| Showcase-Bühne | 127.0.0.2:19133 | nethernet | aus | 19133 | Test/Show |
| E2E-Testserver | 127.0.0.3:19134 | nethernet | aus | 19134 | Test/Show |

Die Live-Welt `19132` bleibt attach-only, wird nie neu gestartet und ist für Shows und Tests strikt tabu.

## Neue Werkbank und Baupläne

- Werkbank auf Port **7070**: 16 Baupläne in der Registry (BP-000 … BP-015), Freigabestandard `manual`.
- `BP-000`–`BP-007` blieben beim Umbau unverändert; T-228 ergänzte die abhängigkeitsgeprüfte Kette `BP-008`–`BP-015` (Sensorik, Executive Loop, Weltgedächtnis, In-Game-UI, Relay, Icons, Tribes, Showcase-Review).
- Inventur: 12 Bereiche nachgewiesen grün, 11 mit Bauplan dokumentiert.
- 4-Stufen-Prozess: (1) ein Satz ab 10 Jahren/max. 3 Klicks, (2) vier Zustände ohne Platzhalter, (3) Wireframe + Vertrag vor dem Coding, (4) echter Klick-Test mit Bildbeleg. Großer Bot-Hilfe-Knopf mit automatischem 60-s-Flugschreiber-Clip und Eskalationskarte.

## Offene Punkte und Grenzen

- Kein Eingriff in 19132, keine Weltdaten-/Auth-/Secret-Änderung, keine Firewall-Änderung, keine kostenpflichtigen API-Calls.
- Emote-UUIDs, FW-Vorlagennamen, Rätselblöcke und Tribes-Zahlen/IDs werden nicht geraten, sondern bleiben offen.
- Worker-Dispatch bleibt pausiert; die finale Einladung zur Live-Präsentation lädt ausschließlich Chris ein.


