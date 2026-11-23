# VoxelCrew — Sync-Bundle (Gesamtspezifikation)

> Erzeugt am 2026-11-23T13:44:41.224Z aus dem laufenden Repo · schreibgeschützte Konsolidierung für dein Notizbuch.
> Eine Datei statt vieler: Inventur, SSOT, Regeln, Welt-Invarianten, Mapping, Capabilities, Registry, alle Baupläne, Handbuch und Konzept/Anlagen.
> Quellmodus: eingecheckter Quellspiegel (CI-Fallback) · Negativliste: 0 Treffer

## Inhalt

- Post-Spülgang-Inventur (Ebene 1 & 2, Negativliste): GRÜN / 0 Treffer
- `ops/SYSTEM_SSOT.json` inkl. `repos` (Slugs und Pages-URL)
- `AGENTS.md`, `ops/WORLD_INVARIANTS.md`, `ops/SPEC_MAPPING.md`, `ops/capabilities.json`, `ops/blueprints/registry.json`
- Handbuchkapitel: 7 (01–07)
- Konzept-/Anlagendokumente: 20
- Baupläne: 16 (BP-000–BP-015)

## Post-Spülgang-Inventur

```markdown
| Prüfung | Ergebnis |
| --- | --- |
| Quellmodus | eingecheckter Quellspiegel (CI-Fallback) |
| Vault Ebene 1 (Positivliste) | 3/13 Ordnungs- und Dateieinträge, unerwartet: 0 |
| Vault Ebene 2 (Pflichtinhalte) | 2 geprüfte Bereiche, Fehler: 0 |
| Negativliste Vault + Code-Repo | 0 Treffer |
| Gesamturteil | GRÜN |
```

## Repos und Links (SSOT)

```json
{
  "leitstelle": "brotfeld/mc-bot-leitstelle",
  "vault": "brotfeld/mc-bot-vault",
  "notebookMirror": "brotfeld/voxelcrew-notebook-sync",
  "notebookPagesUrl": "https://github.io",
  "vaultSlugQuelle": "SSOT (Vault im CI nicht vorhanden — Fallback)",
  "hinweis": "Der Vault-Slug wird beim Export aus dem echten Vault-Remote gelesen und gegen diesen Wert geprüft; fehlt der Vault im CI, gilt der SSOT-Wert."
}
```

## ops/SYSTEM_SSOT.json

```json
{
  "schema": "voxelcrew-ssot.v1",
  "stand": "2026-11-23",
  "konzept_revision": "v1 von Chris",
  "zweck": "Einzige widerspruchsfreie Quelle für Worker, Maschinen, Server, Akteure und Schutzgrenzen. ops/runner.json, ops/runner.feldbuch.json, ops/workers.json, ops/WORLD_INVARIANTS.md und AGENTS.md leiten sich hieraus ab.",
  "repos": {
    "leitstelle": "brotfeld/mc-bot-leitstelle",
    "vault": "brotfeld/mc-bot-vault",
    "notebookMirror": "brotfeld/voxelcrew-notebook-sync",
    "notebookPagesUrl": "https://github.io",
    "vaultSlugQuelle": "git remote get-url origin im Vault-Repo (read-only; SSOT-Wert muss damit übereinstimmen)",
    "hinweis": "Der Vault-Slug wird beim Export aus dem echten Vault-Remote gelesen und gegen diesen Wert geprüft; fehlt der Vault im CI, gilt der SSOT-Wert."
  },
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

## AGENTS.md

```markdown
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
  (`ops/runner.json`, `ops/runner.feldbuch.json`, `ops/workers.json`) werden
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
* `vault: write` — schreiben nur in `wissen/minecraft/Daten/kuratiert/**` und eigene Notizen mit `status: entwurf`.
* Niemals schreiben: `Daten/extrahiert/**`, `Index/**`, `Schema.md`.
* Vor Item-, Block- oder Rezeptarbeit `vault/wissen/minecraft/README.md` und die genannten Datensätze lesen.
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
```

## ops/WORLD_INVARIANTS.md

```markdown
# WORLD_INVARIANTS.md — Verbindlicher QA- & Welt-Standard

Chris-Vorgabe vom 21.11.2026, Punkt 3. Gilt **verbindlich für alle Showcases
und Meilensteine** auf `19133` (Show) und `19134` (E2E). Verankert in
`AGENTS.md` §10.2 und als hartes Gate in `ops/scripts/verify.mjs`.

Dieses Dokument beschreibt Defaults. Eine bewusste Abweichung ist **nur**
gültig, wenn der jeweilige Regieplan (`intent`) sie ausdrücklich als
`intent.exceptions` benennt (Beispiel: `exit_stage` beim geordneten Abgang).
Ohne Eintrag gilt der Default — auch dann, wenn er „ungeschrieben" war.

## 0. Transport- und Port-Invarianten (Chris, 23.11.2026, T-227; ersetzt Q-T216-03)

BDS 1.26.63.1 unterstützt **ausschließlich** NetherNet; `transport=raknet`
erzeugt den `TRANSPORT TYPE ERROR`. Chris' verbindliche Matrix (SSOT
`ops/SYSTEM_SSOT.json`):

| Adresse | Rolle | Transport | LAN-Visibility | UDP |
| --- | --- | --- | --- | --- |
| `127.0.0.1:19132` (`19142`) | Live-Welt, höchster Schutz | `nethernet` | `true` | `7551` exklusiver Inhaber |
| `127.0.0.2:19133` (`19143`) | Showcase-Bühne, direkter Endpunkt | `nethernet` | `false` | `19133` (eigener Port, nie `7551`) |
| `127.0.0.3:19134` (`19144`) | E2E-Testserver, direkter Endpunkt | `nethernet` | `false` | `19134` (eigener Port, nie `7551`) |

Showcases starten nur per Ingame-Chat-Befehl `!show`. Akteure sind strikt
getrennt: Live (`WaechterBot`, `BauBot`, `SWARM_BOT`) nie auf 19133/19134; dort
ausschließlich `SHOW_ACTOR`/`OBSERVER`; umgekehrt nie auf 19132. Erzwungen
durch `ops/lib/actor-profile-guard.cjs`.

* `transport=nethernet` gilt für **alle drei** Server. `19133`/`19134` haben
  `enable-lan-visibility=false` und eigene UDP-Ports (`server-udp-ports=19133`
  bzw. `19134`); Bots, `observer` und Chris' echter Client verbinden direkt und
  **port-exakt**. NetherNet-Discovery-Broadcasts auf UDP `7551` sind dort
  untersagt.
* `19132` bleibt strikt `nethernet` + UDP `7551` und wird von Show/Test nie
  berührt. Keine Port-, Firewall- oder LAN-Visibility-Änderung am Live-Server.
* Der Start-Check `node ops/scripts/check-e2e-server.mjs` verlangt im frischen
  BDS-Log `"transport":"nethernet"` und `Server started.` sowie das Fehlen von
  `TRANSPORT TYPE ERROR`.
* Der `observer`-Beweis muss die **tatsächlich verbundene** Server-Identität
  und den tatsächlichen Port prüfen (`level_name` = `ShowWorld`, Port `19133`),
  nicht nur das angeforderte `target_port`. Eine Abweichung (z. B.
  `TestWorld`/`19132`) ist sofortiger Abbruch und `result=RED` — ein
  sogenanntes „Grün" gegen den Live-Server ist ein False-Green.
* Maschinell geprüft durch `checkTransport()` in
  `ops/qa/world-invariants.mjs` und `tests/world-invariants.test.js`.

## 1. Unabhängiger `observer`-Bot und Paket-Beweis

* Jeder E2E-Lauf und jeder Show-Abnahmelauf verbindet einen **separaten**
  Client mit dem Benutzernamen `observer`, normaler Spielerstatus im
  Zuschauerbereich (kein Spectator-Modus, keine Admin-Rechte nötig).
* Alle Show-Bots müssen für den `observer` `visible=true` sein (nicht
  `invisible`) und auf der Geländeoberfläche stehen (`on_surface=true`,
  keine Kuhle, kein Versatz unter die Oberfläche).
* **Blöcke** (Bühne, Jukebox, Kulisse) gelten erst als bestanden, wenn der
  `observer` das `update_block`-Paket (oder äquivalente Chunk-Daten) an den
  Zielkoordinaten empfangen hat. Reines Senden eines Setblock-Befehls ist
  kein Beweis.
* **Gehaltene Items** (z. B. `music_disc_cat`) gelten erst als bestanden,
  wenn der `observer` das `mob_equipment`-Paket des Akteur-Bots empfangen
  hat — mit passender Actor-Kennung, Item-ID und Hand-Slot.
* Der Beweis liegt als `ops/runtime/latest-observer-proof.json` (oder via
  `--out` an anderer Stelle) vor und wird von
  `node ops/qa/observer-proof.mjs` erzeugt.

## 2. Mathematischer 3D-ViewPort- und Voxel-Raycast-Check

Der `observer` prüft aus seiner Augenposition `eye = position + (0, 1.62, 0)`
und seiner Blickrichtung (`yaw`, `pitch`):

* Sichtfeld: **±45° horizontal**, **±35° vertikal**, Distanz **3–32 Blöcke**
  (jeweils inklusive Grenzen).
* Bühne, Jukebox und alle Show-Bots müssen vollständig darin liegen — bei
  Bots explizit auf **Fußhöhe (`y`), Rumpfhöhe (`y + 0.9`) und Kopfhöhe
  (`y + 1.6`)**.
* Auf der 3D-Sichtlinie darf **kein undurchsichtiger Block** liegen
  (`!air`, `!glass`, inkl. `glass_pane`, `stained_glass`, `tinted_glass`).
  Der Zielblock selbst zählt nicht als Hindernis. Der Check ist ein
  Voxel-Raycast mit 0,25-Block-Schrittweite
  (`ops/qa/world-invariants.mjs` → `voxelRaycast`).
* „Röntgenblick" ist nicht erlaubt: Ein Bot hinter einer Wand ist auch dann
  nicht sichtbar, wenn seine Koordinaten im ViewPort lägen.

## 3. Produktions-Invarianten und Regie-Intention

Defaults bei Umsetzung **und** Prüfung:

| Invariante | Default | Ausnahme nur mit `intent.exceptions` |
| --- | --- | --- |
| Begehbare Wege | Empfang → Zuschauerbereich → Bühne ohne blockierende Blöcke auf Fuß-/Körperhöhe | `blocked_path` |
| Mindest-Raumhöhe | ≥ 2,5 Blöcke über jedem begehbaren Weg (Pre-Build-Layout-Check) | `low_ceiling` |
| Fortbewegung | Gehen statt Teleportieren | `teleport` |
| Szenenstabilität | Kein Reconnect während einer laufenden Szene | `reconnect_in_scene` |
| Tanzfläche | Tänzer bleiben während des Tanzes in der Bühnen-Bounding-Box | `exit_stage` (nur Abgang) |
| Präsentation/Verbeugung | Blickrichtung zum Publikum | `face_away` |
| Sichtbarkeit | `visible=true`, Oberflächenlage | `invisible` |

Der Regieplan wird als `intent`-Objekt im Proof abgelegt; jede Ausnahme
braucht einen kurzen `reason`-Eintrag. Ohne `intent` gelten alle Defaults.

## 4. Stufen und Kontingent

1. **Stufe 1 (kostenlos, immer):** `update_block`, `mob_equipment`,
   Pre-Build-Layout, 3D-ViewPort/Raycast, `WORLD_INVARIANTS.md` — reine
   Skripte / Kilo Auto Free (`kilo1`, `kilo3`, `kilo4`), **0 OpenRouter-Calls**.
   Ca. 95 % aller QA-Prüfungen.
2. **Stufe 2 (optional, letztes Siegel):** Vision-Screenshot-Check
   (z. B. `google/gemma-4-31b-it:free` oder
   `nvidia/nemotron-3.5-super-vl:free`). Nur auslösen, wenn Stufe 1 komplett
   grün ist. Hart gedeckelt in `ops/kilo-handler/vision-budget.mjs`:
   max. **1 Call pro fertigem Meilenstein**, max. **3 Calls pro Tag**.
   Bei Erreichen wird nicht blockiert, sondern gemeldet:
   „Stufe 1 grün, Vision-Call wegen Tagesbudget übersprungen (Screenshots
   abgelegt)".

Die OpenRouter-Modellkette von Kilo2 (T-199 – OpenRouter-Modellausfall: Preset prüfen und freigeben) bleibt unverändert.

## 5. Befehle

```bash
node ops/kilo-handler/vision-budget.mjs status
node ops/qa/observer-proof.mjs --port 19133 --spec <spec.json> --out ops/runtime/latest-observer-proof.json --duration 20000
npm run verify:show          # hartes Gate: Proof muss existieren und gruen sein
node tests/world-invariants.test.js   # Logik-Selbsttest, keine Calls
```
```

## ops/SPEC_MAPPING.md

```markdown
# SPEC_MAPPING.md — Vollständiges Mapping der Systemspezifikation gegen Ist-Stand

Stand: 2026-11-22 (Mastermind/Resident-Agent). Zweck: verhindert blindes Raten und doppelte Suche nach Bausteinen. Basisdokumente: `AGENTS.md`, `ops/PRAESENTATION.md`, `ops/WORLD_INVARIANTS.md`, `ops/BOARD.md`, `ops/QUESTIONS.md`, `ops/capabilities.json`, Code unter `app/`, `ops/qa/`, `ops/scripts/`, Tests unter `tests/`.

## 1) Port-/Protokoll- und Betriebsinvarianten (Soll)

- **19132 (Live, tabu):** `transport=nethernet`, `server-portv6=19142`, `enable-lan-visibility=true`, exklusiver Inhaber von UDP 7551. Niemals anfassen für Show/E2E. (AGENTS.md §2, PRAESENTATION.md, WORLD_INVARIANTS.md §0)
- **19133 (Show/Präsentation):** lokaler Show-Port mit `transport=nethernet`, `server-portv6=19143`, `enable-lan-visibility=false`, eigener UDP-Port `19133`, direkter Login `127.0.0.1:19133`, ohne UDP 7551. (T-227 vom 23.11.2026 in `WORLD_INVARIANTS.md` §0 und `ops/SYSTEM_SSOT.json`; ersetzt Q-T216-03.)
- **19134 (E2E):** lokaler Testport, Analoges zu 19133 (`transport=nethernet`, UDP `19144`/`19134`, LAN aus). (AGENTS.md §2, WORLD_INVARIANTS.md §0)
- **Betrieb:** Showcases nicht blind vorab starten; Präsentation per definiertem Trigger (Chat `!show`/`!tanzshow` laut PRAESENTATION) bzw. per regulärem Auftraggeber-Baustein `npm run show:accept` (AGENTS.md §11).

**Ist (Code/Config/Skripte):**
- `ops/PRAESENTATION.md`, `ops/scripts/setup-presentation-server.ps1`, `ops/scripts/start-presentation.ps1`, `ops/config.presentation.yaml` vorhanden.
- `ops/qa/show-server.mjs`, `ops/e2e/*` vorhanden (Discovery/Connect-Checks referenziert).
- Welt-Invarianten-Prüfung: `ops/qa/world-invariants.mjs`, `ops/qa/observer-proof.mjs`, `npm run verify:show` in `ops/scripts/verify.mjs`/Tests.

**Gaps / Risiken:**
- ~~Drift AGENTS.md §2 vs WORLD_INVARIANTS.md §0~~ **aufgelöst:** Chris' T-227 (23.11.2026) ersetzt Q-T216-03; alle drei Ports = NetherNet, nur 19132 mit LAN `true` und UDP 7551. `ops/SYSTEM_SSOT.json`, `ops/config.presentation.yaml`, `setup-presentation-server.ps1`, `setup-e2e-server.ps1`, `TRANSPORT_RULES`, `observer-proof.mjs` und `tests/tanzshow-plan.test.js` sind darauf abgeglichen. Start-Check `check-e2e-server.mjs` weist `TRANSPORT TYPE ERROR` explizit im BDS-Log zurück.
- Kein zentraler, maschinenlesbarer „Runtime-Truth“-Check, der die laufende `server.properties`/tatsächliche Bindings gegen die Soll-Matrix prüft (teilweise in Skripten vorhanden, aber nicht als einheitlicher Capability-Eintrag dokumentiert).

## 2) Show-/Tanzshow-Fluss T-216 (Soll)

- Vollständige Regie auf 19133: Kulisse/Bühne/Jukebox/Beleuchtung, 4 Crew-Bots, Choreografie, Verbeugung, geordneter Abgang; Freigabe erst bei grünem `observer`-Proof **und** grünem 3D-ViewPort-Check. (T-216 Task, PRAESENTATION, WORLD_INVARIANTS)
- 4 Gate-Punkte (WORLD_INVARIANTS/AGENTS §10.2 + Q-T216-04): (1) observer + alle 4 Bots bei y≈69 nach korrekter Chunk/Spawn-Init, keine Limbo y≈32769; (2) echte `update_block`/`mob_equipment` Pakete; (3) 3D-ViewPort/Raycast mit Bühne/Crew grün; (4) korrekte Server-Identität/Port (ShowWorld/erwarteter Kontext).
- Trigger: Chat `!show`/`!tanzshow` (PRAESENTATION) als System-Trigger; Abnahme programmatisch über `POST /api/show/run` bzw. `npm run show:accept` (AGENTS.md §11, kein manueller Chris-Klick). Bot-Antwort definiert.

**Ist:**
- Regieplan: `ops/qa/show-plan.json`, Spec: `ops/qa/show-spec.tanzshow.json`, Builder/Tests: `ops/qa/show-spec.builder.mjs`, `tests/tanzshow-plan.test.js`.
- Ausführung: `app/backend/show/ShowDirector.js`, `app/backend/show/show-world.js`; Anbindung in `app/backend/index.js` (lädt Plan, `onShowCommand` triggert `showDirector.run()` auf Port 19133).
- Bot-Stack: `app/backend/bots/BotManager.js`, `app/backend/bedrock/BedrockConnector.js` (mit Q-T216-04-Fixes in Worktree-Stand: `request_chunk_radius`, `set_local_player_as_initialized` für Bots; observer-proof ebenfalls gepatcht).
- Präsentations-Start: `ops/scripts/start-presentation.ps1`, Config `ops/config.presentation.yaml`.

**Ist (Stand Abschluss T-216):**
- **Regulärer, programmatischer Auftraggeber-Baustein vorhanden:** `ops/scripts/run-show-acceptance.mjs` führt die vollständige User-Journey autonom aus — Backend-/Crew-Preflight (Reconnect getrennter Bots über `/api/control`), spawn-aware Observer-Platzierung (`tp observer …` erst nach `Player Spawned`, Erfolg im BDS-Log bestätigt), wiederverholbarer Bühnen-/Requisiten-Reset (127 Blöcke + hotbar 0 → air), Show-Trigger per `POST /api/show/run`, observer-proof mit voller Vorlaufzeit, Auswertung der 4 Gates und optional `verify:show`. Kein menschlicher Chat-Klick nötig.
- Programmatischer Trigger-Schnittstelle: `POST /api/show/run` in `app/backend/web/WebServer.js` (lokaler Admin-Endpoint, keine neue Abhängigkeit, kein Safety-/Auth-Eingriff).
- `mob_equipment`-Beweis: `app/backend/bedrock/BedrockConnector.js::holdItem()` sendet nach Konsolen-`replaceitem` zusätzlich eine `mob_equipment`-Handauswahl (Slot 0), damit BDS das Paket an den observer rebroadcastet (sonst ändert `replaceitem` nur das Inventar, ohne Zuschauer-Paket).

**Gaps / Risiken:**
- Wiederholbarkeit (22.11.2026): Reset-Wartezeit im Auftraggeber von 2500 auf 8000 ms erhöht (`run-show-acceptance.mjs`). Bei 2500 ms konnte ein verspätetes Reset-`air`-Paket das Quartz-`update_block` der Kulisse (10,71,6) im Proof überschreiben und einen zweiten, korrekt laufenden Durchgang fälschlich ROT melden. Mit 8000 ms: `ALLE GATES GRÜN`, `openrouter_calls=0`, `target_port=19133`.
- Der Auftraggeber setzt `verify:show` nur optional (`--skip-verify` für schnelle Diagnose); im Regelbetrieb/Abnahme ist `verify:show` zusätzlich direkt zu fahren (siehe Abschnitt 9). Sein Gesamturteil ist `result.ok && verifyExit===0`: Der globale Suite-Exit ist derzeit durch den Vault-Hygiene-Blocker (Abschnitt 3) rot, während `OBSERVER_GATE GREEN` und alle vier Show-Gates grün sind. Kein Show-Gate, kein Abschwächen der Prüfung.
- Kein zentraler „Runtime-Truth"-Abgleich der laufenden `server.properties` gegen die Soll-Matrix (nur implizit über Server-Identität im observer-proof).

## 3) QA / Observer / Invarianten (Soll)

- Separater `observer`-Client, Paketbeweise (`update_block`, `mob_equipment`), 3D-ViewPort/Raycast, keine Spec-Seeding-Beweise, harte Gates via `verify:show` / `WORLD_INVARIANTS.md`. (AGENTS.md §10.2, WORLD_INVARIANTS.md)

**Ist:**
- `ops/qa/observer-proof.mjs`, `ops/qa/world-invariants.mjs`, Tests `tests/world-invariants.test.js`, Verify-Integration in `ops/scripts/verify.mjs` (`verify:show`).

**Ist (Stand Abschluss T-216):**
- `ops/scripts/run-show-acceptance.mjs` triggert den Proof reproduzierbar und wertet die 4 Gates aus; `POST /api/show/run` + BDS-Konsolenkanal (`/api/server/command`) sind die regulären Bausteine.

**Gaps:**
- `npm run verify` global enthält einen vault-hygiene-Check, der derzeit durch eine vom Kilo-Handler automatisch angelegte, unverfolgte Vault-Frage (`fragen/Fragen-T-199-modellausfall.md`, `status: offen`) blockiert wird (`pushed:false`). Das ist ein betriebsweiter, vorbestehender Zustand außerhalb des T-216-Vault-Schreibbereichs und kein Show-Gate; die Show-spezifischen Gates (`verify:show` → `OBSERVER_GATE GREEN`, `world-invariants`, `tanzshow-plan`) sind grün.

## 4) capabilities.json (Ist)

Enthält verifizierte Low-Level-Funktionen (Bedrock sendChat/sendEmote/useBlock/setBlock/walkTo/holdItem, Interaction, Pathfinder, WorldGrid, WorldMemory) und seit 22.11.2026 zusätzlich die registrierte Show-Capability `show.run` (`app/backend/show/ShowDirector.js::run`, Zeile 195). Der zugehörige Auftraggeber/Orchestrator ist über den stabilen Aufrufpfad `npm run show:accept` (`ops/scripts/run-show-acceptance.mjs`) als regulärer Plattform-Baustein verankert.

**Weitere Show-/Tanzshow-Bausteine (implementiert, als eigene Capability-Einträge noch Backlog):**

| Soll-Capability | Modul / Datei | Beleg | Registry-Stand |
| --- | --- | --- | --- |
| `show.run` (programmatischer Trigger) | `app/backend/show/ShowDirector.js::run` → `POST /api/show/run` | `ops/runtime/latest-observer-proof.json`, `npm run verify:show` | ✅ registriert |
| `stage.reset` (BDS-Konsole) | `app/backend/server/ServerManager.js::sendConsoleCommand`, genutzt in `run-show-acceptance.mjs` | BDS-Log `set_build_done`, `Replaced slot … with Air` | Backlog |
| `observer.proof` (Paketbeweis + ViewPort) | `ops/qa/observer-proof.mjs`, `ops/qa/world-invariants.mjs` | `ops/runtime/latest-observer-proof.json`, `npm run verify:show` | Backlog |
| `qa.acceptance` (Auftraggeber/Orchestrator) | `ops/scripts/run-show-acceptance.mjs`, `npm run show:accept` | 4 Gate-Punkte grün | Backlog (stabiler Aufrufpfad vorhanden) |
| `bedrock.holdItemBroadcast` | `app/backend/bedrock/BedrockConnector.js::holdItem()` (mob_equipment-Handauswahl) | `latest-observer-proof.json`: `mob_equipment` für `Crew-Jukebox`/`minecraft:music_disc_cat` | Backlog (`bedrock.holdItem` registriert) |

Weitere nicht registrierte Betriebsbausteine: Präsentations-Startskripte, `verify:show` als eigene Capability, Prozess-Supervision (run-watch).

## 5) Abgeleitete Maßnahmen (Status)

1. **Regel in AGENTS.md verankert** (§11, Chris-Vorgabe 22.11.2026): End-to-End-Produktdenken & simulierter Auftraggeber, vollständige Journey, keine Wegwerf-Skripte, Chris nie als manueller Klicker außer den 4 harten Toren. ✅
2. **Regulärer System-Baustein gebaut:** `ops/scripts/run-show-acceptance.mjs` als wiederverwendbarer Auftraggeber/Orchestrator (Preflight, Reset, Trigger via `POST /api/show/run`, observer-proof, 4-Gate-Auswertung, optional `verify:show`). ✅
3. **T-216** mit diesem Baustein bis zu den 4 grünen Gate-Punkten durchgezogen; `latest-observer-proof.json` `result.ok=true`, `verify:show` Observer-Gate grün. ✅
4. **Drift 19133/19134 Transport** gemäß T-227 aufgelöst (NetherNet überall, LAN aus und eigene UDP-Ports auf 19133/19134, UDP 7551 exklusiv 19132). ✅
5. **Registrierung (22.11.2026):** `show.run` als Capability in `ops/capabilities.json` registriert, `npm run show:accept` als stabiler Aufrufpfad in `package.json` verankert; §11 task-agnostisch formuliert und in Task-Vorlage + Intake-Generator übernommen. ✅ — Rest-Backlog: `stage.reset`, `observer.proof`, `qa.acceptance`, `bedrock.holdItemBroadcast` als eigene Einträge (Abschnitt 4); Vault-Hygiene-Blocker durch zuständigen Worker auflösen (Abschnitt 3).

## 5a) T-226 (Werkstatt-Feinschliff) — Soll/Ist nach Baustein 1

| Soll aus `ops/tasks/T-226.md` | Umsetzung | Beleg |
| --- | --- | --- |
| Drei Servernamen in SSOT, Registry und Manager; 19132 läuft unter bestehender PID | `ops/SYSTEM_SSOT.json` (`name`, `managerRolle`, `bdsManager`), `ops/runtime/pids.json` (echte PPIDs 15672/15052), `ops/runtime/bds-manager-registry.mjs` | `node ops/runtime/bds-manager-registry.mjs`: `VoxelCrew:Oberwelt` PID 18356 attachiert, `protected: true`, kein Start/Stopp |
| 19132 kein Neustart/Verbinden/Beenden, keine Weltdaten-Berührung | `ServerManager.attachExternal()` ohne stdin; `sendConsoleCommand()` und `stop()` gesperrt | `attachExternal` setzt `external: true`; `stop()`/Kommando liefern klare Schutzmeldung |
| 19133 NetherNet/Show, 19134 NetherNet/E2E, LAN aus (heute, nach T-227) | SSOT + `ops/scripts/setup-e2e-server.ps1` schreibt `server.properties` mit `transport=nethernet`, `19144`, `enable-lan-visibility=false` | Registry zeigt `transport` und `enableLanVisibility` pro Server; Start-Check ohne `TRANSPORT TYPE ERROR` |
| Wissensanalyse wählt FW-Base oder `level-seed`, erkundet vorab, atomarer Master-Reset | `ops/wissenanalyse/pipeline.mjs`: `chooseWorld`, `exploreWorld`, `masterReset` (LOCK weg, `.stage`, Origin, `world.reset.completed`, 8000 ms) | `tests/wissenanalyse-pipeline.test.js` 11/11 grün |
| Fünf Härtungen als BP-000, Freigabe `manual` | `ops/blueprints/BP-000.json` via `createBlueprint()` angelegt; `definitionOfReady`, `atomicDiffBudget`, `contractFirst`, `preflight`, `appendLesson` als aufrufbare Funktionen | Registry `BP-000 … BP-006`; `ops/capabilities.json` Einträge `qa.definitionOfReady`, `bds.manager`, `world.chooseAndReset`, `spectator.liveMode` |

**Gaps (bewusst offen, keine Freigabe):** `19134` wird erst nach Ausführen des Setup-Skripts mit FW-Base befüllt; die visuelle Spectator-Journey und die UI-Nachschliffe folgen in den nächsten Bausteinen. BP-000 bleibt `status: entwurf` und `freigabe: manual`.

## 5b) T-227 — Regie-Leitplanken A–D (Soll / Ist / Backlog)

| Leitplanke (Soll) | Ist (verankert) | Backlog / Umsetzung |
| --- | --- | --- |
| **A Capability- & Gap-Planner**: vor jedem Vorhaben `ops/capabilities.json` prüfen, fehlende Grundfähigkeiten als wiederverwendbare Bausteine herausschneiden | `ops/SYSTEM_SSOT.json` → `regieLeitplanken.A_capabilityGapPlanner`; `AGENTS.md` §12 A; Handbuch 05 §A; `ops/capabilities.json` + `ops/SPEC_MAPPING.md` (dieses Dokument) | Capability-Einträge für Planner/Scoring erst bei Umsetzung; Mapping hier laufend gepflegt |
| **B Adaptive Scene Director**: 32×32-Sektoren, sanfte Topografie-Anpassung, Raycast-Korrektur von Verdeckung/Loge | SSOT `regieLeitplanken.B_adaptiveSceneDirector`; AGENTS.md §12 B; Handbuch 05 §B; `ops/qa/world-invariants.mjs` (`voxelRaycast`, Augenhöhe 1,62) | Regieplan-Autopass für Podest/Stufen und Loge; Meilenstein B in `BP-007` |
| **C Dreifache Realitäts-Kopplung + Multi-Seed-Gate N ≥ 3**: Blockdaten + freier Raycast + bequeme Erreichbarkeit, 3 Seeds auf 19134, automatische Platten-Bereinigung | SSOT `regieLeitplanken.C_dreifacheRealitaetsKopplung` und `welten.masterReset`; AGENTS.md §12 C; Handbuch 05 §C; `ops/wissenanalyse/pipeline.mjs` (`masterReset`) | Seed-Orchestrator und 3-Fach-Beweis als eigener Baustein; Meilenstein C in `BP-007` |
| **D User-Empathy-Simulator + BP↔Client-Symbiose**: unprivilegierter Spieler, FOV 70°, ruhiges Tempo, `@minecraft/server`/`@minecraft/server-ui`/`SWARM_BOT` | SSOT `regieLeitplanken.D_userEmpathy` und `akteure`; AGENTS.md §12 D; Handbuch 05 §D; `ops/lib/actor-profile-guard.cjs` (Isolation) | Simulator und UI-/Effect-Brücke; `SWARM_BOT` bleibt Live-Profil, auf 19134 über `SHOW_ACTOR`/`OBSERVER`; Meilenstein D in `BP-007` |

**Soll/Ist/Backlog-Fazit:** Die vier Leitplanken sind als Verbindlichkeits- und Beweisregeln vollständig gemappt (Soll = Ist Dokumentation/SSOT); die ausführenden Bausteine sind bewusst **Backlog** und liegen als Meilensteine im Bauplan `BP-007 (Autonome Welt-Anpassung, Multi-Seed-Regie & User-Empathy-Prüfung)` mit `freigabe: manual`. Kein Vorgriff auf Freigabe oder Worker-Dispatch.

## 6) Offene Entscheidungen (kurz)

- ~~Welche programmatische Trigger-Schnittstelle für ShowDirector soll der Auftraggeber nutzen?~~ — erledigt: lokaler Admin-Endpoint `POST /api/show/run` (ohne neue Abhängigkeit, ohne Safety-/Auth-Eingriff).
- ~~Genaue Soll-Transportmatrix für 19133/19134 finalisieren~~ — erledigt durch T-227 (NetherNet überall, LAN aus auf 19133/19134, UDP 7551 nur 19132).

## 7) Vollständiges Vault-/Inbox-Spezifikationsinventar (Soll-Anteile)

Pfadbasis: `vault/`. Quelle: read-only Inventur aller Inbox-Originale, Vault-Spezifikationen und Handlungsanweisungen (Chris-Erinnerung: „alles, was in der Inbox weiterverarbeitet wurde, auch"). Große Referenzwerke sind über ihre Quellzuordnung in `Inbox-Auswertung-2026-11-19.md` und Volltextsuche erfasst. Relevanz: **direkt** = Soll-Anforderung für T-216-Show/QA; **indirekt** = Plattform-Kontext; **keine** = anderer Fachbereich.

| Datei (relativ zu `vault/`) | Art | Soll-Anforderung (Kurzfassung) | Relevanz |
| --- | --- | --- | --- |
| `handlungen/bearbeitet/Handlung-T-216-grosse-crew-bot-tanzshow.md` | Handlung | Show erst nach grünem Stufe-1-Gate; kein Reconnect/Teleport in Szenen; 4 Gate-Punkte (observer-Proof, sichtbare Bots, `update_block`/`mob_equipment`-Paketbeweis, 3D-ViewPort); `verify:show`, `result.ok=true`, `openrouter_calls=0`. | direkt |
| `handlungen/bearbeitet/Handlung-T-215-verbindlicher-qa-welt-standard.md` | Handlung | Stufe-1 (Observer-Paketbeweis + ViewPort) kostenlos und hartes Pflicht-Gate; Vision nur optionales Siegel danach (max. 1/Meilenstein, 3/Tag), keine Selbstbenotung. | direkt |
| `konzept/2026-11-21 tanzshow-regie-spezifikation-komplett.md` | Spezifikation | Vollumfang: Kulisse per `/fill`/`/setblock` auf 19133, Anteroom mit beleuchtetem Weg, 4 Bots aus 4 Richtungen, Jukebox, `music_disc_cat` sichtbar in der Hand (`mob_equipment`), Choreografie, Verbeugung, geordneter Abgang; Verifikation per `testforblock`, Client-Listener, Vision/Screenshot. | direkt |
| `konzept/2026-11-21 tanzshow-familien-drehbuch.md` | Beispiel | 3–4 Bots, Showmaster, 3 Akte (Warm-up, synchrones Tanzen, Finale mit `zugabe`); Regie-Vorlage für Showcase A (T-206). | direkt |
| `wissen/szenarien/Theater-Show.md` | Spezifikation | Wiederholbare Vorführung: 4 Bots, Chat-Signal, Item zeigen, Jukebox, 1–2 Min Choreografie, Abgang, zweiter Lauf ohne Serverneustart. | direkt |
| `wissen/szenarien/Der Spaß und der Tanz und die Motivation.md` | Beispiel | Ursprungsidee: menschliche Spieler betreten Saal, Chat-Signal, 4 Bots (3 Formation, 4. zeigt Schallplatte + Jukebox), 1–2 Min Hüpfen/Emotes, wiederholbar. | direkt |
| `wissen/minecraft/Daten/kuratiert/notizen/showcases-als-meilensteine.md` | Referenz | Showcases intern E2E-testen, dann Chris präsentieren; nur `19133`, `online-mode=false`, sauberer Stop, Nachweis in `bds.log` + `bot-proof.jsonl`; kein Screengrab ohne Freigabe; Abnahme: Chat startet, 4 Bots, Jukebox hörbar, 2. Lauf ohne Neustart. | direkt |
| `wissen/minecraft/Daten/kuratiert/notizen/e2e-belege-bds-und-bot.md` | Referenz | Isolierte BDS-Kopie, Port 19133, `enable-lan-visibility=false`, `bds.log` + `bot-proof.jsonl` + `summary.json`; clientseitige Effekte (Hotbar/Ausrüsten) sind nicht belegt → Observer-Paketbeweis nötig. | direkt |
| `wissen/Bedrock-26.60-NetherNet.md` | Referenz | Ab 26.60 NetherNet (UDP-Discovery 7551); Bot `transport: nethernet`; die zeitweise RakNet-Ausnahme Q-T216-03 ist durch T-227 ersetzt (alle Ports nethernet). | direkt |
| `konzept/bedrock_26_65_ki_architektur_spezifikation.md` | Spezifikation | NetherNet als Transportlayer; Bot-Clients/Bridges mit `serveridentity`-Verifikation; bevorzugt serverseitige Simulated Players. | direkt |
| `konzept/ki-architektur-handlungsleitfaden-bedrock-26-65.md` | Handlung | Modus A (Script API/BDS) vs. Modus B (`bedrock-protocol`, reiner Client) bewusst wählen; DRY, State-Hoheit, Vanilla-Mechaniken. | direkt |
| `konzept/2026-11-21 e2e-live-spectator.md` | Spezifikation | Menschlicher Spectator darf joinen, ohne Assertions zu brechen; automatischer `/gamemode spectator` + Teleport in Sichtweite (Q-T210). | direkt |
| `konzept/2026-11-21 live-zuschauer-modus.md` | Spezifikation | Feste beleuchtete Tribüne mit freiem Blick auf die Bühne; Join im Spectator-Modus ohne Störung; Abnahme-Maske mit Port/Start-Trigger (Q-T211). | direkt |
| `konzept/10_referenz-showcase-regie-und-review-schema.md` | Spezifikation | `showcase_script.json` (stage/cast/acts, Kamera-Cues) vorab auf E2E-BDS trocken testen; Feedback in `review_submissions`. | direkt |
| `konzept/00_README_KONZEPT_INDEX.md` | Spezifikation | „Autonomous E2E before Human Acceptance"; 5-stufiger Regie-Prozess, 3-Akt-Story, Bühne ≤32×32, nur Vanilla-Elemente der Zielversion. | direkt |
| `konzept/01_architektur-briefing-multi-bot-bedrock-v14.md` | Spezifikation | Vier Betriebsmodi (PURE_CLIENT/PRODUCTION_BDS/E2E_TEST_SERVER/DEMO_SHOWCASE_SERVER), strikte `world_context_id`-Isolation, 3D-Reality-Check, 50-ms-HFSM. | direkt |
| `konzept/06_referenz-fast-voxel-raycaster-tof.md` | Referenz | Zero-Allocation Raycaster, Augenhöhe als Ausgangspunkt, Sichtlinie pro Pixel bis `maxDist` (Grundlage ViewPort-Check, nicht identisch mit Gate-Parametern). | direkt |
| `konzept/2026-11-19 anlage-03-revision-v1-chris-protokoll-matrix.md` | Spezifikation | 5-Phasen-Vertrag `PRE_CHECK → TIMING → APPROACH → EXECUTE (50 ms) → VERIFY_AND_COMMIT`; Erfolg erst nach echter Server-Quittung (`UpdateBlock`, `MobEquipment`). | direkt |
| `konzept/BDS-ServerManager-Systementwurf.md` | Spezifikation | Rollen `prod-main`/`pres-demo`/`e2e-test`/`ana-opt`, Portkollisionsprüfung, zentraler Vorlagen-Pool, atomarer Master-Reset, Schutzschloss, automatisierte Invarianten. | direkt |
| `konzept/Referenzanlage-A-Prozess-und-Ports.md` | Referenz | `PortGuard` prüft IPv4+IPv6; `BdsWindowsProcess` steuert via stdin, harter Kill nach Timeout. | direkt |
| `konzept/Referenzanlage-B-Welt-Vorlagen-und-Reset.md` | Referenz | Template-Deployment über Staging, `db/LOCK` entfernen, atomares `rename`, bitgenauer Master-Reset. | direkt |
| `konzept/Referenzanlage-C-Packs-und-Welt-Monitor.md` | Referenz | Behavior-Packs pro Welt aktivieren, Script-API-Module erlauben, `server.properties` verlustfrei aktualisieren. | direkt |
| `konzept/Referenzanlage-E-Welt-Gedaechtnis-und-Event-Bus.md` | Referenz | Bei Reset Gedächtnis leeren und `world.reset.completed` publizieren, bevor Bots joinen; Log-Regex → `[BOT_EVENT]`-Marker. | direkt |
| `konzept/systemspezifikationen.md` | Spezifikation | Deterministische Weltgenerierung, Koordinaten-/Chunk-Mapping, vier Bot-Anbindungsmuster, Hybrid Echtzeit/Offline-Planung. | indirekt |
| `konzept/02_ui-ux-architektur-briefing-leitstelle-v2.md` | Spezifikation | Zielgruppe ab 10 Jahren; zentrale 2D-Karte; „Demonstrations-Server-Regie" als Fernbedienung für Showcases und Abnahme-Masken. | indirekt |
| `konzept/2026-11-20 vanilla-portale-und-funktionale-aufbauten-als-block-muster.md`, `…/nether_portal_spezifikation_bedrock_26_65-v2.md`, `…/redstone-spezifikation-bedrock-26-65-v3.md`, `…/bedrock-26-65-block-architektur-spezifikation-v2.md`, `…/bedrock-26-65-dimensionen-spezifikation-v3.md` | Spezifikation | Fachspezifikationen Portal/Redstone/Blöcke/Dimensionen (T-204 u. a.); `minecraft:observer` hier Redstone-Block, **nicht** der QA-Observer-Bot. | keine |
| `konzept/2026-11-19 feingranulare-handlungsausfuehrung.md`, `…/block-muster-und-2-5d-weltkarte.md`, `…/bot-relay-und-copilot-modus.md`, `…/anlage-04…09` | Spezifikation/Referenz | VoxelCrew/Frontier-Erweiterungen, Flugschreiber, Co-Pilot-Stufen; Plattform-Kontext für Bots/QA, nicht Show-spezifisch. | indirekt |
| `konzept/2026-11-21 dauerbetrieb-absturzschutz.md`, `…/multi-worker-parallelbetrieb-kilo1-bis-4.md` | Spezifikation | Circuit Breaker, Work-Stealing, RAM-Notbremse; Betriebsrahmen (§10 AGENTS),_show-Server bleibt tabu für Worker. | indirekt |
| `konzept/2026-11-20 2026-11-21-ops-center-gui-redesign-md.md`, `…/ops-center-live-feed-konsole.md`, `…/vault-sync-button-im-cockpit.md` | Spezifikation | Ops-Center-UI/Feed/Vault-Sync; `npm run verify` grün halten (UI-Änderungen → `npm run test:browser`). | keine/indirekt |
| `entscheidungen/ADR-005-Mehrbot-Betrieb.md` | Spezifikation | `bots:`-Liste mit je eigenem SafetyGuard/BotController/BedrockConnector, geteiltes WorldMemory; `max-players` muss Bot-Anzahl decken. | direkt |

## 8) Abschluss-Mapping des Neuaufbaus (23.11.2026)

| Soll-Baustein | Ist | Beleg |
| --- | --- | --- |
| SSOT statt widersprüchlicher Runner-/Worker-Dateien | `ops/SYSTEM_SSOT.json` generiert `runner.json`, `runner.feldbuch.json`, `workers.json` | `node ops/scripts/system-config-sync.mjs`, `ops/SYSTEM_SSOT.sync.json`, `npm run config:sync` |
| Harte Akteurs-Isolation | `ops/lib/actor-profile-guard.cjs` vor jedem Verbindungsaufbau; 19132 nur `LIVE`, 19133/19134 nur `SHOW_ACTOR`/`OBSERVER` | `tests/rebuild-process-guard.test.mjs`, `tests/bot-manager.test.js` |
| Echtes Monitoring | `machine-monitor.mjs` (PID-Registry, 15-s-Hausmeister), `feldbook-telemetry.mjs` (Git-Transport, 30-s-Heartbeat, Cache) | `node ops/runtime/machine-monitor.mjs snapshot`, `node ops/runtime/feldbook-telemetry.mjs read` |
| Vault-Haushalt ohne Chris-Tore | `vault-housekeeping.mjs` mit gemeinsamem Git-Write-Lock, `pull --rebase`, `push`, 30-s-Timeout | `ops/monitor/vault-housekeeping.json`, `ALLOWED_VAULT_SYNC_DIRS` |
| Wissensanalyse + Baupläne | `ops/wissenanalyse/initial-2026-11-23.*`, `ops/blueprints/pipeline.mjs`, 6 initiale Baupläne, Freigabe `manual` | `ops/blueprints/registry.json`, `npm run blueprints` |
| Vierteiliges Vault-Handbuch | `handbuch/01…04`, automatische Aktualisierung bei Bauplan-Abschluss | `ops/wissenanalyse/handbuch-sync.mjs` |
| Werkbank ohne Login | `ops/werkbank/server.mjs`, SSE < 200 ms, 6 Bereiche, Freigabe-/Not-Aus-Schalter | `tests/werkbank-browser-e2e.test.js`, `npm run werkbank:e2e` |
| Prozessschutz | 30-s-Timeout, `taskkill /F /T /PID`, RAM 80/90, Verify/E2E-Lock, kein 429-Cooldown, Quarantäne nach 2 Fehlstarts | `ops/runtime/process-control.mjs`, `tests/rebuild-process-guard.test.mjs` |
| Abschlussprüfung | zentraler Rebuild-Verify inkl. Live-Check | `npm run rebuild:verify`, `ops/runtime/rebuild-verify.json` |

Cache-/Transport-Lücke aus Abschnitt 1 und die Backlog-Einträge aus Abschnitt 4 sind damit als reguläre Bausteine abgebildet; die initialen Baupläne übernehmen die offenen Punkte als geplante Arbeit, ohne Worker-Dispatch auszulösen.
| `wissen/Sicherheitsregeln.md`, `wissen/Befehle-und-Gesten.md`, `wissen/Emotes.md` | Spezifikation/Referenz | 50-Block-Geofence, verbotene Items, Kistenschutz; Chat-Befehle/Gesten/Emote-UUIDs teils live erfassen. | indirekt |
| `wissen/szenarien/README.md`, `wissen/szenarien/Weltvorlagen.md` | Spezifikation | Szenario-Schritt `welt:` auf frischer Kopie (SHA256 vor/nach), `.mcworld`-Vorlagen nicht in Git; `theater.mcworld` für Theater-Show. | direkt/indirekt |
| `handlungen/README.md` | Handlung | Verarbeitung erst bei `status: erledigt` **und** gepushtem Vault; keine Keys/Weltdateien/Screengrabs/echten Konten; Serverstart mit eigenem Freigabe-Task. | direkt |
| `_archiv/2026-11-23-spuelgang/veraltete-projekt-dateien/Inbox-Auswertung-2026-11-19.md` | Referenz | Alle 21 technischen Inbox-Dokumente unter Vorbehalt/nicht validiert, konsolidiert durch T-077; Originale bytegleich in `bearbeitet/`. | indirekt |
| `wissen/minecraft/README.md`, `…/Schema.md` | Spezifikation | Kanonische Wissens-DB als SSOT, Vertrauensstufen, keine geratenen IDs/Härtewerte; vor Item-/Blockarbeit lesen. | keine |

## 8) Inbox-Original → verarbeitetes Artefakt → Status („weiterverarbeitete Inbox", Chris-Erinnerung)

| Inbox-Original (`vault/…`) | Verarbeitetes Artefakt (`vault/…`) | Status / abgeleitete Soll-Anforderung |
| --- | --- | --- |
| `konzept/2026-11-21 tanzshow-regie-spezifikation-komplett.md` | `fragen/bearbeitet/Fragen-T-217-klaerung.md`; `handlungen/bearbeitet/Handlung-T-216-grosse-crew-bot-tanzshow.md` | `beantwortet` (Freigabe „ready", rote Linie Screenshot) / Handlung `erledigt` ohne Abnahme — 4 Gate-Punkte bleiben Bedingung. |
| `konzept/2026-11-21 tanzshow-familien-drehbuch.md` | T-206 (Regie-Skript/Bühnenplan); `fragen/bearbeitet/Fragen-T-206-klaerung.md` | `beantwortet` / FREIGEGEBEN; 3-Akt-Regie mit Showmaster und Requisiten. |
| `konzept/00_README_KONZEPT_INDEX.md` (+01–10, Parser) | `Fragen-T-206-klaerung.md`; `_archiv/2026-11-23-spuelgang/veraltete-projekt-dateien/Inbox-Auswertung-2026-11-19.md`; `…/showcases-als-meilensteine.md` | `beantwortet` / `draft` / `draft`; Regie-Pipeline, 5-stufiger Ablauf, Meilensteine. |
| `konzept/10_referenz-showcase-regie-und-review-schema.md` | `_archiv/2026-11-23-spuelgang/veraltete-projekt-dateien/Inbox-Auswertung-2026-11-19.md` (T-077) | `draft`, unter Vorbehalt; `showcase_script.json` + `review_submissions`. |
| `konzept/2026-11-21 e2e-live-spectator.md` | `Fragen-T-210-klaerung.md` | `beantwortet`, FREIGEGEBEN (rote Linie Port) → Spectator-Beitritt ohne Assertions-Bruch. |
| `konzept/2026-11-21 live-zuschauer-modus.md` | `Fragen-T-211-klaerung.md` | `beantwortet`, freigegeben → Tribüne + Start-Trigger in Abnahme-Maske. |
| `konzept/BDS-ServerManager-Systementwurf.md` | `Fragen-T-101/108/115/138/167/191-klaerung.md` | alle `beantwortet`; Rollen, Portpaare, Master-Reset, Schutzschloss. |
| `konzept/Referenzanlage-A-Prozess-und-Ports.md` | `Fragen-T-102/109/116/139/168/192-klaerung.md` | alle `beantwortet`; Windows-Prozesssteuerung + UDP-Port-Schutz. |
| `konzept/Referenzanlage-B-Welt-Vorlagen-und-Reset.md` | `Fragen-T-103/110/117/140/169/193-klaerung.md`, `Fragen-T-193-prozessschutz-worktree.md` | alle `beantwortet`; Vorlagen-Pool, atomarer Reset, Worktree-Schutz. |
| `konzept/Referenzanlage-C/D/E/F-*.md` | `Fragen-T-104/105/106/107`, `-111/112/113/114`, `-118/119/120/121`, `-141/142/143/144`, `-170/171/172/173`, `-194/195/196/197/198-klaerung.md` | alle `beantwortet`; Packs/properties, Discovery/Migration, Event-Bus, UI-ViewModels. |
| `konzept/2026-11-19 anlage-01…09 + block-muster + bot-relay + konto-UI + feingranulare + resource-pack + namensgebung + README_PAKET_INDEX` | `Fragen-T-122…137`, `-151…166`, `-175…190-klaerung.md` | alle `beantwortet`; VoxelCrew-/Frontier-Erweiterungen, Anlagen 01–09. |
| `konzept/Flache Welten als Vorlagen.md`, `Kontrollierte … Testumgebungen…`, `Kommunikation mit dem KI Agenten…` | `_archiv/2026-11-23-spuelgang/veraltete-projekt-dateien/Inbox-Auswertung.md` → T-014, T-019; `wissen/szenarien/Weltvorlagen.md`, `wissen/szenarien/README.md` | `draft`; T-014: Welten als `FW-*`, Szenario-Schritt `welt:` braucht exportierte Dateien. |
| `konzept/Editor-App für Blueprints.md`, `Erstellung Minecraft-Wissen…md` | `Inbox-Auswertung.md` → T-015/016/017; `Fragen-T-017-exe-bereitstellung.md` | `beantwortet`; QA-Phasen und Render-Pipeline als offene Entscheidungen markiert. |
| `konzept/Module und Vision Tribes…md` | `fragen/bearbeitet/T-025-Wissensstand zur Sichtung.md`; `…/tribes-features-unter-vorbehalt.md` | `beantwortet` / `extracted`; Korrektur Q-023: Semantic Zones, keine Multiblock-Vorlagen. |
| `konzept/gewünschter Test des Systems so früh wie abbildbar.md` | `…/showcases-als-meilensteine.md` (Meilenstein 2), T-050 | `draft` / `ready`; Structure-Block-Analyse eigenes Showcase. |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/Minecraft_Bedrock_Handbuch.md` | `…/T-069-handbuch-und-rezepte-live-tests.md`; Original nach `bearbeitet/` verschoben | `extracted`, unter Vorbehalt; kuratierte Notiz ist Arbeitsquelle. |
| `konzept/2026-11-20 vanilla-portale-und-funktionale-aufbauten-als-block-muster.md` | `…/vanilla-strukturen-als-block-muster.md` | `extracted`, noch kein E2E-Lauf; Portale als Block-Muster (T-204). |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/Chat in Minecraft.md` | `Fragen-T-037.md` | `beantwortet`; Chat-Befehle je Feature teils öffentlich, teils beschränkt. |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/Weiteren Kilo-Worker Rechner einrichten.md` | `Fragen-T-038.md` | `beantwortet`; zweiter Kilo-Worker. |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/Neue Fähigkeiten durch OpenRouter Modelle.md` | `Fragen-T-039.md` | `beantwortet`; OpenRouter-Modelle/Autonomie (Kette unverändert). |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/2026-11-20 showcase-dankeschoen-oase.md`, `…schatzjaeger-parcours…`, `…vanilla-portale…` | `handlungen/bearbeitet/Handlung-Gate-0-Werkstatt-Readiness.md`; `abnahmen/2026-11-20-Werkstatt-Readiness.md` → T-202/203/204 | `erledigt` / Testlauf `passed`; Quellen nach `bearbeitet/` archiviert. |
| `_archiv/2026-11-23-spuelgang/inbox-entwuerfe/2026-11-21 multi-worker-parallelbetrieb-kilo1-bis-4.md`, `…vault-sync-button…`, `…ops-center-gui-redesign…`, `2026-11-20 testthema.md` | `Fragen-T-214/213/207/150/174-klaerung.md` | alle `beantwortet`; Intake-Freigaben mit roter Linie Port/UI. |

## 9) Vollständige User-Journey T-216 (Auftraggeber-Sicht, Soll → Ist)

1. **Auslöser (kein Mensch tippt):** Agent/CI ruft `npm run show:accept` auf (`ops/scripts/run-show-acceptance.mjs`). → Ist: Baustein existiert als regulärer Plattform-Baustein mit stabilem Aufrufpfad, liest `ops/qa/show-spec.tanzshow.json` (nur Erwartungen, keine Beweise).
2. **Voraussetzungen:** Backend `127.0.0.1:3100` erreichbar; alle 4 Crew-Bots connected (Reconnect getrennter Bots via `/api/control`). → Ist: `ensureCrewConnected()`, `stillMissing=[]`.
3. **Observer platzieren:** observer-Bot joined 19133, wartet auf `Player Spawned: observer`, `tp observer 10 71 16 180 -10`, Erfolg im echten BDS-Log. → Ist: `placeObserver()`, `confirmed=true`, Position `(10.5, 71, 16.5)`, `rotation_acknowledged=true`.
4. **Bühne zurücksetzen:** 127 kanonische Blöcke + Crew-Requisit auf `air`, damit wiederholte Läufe echte Pakete erzeugen. → Ist: `resetStage()`, `ok=128, failed=0`.
5. **Show starten:** `POST /api/show/run` (programmatisch, kein Chat-Klick). → Ist: HTTP 200, `Tanzshow gestartet`.
6. **Regie erleben:** Bühne/Jukebox/Beleuchtung, 4 Bots aus 4 Richtungen, Choreografie, `music_disc_cat` in der Hand, Verbeugung, Abgang. → Ist: ShowDirector-Marker im BDS-Log, `show_end`.
7. **Observer-Beweis:** echter Paketstrom (`update_block`, `mob_equipment`), 3D-ViewPort/Raycast, Server-Identität `ShowWorld`/19133. → Ist: `latest-observer-proof.json` `result.ok=true`, 254 `update_block` + 1 `mob_equipment` (`Crew-Jukebox`/`minecraft:music_disc_cat`), 4 Gates grün.
8. **Hartes Gate + Bericht:** `npm run verify:show` (Observer-Gate grün), `npm run verify -- --out ops/results/T-216.verify.json`, Ergebnisdatei + Board/Log. → Ist: `verify:show` Observer-Gate grün; globaler `verify` durch außerhalb liegenden Vault-Hygiene-Blocker (Abschnitt 3) rot — dokumentiert, kein Show-Gate.

## 10) Widersprüche & verbindliche Auflösung

1. **Port-Rolle 19133:** Vault (älter) = E2E, `AGENTS.md` 21.11. + Q-T216-03 = Präsentation. → **AGENTS.md/Q-T216-03 gewinnt** (19133 Show, 19134 E2E, 19132 Live/NetherNet/7551).
2. **Start-Auslöser:** Vault-Spezifikation verlangt Chris-Chat `!show`; §11 verbietet manuellen Klicker. → Chat-Trigger bleibt als System-Trigger zulässig, Abnahme programmatisch über Auftraggeber; kein „bitte tippen" an Chris.
3. **Vision/Screenshot:** Inbox-Spezifikation verlangt Screenshot-Beleg; T-215/§10.3/Q-T217 rote Linie. → Stufe-1-Paketbeweis ist Pflicht; Vision optionales Siegel mit Budget, ohne separate Freigabe kein Screengrab.
4. **Status `erledigt` vs. Checkliste:** `Handlung-T-216` `erledigt` ohne Abnahme (Watcher-Freigabe), Checkliste offen. → `erledigt` ist kein Testattest; 4 Gate-Punkte bleiben Bedingung.
5. **Besetzung 3–4 vs. 4:** Familien-Drehbuch/Theater-Show älter (3–4/3 Formation). → neuere Vorgabe exakt 4 Bots aus 4 Richtungen gewinnt.
6. **Begriff `observer`:** In Telemetrie-Schemas `observer_type` (Weltgedächtnis-Quelle) ≠ QA-Observer-Bot. → nicht gleichsetzen.
7. **ViewPort-Parameter:** §10.2 (Augenhöhe `y+1.62`, ±45°/±35°, 3–32 Blöcke) ist Gate; 80°-ToF-Raycaster ist Wahrnehmungs-Sensor. → nicht als Ersatz für Gate-Check verwenden.

## 11) T-228 — Lückenloses Soll/Ist/Backlog-Mapping für den Gesamt-Entwicklungsplan (23.11.2026)

Pflicht aus AGENTS.md §11 und §12 A: erst Inventur, dann Umsetzung. Quellen: `vault/konzept/00_README_KONZEPT_INDEX.md`, `01_architektur-briefing-multi-bot-bedrock-v14.md`, `02_ui-ux-architektur-briefing-leitstelle-v2.md`, `03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md`, Anlagen 01–10 (insb. 03 Revisionsprotokoll-Matrix, 04–10 VoxelCrew/Frontier), `vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md`, `ops/capabilities.json` und Codebase. **Leseregeln:** „grün" = registrierte Capability + reproduzierbarer Testbeleg; „fehlt" = Soll aus der Spezifikation ohne Beweis → Bauplan. Keine Beweisersetzung durch Befehlssenden, keine geratenen IDs/Härtewerte/Rezepte.

| Soll-Bereich (aus den Quellen) | Ist — nachgewiesen grün (Capability + Beleg) | Fehlt / Backlog (Bauplan) |
| --- | --- | --- |
| Wahrnehmung & 64×36-ToF-Raycaster (alle 500 ms, Amanatides & Woo) | `qa.voxelRaycast` in `ops/qa/world-invariants.mjs` (Augenhöhe y+1,62, ±45°/±35°, 3–32 Blöcke); Referenzanlage `06_referenz-fast-voxel-raycaster-tof.md` | Kontinuierlicher 64×36-Tiefen-Sensor als Bot-Wahrnehmung → **BP-008**. Gate-Strahl ist nicht der Sensor. |
| Sense→Think→Act→Verify (50-ms-HFSM, echte Server-Quittung) | `qa.definitionOfReady` / 5-Phasen-Vertrag Anlage 03 (`PRE_CHECK→TIMING→APPROACH→EXECUTE(50 ms)→VERIFY_AND_COMMIT`), `tests/wissenanalyse-pipeline.test.js` 11/11 | Ausführbarer Executive-Loop mit Quittierung pro Schritt → **BP-009**. |
| Sieben Handlungsgruppen | Low-Level-Primitives `bedrock.sendChat`, `sendEmote`, `commandInteraction`, `walkTo`, `holdItem`, `useBlock`, `setBlock` + `interaction.*`; `tests/interaction.test.js`, `tests/step3a.test.js` | Generische Gruppierungs-/Orchestrierungsschicht → **BP-009**. |
| Gestenerkennung (Winken/Klatschen/Folge-mir, ≤12 Blöcke, 2,5-s-Ringpuffer) | `interaction.resolveGesture`, `interaction.handleEmote`, `bedrock.sendEmote`; Combo-Fenster/Gaze in `tests/interaction.test.js` | Drei Emote-UUIDs **live erfassen** (nicht raten); Ringpuffer-Sensor → **BP-008**. |
| MSA & Co-Pilot-Relay (lokales WebSocket/REST, Modus-Trennung) | Vier Betriebsmodi und `world_context_id`-Isolation als Regel in AGENTS.md §2/SSOT, Actor-Profile-Guard | Relay/Bridge und Co-Pilot-Stufen → **BP-012**; Öffentlichkeit/Ports nur nach Chris-Tor. |
| Shared Weltgedächtnis / Blockmuster / 2.5D-Weltkarte | `memory.WorldMemory` (SQLite, Marker überleben Neustart), Kuratierungsregeln in `vault/wissen/minecraft/README.md` | Geteiltes Gedächtnis, Blockmuster-Bibliothek, 2.5D-Karte → **BP-010**. |
| Ikonischer Resource-Pack-Generator | Keine Capability; Anlage 08/09 als Spezifikation inventarisiert | Generator → **BP-013**; nur kuratierte Quelle, keine geratenen IDs. |
| Leitstellen-Screens/Wizards (Second Screen, Single-Map, Abnahme-Masken) | `werkbank.server`, `werkbank.freigabe`, Cockpit-2ndScreen; `tests/werkbank-browser-e2e.test.js`, `tests/cockpit-browser-e2e.test.js`, 0 Konsolenfehler/0 API-Fehler | Vollständige Wizard-Suite aus UI/UX v2 → **BP-015** (Review-Rückfluss), Teile in **BP-011**. |
| In-Game-UI + Pure-Client-Fallback (`@minecraft/server-ui`) | 4-Stufen-Prozess (AGENTS.md §13, SSOT `uiProzess`, Handbuch 07), `hilfe.botHilfe` mit 60-s-Clip + Eskalationskarte in Werkbank und Leitstelle | Umsetzung im Spiel und Kompass-Notfallknopf → **BP-011**; `server/**` bis Freigabe unangetastet. |
| BDS-Werkstatt (Serverhoheit, FW-Base, atomarer Reset) | `bds.manager` (19132 attach-only/protected), `world.chooseAndReset`, `spectator.liveMode`, `qa.definitionOfReady`; `tests/wissenanalyse-pipeline.test.js` | Feinschliff/FW-Base-Befüllung 19134 → **BP-000 (manual, unverändert)**. |
| Adaptive Scene Director + Multi-Seed-Gate (N ≥ 3) | SSOT `regieLeitplanken A–D`, AGENTS.md §12, Handbuch 05, `world.chooseAndReset` (atomarer Master-Reset) | Ausführender 32×32-Regisseur und 3-Fach-Beweis → **BP-007 (manual, unverändert)**. |
| User-Empathy-Simulator (FOV 70°, ruhiges Tempo) | 10-Jahre-Verständlichkeitscheck in den Stufe-4-Browser-Tests (`hilfe.botHilfe`), SSOT `regieLeitplanken.D` | Unprivilegierter FOV-70°-Spieler-Simulator → **BP-007/D**. |
| Showcases (E2E trocken → Abnahme 19133 → Review) | `show.run`, Observer-Proof (254 `update_block` + 1 `mob_equipment`), `qa.acceptance` via `npm run show:accept`, `verify:show` grün | Showcase-Skript/Review-Rückfluss als eigener Baustein → **BP-015**. |
| Tribes/Frontier (SQLite-Weltgedächtnis, Frontier, eine Instanz pro Welt) | Schnittstelle v2 volltextinventarisiert in Abschnitt 7/8 (Status `extracted`/`beantwortet`) | Implementierung und offene Zahlen/IDs → **BP-014** (nicht raten). |
| Zero-Terminal-Betrieb (Auto-Sync, Notebook-Export, RAM-Schranken) | `sync.werkbankAuto` (25 Prüfungen), `export.notebookSync`, `ram.maschinenSchwellen` (13 Prüfungen), Bot-Hilfe in beiden UIs | Kein offener Soll-Anteil; Knowledge-/Worker-Dispatch bleibt bewusst pausiert. |

**Fazit:** Jeder Soll-Bereich der Spezifikation ist entweder mit Beleg grün oder eindeutig einem Bauplan zugeordnet. Damit ist das Mapping lückenlos; kein Agent muss blind raten oder mehrfach nach demselben Baustein suchen. Die Kette ab `BP-008` ist abhängigkeitsgeprüft (siehe `ops/blueprints/registry.json`, `haengtVon` je Bauplan) und bleibt vollständig `status: entwurf`, `freigabe: manual`. `BP-000`–`BP-007` sind unverändert. Der ausführliche, kindgerechte Plan steht in `vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md`.

## 12) T-231 — 1-Link-SSOT-Bundle & Pages-Auto-Mirror (Soll → Ist)

**User Journey ohne manuelle Klicks:** Ein Push auf `main` im Code-Repo löst `.github/workflows/notebook-mirror-sync.yml` aus → `npm run notebook:export` liest SSOT, Regeln, Baupläne, Handbuch und Konzept/Anlagen (live Vault oder eingecheckter Quellspiegel) → Redaction-Check bricht bei Tokens/.env/Auth-Cache/Server-Secrets/Keys hart ab → `voxelcrew-sync-bundle.md`, `index.html` und `.nojekyll` entstehen → die Action kopiert genau diese drei Dateien mit `NOTEBOOK_SYNC_TOKEN` nach `brotfeld/voxelcrew-notebook-sync` (`main`) → ein einziger Pages-Link liefert die gerenderte Gesamtspezifikation an NotebookLM.

| Soll | Ist | Beleg |
| --- | --- | --- |
| Ein Link = gesamte aktuelle Spezifikation | Bundle mit SSOT inkl. `repos`, AGENTS.md, WORLD_INVARIANTS.md, SPEC_MAPPING.md, capabilities.json, Registry, BP-000–BP-015, Handbuch 01–07, 20 Konzept-/Anlagendokumenten und frischer Inventur (Negativliste 0) | `tests/notebook-export-bundle.test.mjs` |
| Vollständig gerendertes HTML ohne 404 | UTF-8-`index.html` mit einem Artikel pro Quelle, `nav` zu jedem Abschnitt und `.nojekyll`; keine externen Requests | `tests/notebook-export-bundle.test.mjs` |
| Push auf `main` genügt | Workflow mit `push: branches: [main]` + `workflow_dispatch`, Mirror nur der drei Artefakte via `${{ secrets.NOTEBOOK_SYNC_TOKEN }}` | `.github/workflows/notebook-mirror-sync.yml` |
| CI ohne Vault bleibt funktionsfähig | `ops/notebook-sync/quellen/` (27 Dateien, `_manifest.json`) als eingecheckter Quellspiegel; `npm run notebook:quellen` frischt ihn aus dem Vault auf | `tests/notebook-export-bundle.test.mjs` (Fallback-Modus) |
| Keine Secrets im Bundle | `pruefeRedaction()` vor jedem Schreiben; SSOT `repos` enthält nur Slugs/Pages-URL | `tests/notebook-export-bundle.test.mjs` (5 verbotene Muster) |

**Gaps / Risiken:** Der Pages-Link ist erst nach dem ersten erfolgreichen Workflow-Lauf auf `main` öffentlich erreichbar; das Secret `NOTEBOOK_SYNC_TOKEN` muss im Mirror-Repo vorhanden sein. Worker-Dispatch bleibt pausiert; keine finale Einladung zur Live-Präsentation.
```

## ops/capabilities.json

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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/Emotes.md",
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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/minecraft/README.md",
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
      "doku": "vault/wissen/minecraft/README.md",
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
      "doku": "vault/wissen/Emotes.md",
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
      "doku": "vault/wissen/Emotes.md",
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
      "doku": "vault/wissen/Befehle-und-Gesten.md",
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
      "doku": "vault/wissen/minecraft/README.md",
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
      "doku": "vault/wissen/minecraft/README.md",
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
      "doku": "vault/wissen/minecraft/README.md",
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
      "funktion": "exportNotebookSync",
      "zeile": null,
      "parameter": [
        {
          "name": "out",
          "typ": "string",
          "pflicht": false,
          "beschreibung": "Zielordner; Default <root>/.local-notebook-sync/ (gitignoriert)"
        },
        {
          "name": "ohneVault",
          "typ": "boolean",
          "pflicht": false,
          "beschreibung": "CI-Fallback: Quellen aus ops/notebook-sync/quellen/ statt aus dem Vault"
        }
      ],
      "block_states": [],
      "doku": "ops/werkbank/README.md",
      "verified": true,
      "beleg": "tests/notebook-export-bundle.test.mjs: Bundle mit SSOT inkl. repos, AGENTS.md, WORLD_INVARIANTS.md, SPEC_MAPPING.md, capabilities.json, Registry, BP-000–BP-015, Handbuch 01–07 und 20 Konzept-/Anlagendokumenten; vollständiges index.html plus .nojekyll; Inventur grün; Redaction hart; Fallback grün"
    },
    {
      "id": "mirror.notebookPages",
      "modul": "ops/scripts/export-notebook-sync.mjs",
      "funktion": "exportNotebookSync",
      "zeile": null,
      "parameter": [
        {
          "name": "trigger",
          "typ": "string (push main|workflow_dispatch)",
          "pflicht": true,
          "beschreibung": "Start des Mirror-Laufs ohne manuellen Klick"
        },
        {
          "name": "NOTEBOOK_SYNC_TOKEN",
          "typ": "secret",
          "pflicht": true,
          "beschreibung": "Push-Recht für brotfeld/voxelcrew-notebook-sync; niemals im Repo oder Bundle"
        }
      ],
      "block_states": [],
      "doku": ".github/workflows/notebook-mirror-sync.yml, ops/tasks/T-231.md",
      "verified": true,
      "beleg": "tests/notebook-export-bundle.test.mjs prüft Trigger, Secret und genau drei kopierte Artefakte (voxelcrew-sync-bundle.md, index.html, .nojekyll); SSOT repos.notebookMirror/notebookPagesUrl aus ops/SYSTEM_SSOT.json"
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
    "methode": "Read-only Inventur aller Inbox-Originale und weiterverarbeiteter Artefakte (vault/konzept/**, vault/wissen/szenarien/**, vault/wissen/**), Referenzanlagen 01–10, Handbuch 05 und Codebase. Trennung strikt nach Beweisstand: 'nachgewiesen grün' nur mit registrierter Capability + reproduzierbarem Testbeleg, alles Übrige 'fehlt' mit Bauplan-Zuordnung. Keine geratenen IDs/Härtewerte/Rezepte.",
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

## ops/blueprints/registry.json

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

## ops/blueprints/BP-000.json

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

## ops/blueprints/BP-001.json

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

## ops/blueprints/BP-002.json

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

## ops/blueprints/BP-003.json

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

## ops/blueprints/BP-004.json

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

## ops/blueprints/BP-005.json

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

## ops/blueprints/BP-006.json

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

## ops/blueprints/BP-007.json

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

## ops/blueprints/BP-008.json

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

## ops/blueprints/BP-009.json

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

## ops/blueprints/BP-010.json

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

## ops/blueprints/BP-011.json

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

## ops/blueprints/BP-012.json

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

## ops/blueprints/BP-013.json

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

## ops/blueprints/BP-014.json

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

## ops/blueprints/BP-015.json

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

## vault/handbuch/01_Systemueberblick_und_Architektur.md

```markdown
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
```

## vault/handbuch/02_Server_Welten_und_Akteure.md

```markdown
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
```

## vault/handbuch/03_Faehigkeiten_und_Bauplan_Katalog.md

```markdown
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
```

## vault/handbuch/04_Bedienung_und_Ablaeufe.md

```markdown
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
```

## vault/handbuch/05_Autonome_Weltanpassung_und_Regie.md

```markdown
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
```

## vault/handbuch/06_Gesamt_Entwicklungsplan_und_Meilensteine.md

```markdown
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
```

## vault/handbuch/07_Vier_Stufen_UI_Prozess.md

```markdown
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
```

## vault/konzept/00_README_KONZEPT_INDEX.md

```markdown
# Master-Index & Steuerungs-Mandat: Autonomes Multi-Bot- & Tribes-System (Minecraft Bedrock 26.65+)

## 1. Oberstes Arbeitsmandat für das Ziel-Architekturmodell
Das System arbeitet nach dem Prinzip **"Autonomous End-to-End Execution before Human Acceptance"**:
1. **Eigenständige Konzeption & Entwicklung:** Das Architekturmodell und die nachgelagerten Entwicklungs-Agenten konzipieren, implementieren und integrieren sämtliche Module selbstständig auf Basis dieses Gesamtpakets.
2. **Autonome Verifizierung:** Alle Komponenten werden vor jeder menschlichen Sichtung durch selbst generierte E2E-Tests (`E2E_TEST_SERVER`) gegen reale Spielregeln (`Minecraft Bedrock 26.65+`) geprüft.
3. **Geführte Abschluss-Präsentation (Showcase):** Erst nach bestandener E2E-Verifizierung lädt das System den menschlichen Nutzer auf den `DEMO_SHOWCASE_SERVER` (sowie das NeutralinoJS Second-Screen-UI) zu einer humorvollen, kindgerechten (ab 10 Jahren) Live-Vorführung ein.
4. **Strukturierte Datenerfassung bei der Abnahme:** Während und nach der Vorführung werden Änderungswünsche über geführte Datenerfassungsmasken (In-Game-UI & Second Screen) erfasst und in der SQLite-Tabelle `review_submissions` gespeichert, um sofort als neue Tasks in die Entwicklungs-Queue zurückzufließen.

---

## 2. Autonome Showcase-Produktions-Pipeline (Regie-Workflow ab 10 Jahren)
Für die Erstellung von Demonstrationen auf dem `DEMO_SHOWCASE_SERVER` folgt das System einem 5-stufigen Regie-Prozess (abgebildet im JSON-Schema `10_referenz-showcase-regie-und-review-schema.md`):

1. **Konzept & Lernziel:** Ein klares Feature pro Szene (z. B. *„Wie Bots durch Gesten und Block-Muster zusammenarbeiten“*), verständlich erklärt für Kinder ab 10 Jahren.
2. **3-Akt-Story mit Slapstick-Humor:**
   * *Akt 1 (Alltag & Problem):* Ein tollpatschiger Bot scheitert humorvoll an einer Aufgabe (z. B. verstopfter Ofen oder verlorener Weg).
   * *Akt 2 (Rettung & Feature-Einsatz):* Ein Spieler oder ein spezialisierter Bot setzt das neue System-Feature ein (z. B. ein Block-Muster bauen oder per Handzeichen helfen).
   * *Akt 3 (Happy End & Pointe):* Alles funktioniert reibungslos, gefolgt von einer lustigen Emote-Feier.
3. **Protagonisten-Casting:** Auswahl und Konfiguration von 2–3 kontrastierenden Bot-Charakteren (z. B. *Kalle* der übereifrige, tollpatschige Lehrling vs. *Bruno* der tiefenentspannte Profi).
4. **Szenenbild & Requisiten:** Automatischer Aufbau einer kompakten, visuell klaren Bühne (max. 32×32 Blöcke) inklusive vorbereiteter Truhen, Block-Muster-Blöcke und Markierungen.
5. **Kameraführung & Cue-Sheet:** Definierte Kamerapositionen (`/camera` bzw. Teleport-Anker), Untertitel-Einblendungen (`title` / `actionbar`) und synchronisierte Bot-Aktionen (`showcase_script.json`).
6. **Beschränkung auf verfübare Elemente der Vanilla Edition**
Es werden ausschließlich Spielelemnte für die Kulissen, Requisiten, Bot-Erscheinungsbilder oder sonstigem verwendet, die auch in der Vainlla Version von Minecraft Bedrock (`Minecraft Bedrock 26.65+` zum Zeitpunkt der Entwicklung) verfügbar sind.

---

## 3. Verbindliche Lesereihenfolge des Gesamtpakets

| Nr. | Dateiname | Inhalt & Zweck |
| :--- | :--- | :--- |
| `00` | `00_README_KONZEPT_INDEX.md` | Dieses Steuerungsdokument, Autonomie-Mandat & Showcase-Regie-Pipeline |
| `01` | `01_architektur-briefing-multi-bot-bedrock-v14.md` | Gesamtarchitektur (Module A–F, Portable-First, Pure-Client vs. BDS, Dimensionen, Stranded-Recovery, Gesten) |
| `02` | `02_ui-ux-architektur-briefing-leitstelle-v2.md` | UI/UX-Konzept (NeutralinoJS Second Screen, ab 10 Jahren, 6 Konfigurations-Studios, In-Game-UI & Fallback) |
| `03` | `03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md` | IPC-Kontrakte zwischen Tribes-Simulation, Shared Weltgedächtnis, Leitstelle & Semantic Zones |
| `04` | `04_referenz-zod-schemas-ipc-semantic-zones.md` | TypeScript- & `Zod`-Validierungs-Schemas für IPC-Envelopes, Zonen-Regeln & Anomalie-Events |
| `05` | `05_referenz-bds-script-api-memory-bridge.md` | BDS `BehaviourPack` (`@minecraft/server-net`) für den botlosen `HEADLESS_TRIBES_SERVER`-Ingest |
| `06` | `06_referenz-fast-voxel-raycaster-tof.md` | Zero-Allocation *Amanatides & Woo* `64×36` ToF-Raycaster & Abgrund-Detektor |
| `07` | `07_referenz-reverse-dependency-matcher-sqlite.md` | SQLite-Engine & Reverse-Dependency-Matcher für deklarative `Semantic Zones` |
| `08` | `08_referenz-bds-behaviourpack-ingame-ui.md` | Natives In-Game-UI (`@minecraft/server-ui`) für Produktivbetrieb & Actionbar-HUD |
| `09` | `09_referenz-bds-behaviourpack-test-demo-ui.md` | Spezialisierte In-Game-UIs für `E2E_TEST_SERVER` & `DEMO_SHOWCASE_SERVER` |
| `10` | `10_referenz-showcase-regie-und-review-schema.md` | JSON-Schema für Regie-Drehbücher (`showcase_script.json`) & SQLite-Schema für Abnahme-Feedback (`review_submissions`) |
```

## vault/konzept/01_architektur-briefing-multi-bot-bedrock-v14.md

```markdown
# Architektur-Briefing: Autonomes Multi-Bot- & Tribes-System (Minecraft Bedrock 26.65+)

## 0. Unverrückbare Grundprinzipien (Design-Constraints)
1. **Portable-First (Wechseldatenträger-Fähigkeit):** Die gesamte Anwendung inklusive Node.js-Runtime, SQLite-Datenbanken (`./data/`), Bot-Profilen (`./profiles/`), Plugins (`./plugins/`) und optionalen BDS-Instanzen (`./servers/`) muss ohne Installation, ohne OS-Registry-Einträge und ausschließlich über **relative Pfade** von einem portablen Datenträger lauffähig sein. Flash-Speicher wird durch SQLite-WAL-Caching (gebündelte RAM-Flushes alle `5 Sekunden`) geschont.
2. **Pure-Client-Garantie:** Außerhalb kontrollierter BDS-Betriebsmodi müssen sämtliche Bots, das Shared Weltgedächtnis und die Leitstelle als **reine Netzwerk-Clients** (`bedrock-protocol`) auf fremden Servern oder in lokalen LAN-Welten funktionieren – ohne serverseitige `BehaviourPacks` oder Admin-Rechte.
3. **Zentrale Wissensdatenbank als Single Source of Truth (SSOT):** Alle kanonischen Minecraft-Begriffe (Blöcke, Items, Biome, Entities, Rezepte, Abbauzeiten, Dimensionsregeln) werden per Read-Only-Lookup aus der zentralen Wissensdatenbank bezogen. Keine doppelten Eigen-Lexika.
4. **Terminologie-Trennung:** Strikte Unterscheidung zwischen `VanillaStructure` (vom Spiel generierte Bauwerke wie *Village*, *Nether Fortress*), `PlayerConstruct` (von Spielern/Bots gebaute Anlagen), `CustomStructure` (durch Addons/Packs definierte Strukturen) und `SemanticZone` (durch erkennung von Block-Mustern definierte Wirkungsbereiche).
5. **Akteurs-Taxonomie:** Klare Trennung zwischen `PLAYER` (menschliche Spieler), `SWARM_BOT` (externe Client-Bots) und `TRIBES_NPC` (serverseitige Dorf-/Stammesbewohner aus dem `Tribes`-`BehaviourPack`). Bots können per Opt-In (`tribes_membership.opt_in: true`) als hochfähige Stammesmitglieder Spezialaufgaben im Tribes-Verbund übernehmen.

---

## 1. Betriebsmodi der Leitstelle & BDS-Server-Rollen
Über den vorhandenen **BDS Server Manager** unterstützt die Leitstelle vier klar getrennte Server-Rollen sowie den serverlosen Client-Betrieb:
* **`PURE_CLIENT`:** Verbindung als reiner Client zu einer bestehenden LAN-Welt oder einem externen Server.
* **`PRODUCTION_BDS`:** Kontrollierter Produktiv-Server (wahlweise mit `Tribes`-`BehaviourPack`, mit Bots oder als `HEADLESS_TRIBES_SERVER` mit `0 Bots`).
* **`E2E_TEST_SERVER`:** Isolierter Analyse- und Testserver (kann parallel zum Produktivserver gestartet werden) für Versions-Kalibrierungen (`26.65` / `26.70`) und automatisierte E2E-Tests.
* **`DEMO_SHOWCASE_SERVER`:** Dedizierter Präsentations-Server für vorgefertigte Vorführungen und menschliche Endabnahmen.

Alle Datenbank-Tabellen führen eine `world_context_id`, um Produktiv-, Test- und Demo-Welten strikt voneinander zu isolieren.

---

## 2. Modul A: Visuelle & Non-Verbale Wahrnehmung
### 2.1 Trainingsfreier 2D-ToF- & 3D-Hybrid-Ansatz
* **Synthetisches `64×36` ToF-Raster:** Berechnet alle `500 ms` über den *Amanatides & Woo Fast-Voxel-Traversal-Algorithmus* Tiefe (`Layer A`), Block-Kategorie (`Layer B`) und 3D-Weltkoordinate (`Layer C`).
* **2D-Bildverarbeitung & 3D-Reality-Check:** Erkennt Abgründe (`ToF[y][x] - ToF[y+1][x] > 3` Blöcke) sowie künstliche Silhouetten (`MORPH_CLOSE` + `findContours`) und verifiziert diese per begrenztem 3D-Flood-Fill in den `prismarine-chunk`-Daten (inklusive kanonischer Rotations-Normalisierung und Abschneiden prozeduraler `beard_box`-Fundamente).

### 2.2 Konfigurierbare Gesten- & Emote-Erkennung (`GestureTelemetrySensor`)
* Überwacht Spieler im Umkreis (`<= 12 Blöcke`) über einen `2,5-Sekunden`-Ringpuffer auf offizielle Bedrock-Emotes (`EmotePacket`) sowie konfigurierbare Bewegungsmuster (`gesture_pattern_rules.json`), z. B. schnelles Nicken (`Pitch`-Wechsel = Ja), Kopfschütteln (`Yaw`-Wechsel = Nein), Ducken + Rückwärtsgehen (`Komm her`) oder Armschwung (`AnimatePacket` + Blickstrahl-Raycast = `Dort hin`).

---

## 3. Modul B: Shared Multi-Bot-Weltgedächtnis & Leitstelle
* **Single-Writer-Architektur:** Ein zentraler Memory-Service verwaltet alle Schreibzugriffe (`better-sqlite3` im WAL-Modus). Eingehende Pakete mehrerer Bots für denselben Chunk werden innerhalb von `500 ms` dedupliziert; überlappende Teil-Strukturen werden per Multi-View-Fusion vereinigt.
* **Event-Hooks (3s Debounce):** Feuert `onStructureDiscovered`, `onStructureModified`, `onStructureDamaged`, `onResourceDepleted` sowie `semantic_zone.created` / `semantic_zone.dissolved`.
* **Declarative Semantic Zones:** Erkennt von Spielern gebaute Block-Muster (z. B. *Heuballen + gefüllter Kessel + Zaun + Bett + Doppeltruhe* = Handelslager; *Goldblock + Rüstungsständer mit Lederhelm & Redstoneblock in Pose 8* = Redstone-Abbaugebiet) rein clientseitig über Anker-Indexierung und überwacht die Schlüssel-Blöcke via Reverse-Dependency-Index (inkl. `30s` Grace-Period).

---

## 4. Modul C: Überleben, GOAP-Planung & Resilienz
* **Dimensions-Adaptor (`Overworld`, `Nether`, `The End`):** Passt Sichtfeld-Auswertung (Höhlen-Decke im Nether, Void im Ende) und Überlebensregeln (Bett-Explosionsschutz, Piglin-Goldrüstung, Enderman-Blickkontakt-Vermeidung) dynamisch an `ChangeDimension` an.
* **Multi-Source-Task-Queue & Survival-Policies:**
  * `STANDARD_SURVIVAL`: Vollständiger Hunger-, Heilungs-, Sauerstoff- (`Air < 30%`), Nacht- (`Time >= 12500`) und Fallschadensschutz.
  * `KAMIKAZE_EXPLORE`: Von der Leitstelle angeordnete Kartierungsmission; Inventar wird vorher im Depot gesichert, Tod & Respawn werden zur schnellen Frontier-Aufdeckung genutzt.
  * `SACRIFICIAL_GUARD`: Opferbereiter Wach-Modus für Schutzaufträge berechtigter Spieler (`trusted_players.json`).
* **`Stranded-Bot-Recovery`-Protokoll:** Erkennt unerwartete Admin-Teleports (`> 50 Blöcke`), friert laufende Aufgaben ein, prüft die Erreichbarkeit der Basis und meldet `BOT_DISPLACED` an die Leitstelle (Rettungsstrategien: *Task-Reassignment*, *`NOMAD_SURVIVAL`-Rückmarsch*, *Taktischer Respawn* oder *Außenposten-Gründung*).

---

## 5. Modul D, E & F: Plugins, Dialog-Pipeline & Taktische Ausführung
* **Modulare Bot-Profile & Blackboard:** Charakterzüge (`sociability`, `diligence`, `caution`, `curiosity`) gewichten GOAP-Ziele, Blickkontakt und Sprachstil. Custom-Sensoren und -Skills docken über ein typisiertes `BotPlugin`-Interface an das Blackboard an.
* **Dual-Track Dialog-Pipeline (Modul E):** Trennt Chat-Kommandos (`!watch`, `!guard`, `!abort`) von freier Sprache (`NLU-Router` ➔ `Context-Resolver` ➔ `NLG-Generator` mit Upgrade-Pfad von Templates zu `onnxruntime-node` / `node-llama-cpp`) und verknüpft non-verbale Gesten mit offenen Dialog-Rückfragen.
* **In-Game-UI & Fallback:** Nutzt `@minecraft/server-ui`, sobald das `BehaviourPack` verfügbar ist; fällt im `PURE_CLIENT`-Betrieb nahtlos auf Chat, Gesten, Emote-Spiegelung und das Second-Screen-UI zurück.
* **HFSM-Executor & Workflow-Rekorder (Modul F):** Setzt Pläne tickgenau (`50 ms`) mit Task-Stack-Preemption um und kompiliert beobachtete Spieler-Abläufe (`/bot watch me`) in wiederverwendbare JSON-Workflows.
```

## vault/konzept/02_ui-ux-architektur-briefing-leitstelle-v2.md

```markdown
# UI/UX-Architektur-Briefing: Second Screen (NeutralinoJS) & In-Game-UI

## 1. Leitbild & Zielgruppen-Anspruch (Einstiegsschwelle ab 10 Jahren)
* **Zielgruppe ab 10 Jahren:** Keine rohen JSON-Editoren, kein Regex, keine SQL-Ansichten. Alle technischen Konzepte werden in **visuelle Spiel-Metaphern** übersetzt (Sammelkarten, Rezeptbücher, Ampeln, Comic-Schrittketten, klare Antwort-Buttons).
* **Technologie-Basis:** Reines **HTML5 / CSS3 / TypeScript (Vanilla Web Components)** ohne schwere Frameworks, gebündelt als portable **NeutralinoJS**-Desktop-App (mit Fallback auf den lokalen Browser unter `http://127.0.0.1:<port>`).
* **Dual-Layer-Bedienung:** Wo das BDS-`BehaviourPack` aktiv ist, steht eine native **In-Game-UI** (`@minecraft/server-ui`) bereit. Fehlt der Server (`PURE_CLIENT`), übernimmt die nahtlose Fallback-Kette aus **Chat, Gesten, Bot-Feedback und Second-Screen-UI**.

---

## 2. Informationsarchitektur des Second-Screen-UIs (NeutralinoJS)

### 2.1 Der Szenario-Launcher (Startbildschirm)
Beim Start wählt der Nutzer eine von drei großen Bildkarten statt technischer Server-Flags:
1. **„In bestehende Welt einsteigen“** (`PURE_CLIENT` / LAN-Welt).
2. **„Eigene Bot-Welt starten“** (`PRODUCTION_BDS` mit Schwarm-Bots).
3. **„Große Stammes-Welt starten“** (`PRODUCTION_BDS` + `Tribes`-Simulation).

### 2.2 Das Single-Map-Paradigma (Zentrale Taktische Weltkarte)
Es gibt im Produktivbetrieb **exakt eine zentrale 2D-Weltkarte**, die als Haupt-Dashboard, Live-Event-Feed und Navigations-Zentrale dient:
* **Zuschaltbare Karten-Layer:** Bots & Blickkegel, bekannte Strukturen (`VanillaStructure` & `PlayerConstruct`), aktive `Semantic Zones` (mit Wirkungsradius), `Tribes`-Siedlungen & Handelsrouten (wenn Modul aktiv) sowie unerforschter Nebel (*Frontiers*).
* **Kontext-Klick auf die Karte:** Öffnet ein direktes Aktionsmenü (*„Bot hierher schicken“*, *„Gebiet erkunden“*, *„Kamikaze-Aufklärung starten“*, *„Wachposten aufstellen“*).
* **Eskalations-Postfach (Oben rechts):** Wenn ein Bot gestrandet ist (`BOT_DISPLACED`) oder eine autonome Test-Frage offen bleibt, erscheint ein freundliches Ausrufezeichen mit einer verständlichen Frage und **2–3 großen Entscheidungs-Buttons**.

### 2.3 Bot-Kommandostand (Schwarm & Einzel-Bot)
* **Schwarm-Übersicht:** Kompakte Status-Karten aller aktiven Bots (Gesundheit, Hunger, aktueller Auftrag, Stimmung).
* **Einzel-Bot-Fokus:** Aktive Aufgaben-Warteschlange (Task-Stack mit Pause/Priorisieren/Abbrechen), Live-Chat & Gesten-Verlauf sowie direktes Inventar.
* **Debug-Reiter (sekundär):** Ausgelagerte Entwickler-Ansicht für das `64×36` ToF-Tiefenbild, Konturen-Masken und rohe Gesten-Vektoren.

---

## 3. Die 5 kindgerechten Konfigurations-Studios (CRUD-API-gestützt)
1. **Bot-Charakter-Baukasten:** Sammelkarten-Editor mit 4 Emoji-Schiebereglern (*Freundlichkeit, Fleiß, Vorsicht, Neugier*), Live-Sprechblasen-Vorschau und Ausrüstungs-Slots für Fähigkeiten (*Holzfäller, Leibwächter, Redstone-Spürnase, Tribes-Helfer*).
2. **Ort- & Block-Muster-Designer (Rezeptbuch):** Zeigt alle `Semantic Zones` wie Minecraft-Crafting-Rezepte (*„Baue diese Blöcke nebeneinander ➔ Handelslager“*) und bietet einen 5×5-Block-Editor zum Erfinden eigener Block-Muster.
3. **Gesten- & Kommando-Studio:** Visuelle „Wenn Spieler das macht ➔ Dann tut Bot das“-Karten für Emotes, Nicken/Kopfschütteln und Chat-Schlüsselwörter inklusive Live-Gesten-Ampel und Spieler-Avatar-Whitelist.
4. **Ablauf-Rekorder & Tagesplan:** Start-Button für *„Bot zuschauen lassen“* und ein Comic-Strip-Editor für gelernte Workflows (z. B. *Seetang-Kreislauf*), mit Zeitplan-Auswahl (*„Jeden Morgen“*, *„Auf Knopfdruck“*).
5. **Welt-Kompendium (Pokédex-Style):** Durchsuchbare Kacheln für Blöcke, Items, Tiere/Monster, Biome und Bauwerke – klar getrennt nach *Vanilla* und *Geladenen Zusatz-Paketen*.

---

## 4. Dynamische Spezial-Bereiche (Feature-Flag-gesteuert)
* **BDS-Admin-Tools:** Ein-Klick-Start/Stopp, Backup und Konfigurations-Schalter für den Server.
* **Analyse-Server-Cockpit (wenn aktiv):** Steuerung und Fortschrittsbalken für Hintergrund-Analysen auf dem parallelen Analyse-Server.
* **Demonstrations-Server-Regie (wenn aktiv):** Vorführungs-Fernbedienung für fertige Showcases und Abnahme-Masken.
* **E2E-Test-Monitor (wenn aktiv):** Isolierte Test-Minimap (strikt getrennt von der Hauptwelt) mit Ampel-Status der laufenden Test-Suiten und Bot-Feedback.
```

## vault/konzept/03_schnittstellen-spezifikation-tribes-weltgedaechtnis-leitstelle-v2.md

```markdown
# Schnittstellen-Spezifikation: Tribes-Modul ↔ Shared Weltgedächtnis ↔ Leitstelle

## 1. Transport & Portable IPC-Architektur
* **Protokoll:** Lokales WebSocket (`ws://127.0.0.1:<port>`) für bidirektionale Echtzeit-Events sowie HTTP-REST (`http://127.0.0.1:<port>/api/v1`) für Abfragen und CRUD-Operationen.
* **Dynamische Port-Aushandlung:** Beim Start schreibt die Leitstelle alle vergebenen Loopback-Ports und das Sitzungs-Token in `./runtime/session_ports.json`.
* **Betriebsmodi:** Unterstützt sowohl `PRODUCTION_BDS` (mit oder ohne aktive `SWARM_BOTs`, also auch `HEADLESS_TRIBES_SERVER`) als auch den rein clientseitigen `PURE_CLIENT`-Betrieb.

---

## 2. SQLite-Erweiterungsschema (Single-Writer im Weltgedächtnis)

```sql
CREATE TABLE world_landmarks (
    landmark_id TEXT PRIMARY KEY,
    world_context_id TEXT NOT NULL,
    dimension TEXT NOT NULL,
    name TEXT NOT NULL,
    category TEXT NOT NULL,          -- "TRIBE_SETTLEMENT", "TRADING_POST", "DEPOT", "OUTPOST"
    owner_tribe_id TEXT,
    x INTEGER NOT NULL, y INTEGER NOT NULL, z INTEGER NOT NULL,
    radius INTEGER DEFAULT 25,
    status TEXT DEFAULT 'ACTIVE',    -- "ACTIVE", "UNDER_ATTACK", "DESTROYED"
    updated_at INTEGER NOT NULL
);

CREATE TABLE trade_routes (
    route_id TEXT PRIMARY KEY,
    world_context_id TEXT NOT NULL,
    tribe_id TEXT NOT NULL,
    origin_landmark_id TEXT NOT NULL,
    destination_landmark_id TEXT NOT NULL,
    waypoints_json TEXT NOT NULL,    -- Array von [x, y, z] Koordinaten
    risk_level REAL DEFAULT 0.1,
    status TEXT DEFAULT 'OPEN',      -- "OPEN", "BLOCKED", "ORPHANED"
    updated_at INTEGER NOT NULL
);

CREATE TABLE semantic_zones (
    zone_id TEXT PRIMARY KEY,
    world_context_id TEXT NOT NULL,
    dimension TEXT NOT NULL,
    pattern_id TEXT NOT NULL,
    zone_type TEXT NOT NULL,
    anchor_x INTEGER NOT NULL, anchor_y INTEGER NOT NULL, anchor_z INTEGER NOT NULL,
    radius INTEGER NOT NULL,
    status TEXT DEFAULT 'ACTIVE',    -- "ACTIVE", "SUSPENDED", "DISSOLVED"
    suspended_since INTEGER,
    created_at INTEGER NOT NULL
);

CREATE TABLE semantic_zone_dependencies (
    dep_id INTEGER PRIMARY KEY AUTOINCREMENT,
    zone_id TEXT NOT NULL,
    kind TEXT NOT NULL,              -- "BLOCK" | "ENTITY"
    target_id TEXT NOT NULL,
    x INTEGER, y INTEGER, z INTEGER,
    entity_runtime_id TEXT,
    FOREIGN KEY (zone_id) REFERENCES semantic_zones(zone_id) ON DELETE CASCADE
);
```

---

## 3. Kern-Schnittstellen & Event-Kanäle
1. **Tribes ➔ Weltgedächtnis (`tribes.landmark.upsert`, `tribes.route.upsert`):** Registriert Stammes-Siedlungen, Handelsposten und geplante Karawanen-Routen im Shared Weltgedächtnis.
2. **Dualer Beobachtungs-Ingest (`world.raw_observation`):** Speist Block- und Entity-Änderungen wahlweise aus `SWARM_BOT`-Paketen oder direkt aus dem BDS-`BehaviourPack` (`HEADLESS_TRIBES_SERVER`) in die `Semantic-Zone`-Engine ein.
3. **Weltgedächtnis ➔ Tribes (`memory.hook.route_obstructed`, `memory.hook.semantic_zone.created`, `memory.hook.semantic_zone.dissolved`):** Meldet blockierte Handelswege, neu errichtete Spieler-Handelslager (Block-Muster) oder zerstörte Markierungen in Echtzeit an die Handels-Simulation.
4. **Tribes ↔ Leitstelle (`tribes.assist.request`, `dispatch.task.assigned`, `bot.anomaly.displaced`):** Ermöglicht es dem Tribes-Modul, Opt-In-Bots für Spezialaufgaben (Brückenbau, Karawanen-Eskorte, Entlastung von `TRIBES_NPCs`) anzufordern und auf Bot-Anomalien zu reagieren.
```

## vault/konzept/2026-11-19 anlage-01-revision-v1-chris-architektur.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 01 (Revision v1 von Chris): Architektur-Erweiterung für VoxelCrew & VoxelCrew Frontier

> **Bezug:** Ergänzt `01_architektur-briefing-multi-bot-bedrock-v14.md` um die verabschiedete Namensgebung (**VoxelCrew**, **VoxelCrew Frontier**), die 4-stufige Sense-Think-Act-Verify-Pipeline, Betriebs-Modi, den Relay-/Co-Pilot-Modus, höhenbegrenzte Block-Muster und die Icon-Pipeline.

## 1. Verbindliche Namensgebung & Rollen-Trennung
* **Plattform & Leitstelle:** **VoxelCrew** (Akteure: `PLAYER`, `CREW_BOT` / `SWARM_BOT`).
* **Siedlungs- & Handels-Addon:** **VoxelCrew Frontier** (ehemals Arbeitstitel *Tribes-Modul*).
  * **Die drei NPC-Gilden (`culture_guild`):** `SETTLERS` (Oberfläche), `DELVERS` (Tiefe Höhlen `Y < 0`), `WAYFARERS` (Karawanen-Nomaden zwischen den Ebenen). Rahmenstory: *„Die Narben der Tiefe“* (Schluchten-Konflikt zwischen Settlers und Delvers).
  * **Zukunfts-Anker für Spieler-Stämme (`player_tribe_id` / `alliance_id`):** Strikt getrennt von den drei NPC-Gilden werden im Datenmodell (`world_landmarks`, `semantic_zones`) optionale Felder für von Spielern gegründete Stämme (*Player Tribes*), befestigte Territorien (`defense_rating`, `territory_radius`) und Allianzen reserviert.

## 2. Geschlossener Vier-Stufen-Kreislauf (Sense ➔ Think ➔ Act ➔ Verify)
1. **Stufe 1 – Reiz-Pipeline (Sense):** Bündelt `64×36`-ToF-Vision, Chunk-Daten, Körperwerte (`Health`, `Hunger`, `Air`), Weltgedächtnis-Hooks sowie den modularen `GestureTelemetrySensor`.
2. **Stufe 2 – Entscheidungs-Pipeline (Think):** Utility- & GOAP-Abwägung unter dem Filter des aktiven **Betriebs-Modus**:
   * `ALLTAG`: Voller Selbstschutz; pausierte Aufgaben wandern auf den Task-Stack.
   * `KAMIKAZE`: Inventar wird vorab in einer registrierten Truhe (`Depot`) gesichert; danach werden Selbstschutz-Regeln deaktiviert, um tiefe Frontier-Erkundungen durchzuführen.
   * `LEIBWAECHTER`: Begleitschutz für Spieler; umfasst soziale Hunger-Abwägungen (Proviant-Bitte an den Spieler vor kurzer Jagdpause in Sichtweite) sowie normale vs. opferbereite Schutzstufe.
3. **Stufe 3 – Motorik-Pipeline (Act):** Weiche Kamera-Interpolation (`Yaw`/`Pitch`) und Tick-genaue (`50 ms`) Paket-Steuerung über alle **7 Handlungsgruppen** (inklusive Zähmen, Reiten, Fliegen, Anleinen und Truhenboot-Logistik).
4. **Stufe 4 – Kontroll- & Störfall-Pipeline (Verify & Recover):** Verrechnet Server-Quittungen (`UpdateBlock`, `MovePlayer`, `SetEntityLink`) und aktiviert bei Abweichungen konfigurierbare Störfall-Wächter (`StuckDetector`, `CliffGuard`, `LineOfSightGuard`, `LeashTensionGuard`).

## 3. Relay- & Co-Pilot-Modus (`bedrock-protocol` Proxy)
Ein Crew-Bot kann als lokaler Proxy zwischen den Minecraft-Client eines Spielers und den Zielserver geschaltet werden (`PASS_THROUGH_RECORDER`, `COPILOT_GUARD_OVERLAY`, `AUTOPILOT_AFK_TAKEOVER`). Im Co-Pilot-Modus greifen die konfigurierten Störfall-Wächter (z. B. Kanten-Absturz-Schutz) direkt auf die Spielersitzung zu, und das Lernen-durch-Zeigen erfasst Inventar- und Rezept-Aktionen verlustfrei.

## 4. Höhenbegrenzte Block-Muster (`BlockPattern`) & 3D-Icon-Pipeline
* **Begriff:** Durchgängig **Block-Muster (`BlockPattern`)** verwenden (T-229-Begriffsbereinigung).
* **Höhen-Begrenzung:** Jedes erkannte Block-Muster definiert neben dem horizontalen Radius explizit `min_y_offset` und `max_y_offset`, damit unterirdische *Delver*-Siedlungen (`Y < 0`) nicht bis in *Settler*-Wächterzonen an der Oberfläche durchschlagen.
* **Icon-Generator:** Ein Hintergrund-Worker extrahiert 2D-Items und rendert 3D-Blöcke (inklusive Treppen, Zäune, Stufen, Türen und Addon-`.geo.json`) isometrisch in den portablen Cache `./data/icon_cache/`.
```

## vault/konzept/2026-11-19 anlage-02-revision-v1-chris-ui-leitstelle.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---
# Anlage 02 (Revision v1 von Chris): UI/UX-Erweiterung für die VoxelCrew-Leitstelle

> **Bezug:** Ergänzt `02_ui-ux-architektur-briefing-leitstelle-v2.md` für die NeutralinoJS-Second-Screen-App (Einstiegsschwelle ab 10 Jahren) um das **VoxelCrew**-Branding, den Bot-Flugschreiber, Pipeline-Steckbretter, die MSA-Kontoverwaltung, den Relay-Umschalter, die 2.5D-Schichtenkarte, den Block-Muster-Editor sowie die Server-Werkstatt.

## 1. Branding & Design-System (`VoxelCrew`)
* **Visuelle Identität:** Scharfe Pixel-Kanten (`0px` Radius), 3D-Bevel-Rahmen auf Schiefer-Dunkel (`#1E222A`), Smaragdgrün (`#17DD62`) als Primärfarbe, Redstone-Rot (`#E53935`) für Warnungen/Aufnahme und Gold (`#FFCA28`) für **VoxelCrew Frontier**. Einbindung der Logos aus `assets/branding/`.
* **Altersgerechte Sprache (ab 10 Jahren):** Technische Bereiche heißen **„Server-Werkstatt & Einstellungen“**, **„Bot-Flugschreiber“**, **„Pipeline-Steckbrett“** und **„Block-Muster-Baukasten“**.

## 2. Bot-Flugschreiber (Kindgerechter Aktions-Rekorder)
* **Steuerung:** Aufnahme-Button plus **60-Sekunden-Rückspul-Ringpuffer**; Zeitleiste mit farbigen Ereignis-Pins (Gelb = neuer Entschluss, Rot = Störfall-Wächter, Grün = Erfolg) und Tick-Schritten (`< | >` in `50 ms`-Schritten).
* **4 synchrone Blickfenster:** (1) Sinne & `64×36`-ToF-Vorschau, (2) Entscheidungs-Waage mit Punktzahlen & Betriebs-Modus, (3) Motorik-Kompass & aktive Tasten, (4) Kontroll-Ampel & Störfall-Wächter-Status.

## 3. Bot-Verbindungs-, Konto- (MSA) & Relay-Verwaltung
* **Konnektivitäts-Modi:** Klare Auswahl zwischen *Eigener Server ohne Anmeldung*, *Lokale LAN-Welt (Konto erforderlich)*, *Online-Server (Konto erforderlich)* und *Test-/Demo-Server*.
* **Geführter MSA-Login & Trennen:** Großer Einmal-Code (`ABCD-1234`) mit Browser-Start und automatischer Status-Prüfung sowie ein geschützter Button **„Konto trennen“** (löscht `./runtime/auth_cache/<bot_id>/`) und Sperre gegen Doppelbelegung.
* **Co-Pilot- / Relay-Schalter:** Direkte Umschaltung pro Bot zwischen *Autonom*, *Relay: Beobachter*, *Relay: Schutzengel (Co-Pilot)* und *Relay: Autopilot (AFK)*.

## 4. 2.5D-Schichtenkarte & Block-Muster-Editor
* **2.5D-Höhen-Stockwerke:** Umschaltbare Ebenen (*Gipfel `Y > 100`*, *Oberfläche `Y 50–100`*, *Obere Höhlen `Y 0–49`*, *Tiefe Höhlen `Y -64 bis -1`*, *Röntgen-Blick*) inklusive Schacht-/Aufzug-Icons und Routen-Höhenprofil. Vorbereiteter Layer für spätere **Spieler-Stammesgebiete** (*Player Tribes*).
* **Block-Muster-Editor:** 5×5-Etagen-Raster (Unten/Mitte/Oben) mit isometrischen Block-Icons, Umkreis-Zutaten-Box, Schiebereglern für Radius und **Höhen-Begrenzung** sowie Live-Checkliste (*„Was fehlt dem Muster im Spiel noch?“*).
```

## vault/konzept/2026-11-19 anlage-03-revision-v1-chris-protokoll-matrix.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 03 (Revision v1 von Chris): Protokoll-Matrix & 5-Phasen-Handlungsvertrag

> **Bezug:** Technische Spezifikation für den 50-ms-HFSM-Executor in **VoxelCrew** (`bedrock-protocol` für Bedrock `26.65+`).

## 1. Universeller 5-Phasen-Handlungsvertrag (Execution Contract)
1. **`PRE_CHECK`:** Prüft Inventar, SSOT-Wissensdatenbank, Weltgedächtnis und aktuellen Betriebs-Modus (`ALLTAG`, `KAMIKAZE`, `LEIBWAECHTER`).
2. **`TIMING_CALC`:** Berechnet `expected_ticks` und `timeout_ticks` aus SSOT-Blockhärte, Werkzeug-Geschwindigkeit und aktiven `MobEffectPacket`-Werten.
3. **`APPROACH_AND_AIM`:** Interpoliert `Yaw`/`Pitch` weich auf das Ziel und bewegt den Bot via `PlayerAuthInputPacket` in Reichweite (`< 4.5 Blöcke`).
4. **`EXECUTE_LOOP`:** Sendet aktionsspezifische Pakete im `50 ms`-Takt unter permanenter Überwachung durch die Störfall-Pipeline.
5. **`VERIFY_AND_COMMIT`:** Wartet auf die echte Server-Quittung (`UpdateBlock`, `TakeItemEntity`, `ContainerOpen`, `SetEntityLink`), bevor `SUCCESS` gemeldet wird.

## 2. Protokoll-Matrix über alle 7 Handlungsgruppen

| Handlungsgruppe | Daten-Inputs (SSOT / Sensoren) | Ausgehende Pakete (`Bot ➔ Server`) | Timing-Logik | Eingehende Quittung (`Server ➔ Bot`) | Aktive Störfall-Wächter |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Fortbewegung & Körper** | `64×36`-ToF, `Hunger`, `Air`, `BlockFriction` | `PlayerAuthInputPacket` (`UP`, `JUMP`, `SNEAK`, `SPRINT_DOWN`) | `50 ms`-Tick; Sprung nach Terrain-Höhe | `MovePlayerPacket` | `StuckDetector`, `CliffGuard` |
| **2. Blick & Gestik** | Ziel-Augenhöhe, `EmoteList` (SSOT) | `PlayerAuthInputPacket`, `EmotePacket`, `AnimatePacket` | Schwenk `4–12 Ticks`; Nicken `4 Ticks` | Visuell / `GestureTelemetrySensor` | `TargetLostGuard` |
| **3. Welt-Interaktion** | Blockhärte & Tool-Speed (SSOT), Nachbarflächen | `MobEquipment`, `PlayerAction` (`START`/`STOP_BREAK`), `ItemUseOn` | `Ticks = ceil((Härte * 30) / Speed)` | `LevelEventPacket`, `UpdateBlockPacket` | `LineOfSightGuard`, `ReachGuard`, `BreakTimeoutGuard` |
| **4. Inventar & Handwerk** | Rezepte & Brennwerte (SSOT), `ContainerId` | `ItemUseOn`, `ItemStackRequest`, `BookEdit`, `ContainerClose` | Warte auf `ContainerOpen`, dann `1 Action/Tick` | `ContainerOpen`, `InventoryContent` | `ContainerClosedGuard`, `MissingIngredientGuard` |
| **5. Überleben & Kampf** | Waffen-Cooldown & Essensdauer (SSOT), Hitbox | `MobEquipment`, `InventoryTransaction`, `PlayerAuthInput` (`SNEAK`) | Essen `32 Ticks`; Nahkampf `10–16 Ticks`; Bogen `20 Ticks` | `EntityEvent`, `UpdateAttributes` | `CreeperSwellGuard`, `LowHealthGuard` |
| **6. Spezial-Rollen** | Futter- & Gilden-Handelstabellen (SSOT) | `ItemUse`, `Interact`, `TradeRequest` | Angeln ereignisgesteuert; Handel `2–4 Ticks` | `EntityEvent` (`FISH_HOOK`), `UpdateTrade` | `HostileProximityGuard`, `TradeOutOfStockGuard` |
| **7. Tierführung & Logistik** | Zähm-Items & Mount-Hitbox (SSOT), Leash (`10 Blöcke`) | `Interact` (`MOUNT`/`LEASH`), `PlayerAuthInput` (`JUMP`/`SNEAK`) | Zähmen bis `HEART_PARTICLES`; Ziehen kontinuierlich | `SetEntityLinkPacket`, `MoveEntityAbsolute` | `LeashTensionGuard` (`> 7.5 Blöcke`), `MountHitboxGuard` |
```

## vault/konzept/2026-11-20 anlage-07-referenz-minecraft-ui-design-system-und-wireframes.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 07 (Referenz): VoxelCrew Minecraft-UI-Design-System & Wireframes

## 1. CSS-Design-Tokens (`voxelcrew-theme.css`)
```css
:root {
  --vc-bg-slate: #1e222a;
  --vc-panel-bg: #2b303a;
  --vc-bevel-light: #6a758a;
  --vc-bevel-dark: #14171c;
  --vc-emerald: #17dd62;
  --vc-redstone: #e53935;
  --vc-frontier-gold: #ffca28;
  --vc-radius: 0px;
}

.mc-panel {
  background: var(--vc-panel-bg);
  border: 3px solid var(--vc-bevel-dark);
  box-shadow: inset 3px 3px 0 var(--vc-bevel-light), inset -3px -3px 0 var(--vc-bevel-dark);
  border-radius: var(--vc-radius);
}
```

## 2. Wireframe: Zentrale 2.5D-Schichten-Weltkarte (inkl. Frontier-Platzhalter)
```text
+-----------------------------------------------------------------------------------+
| [VoxelCrew Logo]  [2.5D-Karte] [Crew-Bots] [Baupläne] [Werkstatt]   [Hilfe (1)]   |
+-----------------------------------+-----------------------------------------------+
| EBENEN-SCHALTER (2.5D-HÖHEN)      | ZENTRALE 2.5D-WELTKARTE (Oberwelt)            |
| ( ) Gipfel (Y > 100)              |                                               |
| (*) Oberfläche (Y 50..100)        |   [Settlers-Hafen] ~~~~~ (Truhenboot-Route)   |
| ( ) Obere Höhlen (Y 0..49)        |          |                                    |
| ( ) Tiefe Höhlen (Y -64..-1)      |   [Schacht-Aufzug Y:64 -> Y:-32]              |
| [ ] Röntgen-Blick (Alle Ebenen)   |          : (gestrichelte Tiefen-Route)        |
|                                   |          :---> [Delvers-Halle (Y: -32)]       |
| KARTEN-FILTER                     |                                               |
| [x] Crew-Bots & Relay-Spieler     |   [Bot Kalle: Eskorte aktiv]                  |
| [x] Block-Muster (Orte)           +-----------------------------------------------+
| [x] VoxelCrew Frontier (Gilden)   | ROUTEN-QUERSCHNITT (Höhenprofil)              |
| [ ] Spieler-Stämme (Später)       | Y+68 [Settlers] \___                          |
|                                   | Y-32                \___ [Delvers-Siedlung]   |
+-----------------------------------+-----------------------------------------------+
```
```

## vault/konzept/2026-11-20 anlage-08-referenz-neutralino-ui-workflows-und-wizards.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 08 (Referenz): NeutralinoJS-App-Lifecycle, Wizards & Dialog-System

## 1. Die 5 geführten Schritt-für-Schritt-Wizards (ab 10 Jahren)
1. **Erststart- & Portabilitäts-Wizard:** Prüft beim ersten Start auf einem Wechseldatenträger den Icon-Cache, die OS-Plattform (Windows BDS-Option) und die Second-Screen-Fensterposition.
2. **Neuen Crew-Bot erschaffen (Bot-Wizard):** Name & Charakter-Karte ➔ Verbindungsziel ➔ Geführter Konto-Login (falls nötig) ➔ Start-Auftrag.
3. **Co-Pilot- / Relay-Assistent:** Führt in 3 Bild-Karten durch das Verbinden des eigenen Minecraft-Clients über `127.0.0.1` und das Aktivieren von Schutzengel-Regeln.
4. **Kamikaze-Erkundungs-Wizard:** Fragt vor dem Start zwingend eine sichere Lagertruhe auf der 2.5D-Karte ab, bevor der Bot ohne Inventar aufbricht.
5. **Lernen-durch-Zeigen-Assistent:** Begleitet Aufnahme, Stationen-Check und Wiederholungs-Zeitplan.

## 2. Dialog-Klassen & NeutralinoJS-Lifecycle
* **Achievement-Toasts:** Minecraft-Erfolgsbanner oben rechts bei neu erkannten Block-Mustern oder abgewehrten Gefahren.
* **Fehler-Übersetzer:** Wandelt Netzwerk- und Protokollfehler in verständliche Karten mit direkten Lösungs-Buttons um.
* **Graceful Shutdown:** Fängt `windowClose` in NeutralinoJS ab, damit laufende Server und Bots sauber speichern, bevor das Fenster schließt.
```

## vault/konzept/2026-11-20 anlage-09-referenz-bds-manager-und-msa-workflows.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 09 (Referenz): Server-Werkstatt (BDS Manager) & MSA-Konto-Workflows

## 1. Server-Werkstatt & Einstellungen (Windows Opt-In-Modul)
* **Erst-Import ohne mitgeliefertes Server-ZIP:** Zeigt bei fehlender Engine eine kindgerechte Import-Karte für `bedrock-server-*.zip`, prüft die `bedrock_server.exe` und entpackt sie portabel nach `./runtime/bds_engine/`.
* **Geführter Server-Update-Wizard:** Erstellt vor jedem Versions-Update ein automatisches Backup von `worlds/`, `server.properties` und allen Packs nach `./data/backups/`, tauscht nur die Engine-Dateien aus und startet anschließend den Analyse-Server zur E2E-Kalibrierung.

## 2. MSA-Konto-Workflow (Pflicht für LAN-Welten & Online-Server)
* **Verbindungs-Matrix:** Da aktuelle Bedrock-Versionen (`26.65+`) auch für lokale LAN-Welten eine gültige Xbox-Live-/MSA-Identität verlangen, prüft die UI vor dem Bot-Start automatisch den Konto-Status. Nur ein eigener lokal betriebener BDS mit `online-mode=false` (oder der interne Test-/Demo-Server) startet ohne Konto.
* **Einmal-Code-Dialog (`ABCD-1234`) & Konto trennen:** Führt mit großem Code und 1-Klick-Browser-Start durch den Device-Code-Flow, kapselt Tokens pro Bot unter `./runtime/auth_cache/<bot_id>/` und bietet einen geschützten **„Konto trennen“**-Button inklusive Doppelbelegungs-Sperre.
```

## vault/konzept/04_referenz-zod-schemas-ipc-semantic-zones.md

```markdown
# Referenzanlage 04: TypeScript- & Zod-Validierungs-Schemas

```typescript
import { z } from "zod";

export const DimensionSchema = z.enum(["overworld", "nether", "the_end"]);
export const Vec3Schema = z.tuple([z.number().int(), z.number().int(), z.number().int()]);

export const IpcEnvelopeSchema = z.object({
  msg_id: z.string().uuid(),
  timestamp: z.number().int(),
  world_context_id: z.string().min(1),
  source_module: z.enum(["LEITSTELLE", "WORLD_MEMORY", "SWARM_BOT", "TRIBES_BP", "UI_CLIENT"]),
  event_type: z.string().min(1),
  auth_token: z.string().min(8),
  payload: z.record(z.unknown())
});

export const RawObservationPayloadSchema = z.object({
  observer_type: z.enum(["SWARM_BOT", "BDS_SCRIPT_API"]),
  observer_id: z.string(),
  dimension: DimensionSchema,
  observation_kind: z.enum(["BLOCK_PLACED", "BLOCK_BROKEN", "ENTITY_SPAWNED", "ENTITY_UPDATED", "ENTITY_REMOVED"]),
  coords: Vec3Schema.optional(),
  block_id: z.string().optional(),
  block_states: z.record(z.union([z.string(), z.number(), z.boolean()])).optional(),
  entity_data: z.object({
    runtime_id: z.string(),
    type_id: z.string(),
    coords: Vec3Schema,
    pose_id: z.number().int().optional(),
    equipment: z.record(z.string()).optional()
  }).optional()
});

export const ZonePatternRuleSchema = z.object({
  pattern_id: z.string(),
  display_name_de: z.string(),
  zone_type: z.string(),
  anchor: z.object({
    kind: z.enum(["BLOCK", "ENTITY"]),
    id: z.string(),
    pose_id: z.number().int().optional(),
    equipment: z.record(z.string()).optional()
  }),
  constraints: z.array(z.object({
    kind: z.enum(["BLOCK", "ENTITY"]),
    id: z.string(),
    max_distance: z.number().positive(),
    relative_offset: Vec3Schema.optional(),
    state: z.record(z.union([z.string(), z.number(), z.boolean()])).optional()
  })),
  zone_definition: z.object({
    radius: z.number().positive(),
    tribes_role: z.string().optional(),
    bot_behavior: z.string().optional()
  })
});

export const BotDisplacedAnomalySchema = z.object({
  bot_id: z.string(),
  dimension: DimensionSchema,
  old_coords: Vec3Schema,
  new_coords: Vec3Schema,
  distance_blocks: z.number(),
  inventory_valuable: z.boolean(),
  has_home_bed: z.boolean()
});
```
```

## vault/konzept/05_referenz-bds-script-api-memory-bridge.md

```markdown
# Referenzanlage 05: BDS Script-API Memory-Bridge (`BehaviourPack`)

Ermöglicht dem Shared Weltgedächtnis den Empfang von Block- und Block-Muster-Änderungen auch im botlosen `HEADLESS_TRIBES_SERVER`-Betrieb über `@minecraft/server-net`.

### `manifest.json`
```json
{
  "format_version": 2,
  "header": {
    "name": "Tribes & WorldMemory Bridge",
    "description": "Sendet Welt-Telemetrie an das lokale Shared Weltgedächtnis (Bedrock 26.65+)",
    "uuid": "8c1e4a90-3b2d-4f11-9a8e-71c2b0e4d901",
    "version": [1, 0, 0],
    "min_engine_version": [1, 21, 60]
  },
  "modules": [
    {
      "type": "script",
      "language": "javascript",
      "uuid": "8c1e4a90-3b2d-4f11-9a8e-71c2b0e4d902",
      "version": [1, 0, 0],
      "entry": "scripts/memory_bridge.js"
    }
  ],
  "dependencies": [
    { "module_name": "@minecraft/server", "version": "1.17.0" },
    { "module_name": "@minecraft/server-net", "version": "1.0.0-beta" }
  ]
}
```

### `scripts/memory_bridge.js`
```javascript
import { world, system } from "@minecraft/server";
import { http, HttpRequest, HttpRequestMethod, HttpHeader } from "@minecraft/server-net";

const MEMORY_INGEST_URL = "http://127.0.0.1:18080/api/v1/observations";
const AUTH_TOKEN = "local-portable-session-token";
const WORLD_CONTEXT_ID = "production_main";

const outbox = [];

function enqueueObservation(payload) {
  outbox.push({
    msg_id: `${Date.now()}-${Math.random().toString(36).slice(2, 10)}`,
    timestamp: Date.now(),
    world_context_id: WORLD_CONTEXT_ID,
    source_module: "TRIBES_BP",
    event_type: "world.raw_observation",
    auth_token: AUTH_TOKEN,
    payload
  });
}

world.afterEvents.playerPlaceBlock.subscribe((ev) => {
  const { block, dimension } = ev;
  enqueueObservation({
    observer_type: "BDS_SCRIPT_API",
    observer_id: "bds_server",
    dimension: dimension.id.replace("minecraft:", ""),
    observation_kind: "BLOCK_PLACED",
    coords: [block.location.x, block.location.y, block.location.z],
    block_id: block.typeId,
    block_states: block.permutation.getAllStates()
  });
});

world.afterEvents.playerBreakBlock.subscribe((ev) => {
  const { block, brokenBlockPermutation, dimension } = ev;
  enqueueObservation({
    observer_type: "BDS_SCRIPT_API",
    observer_id: "bds_server",
    dimension: dimension.id.replace("minecraft:", ""),
    observation_kind: "BLOCK_BROKEN",
    coords: [block.location.x, block.location.y, block.location.z],
    block_id: brokenBlockPermutation.type.id
  });
});

world.afterEvents.entitySpawn.subscribe((ev) => {
  const { entity } = ev;
  if (entity.typeId !== "minecraft:armor_stand") return;
  enqueueObservation({
    observer_type: "BDS_SCRIPT_API",
    observer_id: "bds_server",
    dimension: entity.dimension.id.replace("minecraft:", ""),
    observation_kind: "ENTITY_SPAWNED",
    entity_data: {
      runtime_id: entity.id,
      type_id: entity.typeId,
      coords: [Math.floor(entity.location.x), Math.floor(entity.location.y), Math.floor(entity.location.z)]
    }
  });
});

system.runInterval(async () => {
  if (outbox.length === 0) return;
  const batch = outbox.splice(0, 50);
  const req = new HttpRequest(MEMORY_INGEST_URL);
  req.method = HttpRequestMethod.Post;
  req.headers = [new HttpHeader("Content-Type", "application/json")];
  req.body = JSON.stringify({ batch });
  try {
    await http.request(req);
  } catch (_) {
    // Re-Queue bei kurzzeitigem Verbindungsabbruch
    outbox.unshift(...batch);
  }
}, 10);
```
```

## vault/konzept/06_referenz-fast-voxel-raycaster-tof.md

```markdown
# Referenzanlage 06: Fast-Voxel-Raycaster & ToF-Kanten-Detektor (`64×36`)

```typescript
export interface ToFGridResult {
  width: 64;
  height: 36;
  depthLayer: Float32Array;       // Layer A: Distanz in Blöcken (999.0 = Himmel)
  categoryLayer: Uint8Array;      // Layer B: 0=Himmel, 1=Natur, 2=Wasser/Lava, 3=Künstlich, 4=Nahrung
  hitCoordsLayer: Int32Array;     // Layer C: [x, y, z] pro Pixel (Länge: 64 * 36 * 3)
  cliffEdgeMask: Uint8Array;      // 1 = Abgrund > 3 Blöcke erkannt
}

export type BlockLookupFn = (x: number, y: number, z: number) => { isSolid: boolean; category: number };

export function castToFFrame(
  eyeX: number, eyeY: number, eyeZ: number,
  yawRad: number, pitchRad: number,
  maxDist: number,
  lookup: BlockLookupFn
): ToFGridResult {
  const W = 64, H = 36;
  const depthLayer = new Float32Array(W * H);
  const categoryLayer = new Uint8Array(W * H);
  const hitCoordsLayer = new Int32Array(W * H * 3);
  const cliffEdgeMask = new Uint8Array(W * H);

  const fx = -Math.sin(yawRad) * Math.cos(pitchRad);
  const fy = -Math.sin(pitchRad);
  const fz = Math.cos(yawRad) * Math.cos(pitchRad);

  const rx = Math.cos(yawRad), ry = 0, rz = Math.sin(yawRad);
  const ux = ry * fz - rz * fy;
  const uy = rz * fx - rx * fz;
  const uz = rx * fy - ry * fx;

  const tanHalfFov = Math.tan((80 * Math.PI) / 360);
  const aspect = W / H;

  for (let py = 0; py < H; py++) {
    const ndcY = (1 - (2 * (py + 0.5)) / H) * tanHalfFov;
    for (let px = 0; px < W; px++) {
      const ndcX = ((2 * (px + 0.5)) / W - 1) * tanHalfFov * aspect;
      let dx = fx + rx * ndcX + ux * ndcY;
      let dy = fy + ry * ndcX + uy * ndcY;
      let dz = fz + rz * ndcX + uz * ndcY;
      const len = Math.hypot(dx, dy, dz);
      dx /= len; dy /= len; dz /= len;

      let x = Math.floor(eyeX), y = Math.floor(eyeY), z = Math.floor(eyeZ);
      const stepX = dx >= 0 ? 1 : -1, stepY = dy >= 0 ? 1 : -1, stepZ = dz >= 0 ? 1 : -1;
      const tDeltaX = dx !== 0 ? Math.abs(1 / dx) : 1e9;
      const tDeltaY = dy !== 0 ? Math.abs(1 / dy) : 1e9;
      const tDeltaZ = dz !== 0 ? Math.abs(1 / dz) : 1e9;

      let tMaxX = dx !== 0 ? ((dx > 0 ? x + 1 - eyeX : eyeX - x) * tDeltaX) : 1e9;
      let tMaxY = dy !== 0 ? ((dy > 0 ? y + 1 - eyeY : eyeY - y) * tDeltaY) : 1e9;
      let tMaxZ = dz !== 0 ? ((dz > 0 ? z + 1 - eyeZ : eyeZ - z) * tDeltaZ) : 1e9;

      let dist = 0, hitCat = 0;
      while (dist <= maxDist) {
        const info = lookup(x, y, z);
        if (info.isSolid || info.category === 2) {
          hitCat = info.category;
          break;
        }
        if (tMaxX < tMaxY) {
          if (tMaxX < tMaxZ) { dist = tMaxX; tMaxX += tDeltaX; x += stepX; }
          else { dist = tMaxZ; tMaxZ += tDeltaZ; z += stepZ; }
        } else {
          if (tMaxY < tMaxZ) { dist = tMaxY; tMaxY += tDeltaY; y += stepY; }
          else { dist = tMaxZ; tMaxZ += tDeltaZ; z += stepZ; }
        }
      }

      const idx = py * W + px;
      if (hitCat === 0) {
        depthLayer[idx] = 999.0;
        categoryLayer[idx] = 0;
      } else {
        depthLayer[idx] = dist;
        categoryLayer[idx] = hitCat;
        hitCoordsLayer[idx * 3] = x;
        hitCoordsLayer[idx * 3 + 1] = y;
        hitCoordsLayer[idx * 3 + 2] = z;
      }
    }
  }

  // Vertikaler ToF-Gradient für Fallschaden-/Abgrund-Erkennung (> 3 Blöcke)
  for (let py = 0; py < H - 1; py++) {
    for (let px = 0; px < W; px++) {
      const idx = py * W + px;
      const idxBelow = (py + 1) * W + px;
      if (depthLayer[idxBelow] < 12 && (depthLayer[idx] - depthLayer[idxBelow]) > 3.5) {
        cliffEdgeMask[idx] = 1;
      }
    }
  }

  return { width: 64, height: 36, depthLayer, categoryLayer, hitCoordsLayer, cliffEdgeMask };
}
```
```

## vault/konzept/07_referenz-reverse-dependency-matcher-sqlite.md

```markdown
# Referenzanlage 07: Reverse-Dependency-Matcher & Semantic-Zone-Engine

```typescript
import Database from "better-sqlite3";

export class SemanticZoneEngine {
  private db: Database.Database;
  private readonly GRACE_PERIOD_MS = 30_000;

  constructor(db: Database.Database) {
    this.db = db;
  }

  public onBlockBroken(worldContextId: string, dimension: string, x: number, y: number, z: number): string[] {
    const affectedZones = this.db.prepare(`
      SELECT DISTINCT sz.zone_id, sz.status
      FROM semantic_zones sz
      JOIN semantic_zone_dependencies dep ON sz.zone_id = dep.zone_id
      WHERE sz.world_context_id = ? AND sz.dimension = ?
        AND dep.kind = 'BLOCK' AND dep.x = ? AND dep.y = ? AND dep.z = ?
        AND sz.status = 'ACTIVE'
    `).all(worldContextId, dimension, x, y, z) as { zone_id: string; status: string }[];

    const suspendedIds: string[] = [];
    const now = Date.now();
    const updateStmt = this.db.prepare(`
      UPDATE semantic_zones SET status = 'SUSPENDED', suspended_since = ? WHERE zone_id = ?
    `);

    for (const row of affectedZones) {
      updateStmt.run(now, row.zone_id);
      suspendedIds.push(row.zone_id);
    }
    return suspendedIds;
  }

  public sweepExpiredSuspensions(): string[] {
    const cutoff = Date.now() - this.GRACE_PERIOD_MS;
    const expired = this.db.prepare(`
      SELECT zone_id FROM semantic_zones WHERE status = 'SUSPENDED' AND suspended_since <= ?
    `).all(cutoff) as { zone_id: string }[];

    const dissolveStmt = this.db.prepare(`
      UPDATE semantic_zones SET status = 'DISSOLVED' WHERE zone_id = ?
    `);

    const dissolvedIds: string[] = [];
    for (const row of expired) {
      dissolveStmt.run(row.zone_id);
      dissolvedIds.push(row.zone_id);
    }
    return dissolvedIds;
  }
}
```
```

## vault/konzept/08_referenz-bds-behaviourpack-ingame-ui.md

```markdown
# Referenzanlage 08: Natives In-Game-UI (`@minecraft/server-ui`) für Produktivbetrieb

Bietet kindgerechte Formular-Dialoge (`ActionFormData`, `ModalFormData`) direkt im Minecraft-Client, sobald das `BehaviourPack` aktiv ist.

```javascript
import { world } from "@minecraft/server";
import { ActionFormData, ModalFormData } from "@minecraft/server-ui";

// Kompass öffnet das Leitstellen-Hauptmenü im Spiel
world.afterEvents.itemUse.subscribe(async (ev) => {
  const { source: player, itemStack } = ev;
  if (itemStack.typeId !== "minecraft:compass") return;

  const form = new ActionFormData()
    .title("§l§2Leitstelle & Schwarm-Menü")
    .body("Was möchtest du tun?")
    .button("§lMeine Bots rufen & steuern", "textures/items/compass_item")
    .button("Block-Muster-Rezeptbuch ansehen", "textures/blocks/hay_block_side")
    .button("§lNeues Gebiet erkunden lassen", "textures/items/map_filled")
    .button("§lOffene Bot-Fragen (Postfach)", "textures/items/book_writable");

  const res = await form.show(player);
  if (res.canceled) return;
  if (res.selection === 2) {
    await openExploreDialog(player);
  }
});

async function openExploreDialog(player) {
  const modal = new ModalFormData()
    .title("Gebiet erkunden")
    .slider("Such-Radius (Blöcke)", 50, 500, 50, 150)
    .toggle("Kamikaze-Aufklärung erlauben (Inventar vorher sichern!)", false);

  const res = await modal.show(player);
  if (res.canceled) return;
  const [radius, kamikaze] = res.formValues;
  player.sendMessage(`§a[Leitstelle] Erkundungsauftrag gestartet (Radius: ${radius} Blöcke, Kamikaze: ${kamikaze ? "JA" : "NEIN"}).`);
}
```
```

## vault/konzept/09_referenz-bds-behaviourpack-test-demo-ui.md

```markdown
# Referenzanlage 09: In-Game-UIs für `E2E_TEST_SERVER` & `DEMO_SHOWCASE_SERVER`

```javascript
import { world } from "@minecraft/server";
import { ActionFormData, ModalFormData } from "@minecraft/server-ui";

// Emerald = E2E-Test-Fernbedienung | Uhr = Showcase-Regie-Fernbedienung
world.afterEvents.itemUse.subscribe(async (ev) => {
  const { source: player, itemStack } = ev;

  if (itemStack.typeId === "minecraft:emerald") {
    const e2eMenu = new ActionFormData()
      .title("§l§bE2E-Test-Server Konsole")
      .body("Wähle eine Test- oder Kalibrierungsaktion:")
      .button("Vanilla-Struktur vor mir aufbauen")
      .button("Werkzeug-Abbauzeiten messen")
      .button("Weltgedächtnis-Snapshot auslösen");
    await e2eMenu.show(player);
  }

  if (itemStack.typeId === "minecraft:clock") {
    const demoMenu = new ActionFormData()
      .title("§l§6Vorführungs-Fernbedienung")
      .body("Welche Geschichte sollen die Bots vorführen?")
      .button("Szene 1: Kalle, Bruno & der brennende Ofen")
      .button("Szene 2: Geheimnis des Handelslager-Block-Musters")
      .button("Szene 3: Die mutige Karawanen-Eskorte")
      .button("§l§aAbnahme-Feedback abgeben");
    const res = await demoMenu.show(player);
    if (!res.canceled && res.selection === 3) {
      await openAcceptanceReviewForm(player);
    }
  }
});

async function openAcceptanceReviewForm(player) {
  const review = new ModalFormData()
    .title("Abnahme & Feedback")
    .dropdown("Welche Szene bewertest du?", ["Szene 1: Seetang-Ofen", "Szene 2: Handelslager-Block-Muster", "Szene 3: Karawanen-Eskorte"], 0)
    .slider("Wie gut hat es geklappt? (1 = Fehler, 5 = Super!)", 1, 5, 1, 5)
    .toggle("Darf das Feature so freigegeben werden?", true)
    .textField("Was sollen die Bots noch besser machen?", "Hier Wunsch eingeben...");

  const res = await review.show(player);
  if (!res.canceled) {
    player.sendMessage("§a[Abnahme] Danke! Dein Feedback wurde direkt in die Entwicklungs-Queue gespeichert.");
  }
}
```
```

## vault/konzept/10_referenz-showcase-regie-und-review-schema.md

```markdown
# Referenzanlage 10: Regie-Drehbuch (`showcase_script.json`) & Abnahme-Feedback (`review_submissions`)

## 1. JSON-Schema & Beispiel für das Showcase-Drehbuch (`showcase_script.json`)
Jede Vorführung auf dem `DEMO_SHOWCASE_SERVER` wird vom System autonom als strukturiertes Regie-Drehbuch erzeugt und vorab im `E2E_TEST_SERVER` trocken getestet.

```json
{
  "showcase_id": "showcase_kelp_loop_v1",
  "title": "Kalle, Bruno und der verstopfte Seetang-Ofen",
  "target_age_min": 10,
  "learning_goal": "Zeigt Lernen durch Zeigen (Workflow-Rekorder) und Charakter-Unterschiede",
  "stage": {
    "template_id": "stage_coastal_hut_32x32",
    "anchor_coords": [100, 64, 100],
    "props": [
      { "id": "struct_smoker_01", "block": "minecraft:smoker", "offset": [4, 1, 6], "initial_items": { "output": ["minecraft:dried_kelp", 64] } },
      { "id": "struct_crafting_01", "block": "minecraft:crafting_table", "offset": [6, 1, 6] },
      { "id": "struct_chest_01", "block": "minecraft:chest", "offset": [8, 1, 6], "initial_items": { "slot_0": ["minecraft:writable_book", 1] } }
    ]
  },
  "cast": [
    {
      "actor_id": "bot_kalle",
      "display_name": "Kalle (Lehrling)",
      "profile": "scout_friendly.json",
      "spawn_offset": [3, 1, 3],
      "costume": { "head": "minecraft:leather_helmet" }
    },
    {
      "actor_id": "bot_bruno",
      "display_name": "Bruno (Profi)",
      "profile": "worker_diligent.json",
      "spawn_offset": [10, 1, 3],
      "costume": { "head": "minecraft:iron_helmet" }
    }
  ],
  "acts": [
    {
      "act_number": 1,
      "name": "Das Chaos am Ofen",
      "camera_cue": { "offset": [6, 5, -2], "look_at_offset": [6, 2, 6] },
      "subtitles": "Akt 1: Kalle will helfen, weiß aber noch nicht, wie man Seetang-Blöcke presst...",
      "actions": [
        { "actor": "bot_kalle", "cmd": "CHAT", "text": "Oh nein! Der Räucherofen platzt gleich vor lauter Seetang!" },
        { "actor": "bot_kalle", "cmd": "EMOTE", "emote": "panic_wave" }
      ]
    },
    {
      "act_number": 2,
      "name": "Bruno zeigt wie es geht",
      "camera_cue": { "offset": [7, 4, 1], "look_at_offset": [6, 2, 6] },
      "subtitles": "Akt 2: Kalle schaut genau zu, während Bruno den Ablauf vormacht.",
      "actions": [
        { "actor": "bot_kalle", "cmd": "START_WORKFLOW_WATCH", "target_actor": "bot_bruno" },
        { "actor": "bot_bruno", "cmd": "EXECUTE_WORKFLOW", "workflow_id": "kelp_fuel_loop" }
      ]
    },
    {
      "act_number": 3,
      "name": "Meisterleistung & Eintrag ins Logbuch",
      "camera_cue": { "offset": [8, 4, 2], "look_at_offset": [8, 2, 6] },
      "subtitles": "Akt 3: Kalle hat den Ablauf gelernt und trägt alles stolz ins Buch ein!",
      "actions": [
        { "actor": "bot_kalle", "cmd": "CHAT", "text": "Hab's kapiert! Alles in der Kiste verstaut und mit Datum notiert!" },
        { "actor": "bot_kalle", "cmd": "EMOTE", "emote": "clap_celebrate" }
      ]
    }
  ]
}
```

---

## 2. SQLite-Schema für die geführte Endabnahme (`review_submissions`)
Alle Rückmeldungen aus den In-Game-UI-Masken (`@minecraft/server-ui`) und dem Second-Screen-UI werden strukturiert in der Leitstellen-Datenbank abgelegt und automatisch in neue Entwicklungs-Tasks überführt:

```sql
CREATE TABLE review_submissions (
    review_id TEXT PRIMARY KEY,
    showcase_id TEXT NOT NULL,
    world_context_id TEXT NOT NULL,
    reviewer_name TEXT NOT NULL,
    review_channel TEXT NOT NULL,      -- "INGAME_UI" | "SECOND_SCREEN_UI"
    act_number INTEGER,                -- Optionaler Bezug auf bestimmten Akt
    rating_score INTEGER NOT NULL,     -- 1 bis 5 Sterne
    approved BOOLEAN NOT NULL,         -- 1 = Freigegeben, 0 = Nacharbeit erforderlich
    category_tag TEXT,                 -- "PATHFINDING", "DIALOG_HUMOR", "TIMING", "UI_UX", "BUG"
    feedback_text TEXT,
    generated_task_id TEXT,            -- Verknüpfung zum automatisch erzeugten Fix-Task
    created_at INTEGER NOT NULL
);
```
```

## vault/konzept/2026-11-19 anlage-04-referenz-action-executor-und-guards.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 04 (Referenz): 50-ms-Action-Executor & Modulare Störfall-Wächter (TypeScript)

```typescript
export type OperatingMode = "ALLTAG" | "KAMIKAZE" | "LEIBWAECHTER";

export interface GuardResult {
  triggered: boolean;
  guardId: string;
  action: "CONTINUE" | "ADJUST_STEP" | "PAUSE_AND_RECOVER" | "ABORT_TO_GOAP";
  reason?: string;
}

export interface FailureGuard {
  id: string;
  enabledInModes: OperatingMode[];
  evaluate(ctx: ExecutionTickContext): GuardResult;
}

export interface ExecutionTickContext {
  tick: number;
  mode: OperatingMode;
  botPos: { x: number; y: number; z: number };
  targetPos?: { x: number; y: number; z: number };
  targetDistance: number;
  lineOfSightBlockedByEntity: boolean;
  tofDropAhead: number;
  leashedEntityDistance?: number;
  elapsedTicks: number;
  timeoutTicks: number;
}

export const CliffGuard: FailureGuard = {
  id: "guard_cliff_protection",
  enabledInModes: ["ALLTAG", "LEIBWAECHTER"], // Im KAMIKAZE-Modus bewusst deaktiviert
  evaluate(ctx) {
    if (ctx.tofDropAhead > 3.0) {
      return {
        triggered: true,
        guardId: this.id,
        action: "ADJUST_STEP",
        reason: `Abgrund von ${ctx.tofDropAhead.toFixed(1)} Blöcken erkannt – aktiviere Sneak/Stopp.`
      };
    }
    return { triggered: false, guardId: this.id, action: "CONTINUE" };
  }
};

export const LineOfSightGuard: FailureGuard = {
  id: "guard_line_of_sight",
  enabledInModes: ["ALLTAG", "KAMIKAZE", "LEIBWAECHTER"],
  evaluate(ctx) {
    if (ctx.lineOfSightBlockedByEntity) {
      return {
        triggered: true,
        guardId: this.id,
        action: "PAUSE_AND_RECOVER",
        reason: "Spieler oder Tier steht in der Schlaglinie – warte oder weiche 1 Block seitlich aus."
      };
    }
    return { triggered: false, guardId: this.id, action: "CONTINUE" };
  }
};

export const LeashTensionGuard: FailureGuard = {
  id: "guard_leash_tension",
  enabledInModes: ["ALLTAG", "LEIBWAECHTER"],
  evaluate(ctx) {
    if (ctx.leashedEntityDistance !== undefined && ctx.leashedEntityDistance > 7.5) {
      return {
        triggered: true,
        guardId: this.id,
        action: "PAUSE_AND_RECOVER",
        reason: `Leinenabstand ${ctx.leashedEntityDistance.toFixed(1)} Blöcke – drossele Tempo für Tier/Truhenboot.`
      };
    }
    return { triggered: false, guardId: this.id, action: "CONTINUE" };
  }
};
```
```

## vault/konzept/2026-11-19 anlage-05-referenz-flugschreiber-telemetry.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 05 (Referenz): Bot-Flugschreiber (60s-Ringpuffer) & What-If-Re-Simulation

```typescript
export interface FlightRecorderFrame {
  tick: number;
  timestampMs: number;
  mode: "ALLTAG" | "KAMIKAZE" | "LEIBWAECHTER";
  sense: {
    tofGridBase64: string;
    health: number;
    hunger: number;
    air: number;
    activeGestures: string[];
  };
  think: {
    selectedGoal: string;
    candidates: Array<{ goal: string; score: number }>;
  };
  act: {
    yaw: number;
    pitch: number;
    pressedInputs: string[];
    activePrimitive: string;
  };
  verify: {
    status: "OK" | "GUARD_ADJUSTED" | "ABORTED";
    triggeredGuard?: string;
    note?: string;
  };
}

export class BotFlightRecorder {
  private readonly maxFrames: number;
  private buffer: FlightRecorderFrame[] = [];

  constructor(bufferSeconds = 60, tickRateHz = 20) {
    this.maxFrames = bufferSeconds * tickRateHz;
  }

  public recordFrame(frame: FlightRecorderFrame): void {
    if (this.buffer.length >= this.maxFrames) {
      this.buffer.shift();
    }
    this.buffer.push(frame);
  }

  public exportClip(lastSeconds = 30): FlightRecorderFrame[] {
    const count = Math.min(this.buffer.length, lastSeconds * 20);
    return this.buffer.slice(-count);
  }
}
```
```

## vault/konzept/2026-11-19 anlage-06-referenz-isometric-icon-generator.md

```markdown
---
typ: inbox
tags: [inbox, beispiel]
status: bereit
stand: 2026-11-19
beispiel: true
---

# Anlage 06 (Referenz): Resource-Pack-Layering & Isometrischer 3D-Icon-Generator

## Architektur der Icon-Pipeline
1. **Layer-Auflösung:** Basis-Texturen aus `./runtime/bds_engine/resource_packs/vanilla/` werden mit welt-spezifischen `PACK_CUSTOM`-Paketen aus `ResourcePacksInfoPacket` überlagert (`terrain_texture.json`, `item_texture.json`, `blocks.json`).
2. **Geometrie-Klassen:**
   * `FLAT_ITEM`: Nearest-Neighbor-Skalierung auf `64×64` PNG.
   * `CUBE_FULL`: Isometrische Projektion aus `top` (100 % Helligkeit), `north` (80 %) und `west` (60 %) inklusive Biom-Tint-Multiplikator.
   * `PARAMETRIC_SHAPE`: Vordefinierte Sub-Box-UV-Schnitte für `STAIRS`, `SLAB`, `FENCE`, `WALL` und `DOOR`.
   * `CUSTOM_GEO`: Parser für Bedrock-`.geo.json`-Modelle aus Zusatz-Paketen.
3. **Cache-Ablage:** Speicherung unter `./data/icon_cache/<pack_hash>/<block_or_item_id>.png` für sofortiges Laden in der NeutralinoJS-Oberfläche ohne GPU-Last.
```
