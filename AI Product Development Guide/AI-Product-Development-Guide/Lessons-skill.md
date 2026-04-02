# Lessons.skill

---

## name: lessons
description: >
Persistent self-learning memory system for AI agents. Use this skill whenever
you are working in a project that has a tasks/lessons.md file, or whenever
the user mentions "lessons", "learnings", "agent memory", "feedback loop",
or "self-learning system". Also trigger when the user asks the agent to
remember mistakes, avoid repeating errors, or improve over time. After any
task where something unexpected happened or failed, always consult this skill
to write a structured lesson. Before starting complex tasks, always consult
this skill to load relevant past lessons.

# Lessons — Persistentes Selbstlern-System

Dieses Skill beschreibt, wie ein Agent sein eigenes Wissen persistent speichert,
strukturiert und wiederverwende. Die Datei `tasks/lessons.md` ist der Datenspeicher;
dieses [SKILL.md](http://skill.md/) ist das Interface.

---

## Wann dieses Skill verwenden

| Situation | Aktion |
| --- | --- |
| Vor einer komplexen Aufgabe | Relevante Lessons laden (nach Tags filtern) |
| Nach einem Fehler oder unerwarteter Schwierigkeit | Neuen Eintrag schreiben |
| Ein bestehendes Learning bestätigt sich erneut | Confidence erhöhen |
| Neues Learning widerspricht einem bestehenden | Konflikt markieren |

---

## Eintrag-Format

Jeder Eintrag **muss** diesem Schema folgen — Freitext ist nicht erlaubt:

```markdown
## [KATEGORIE] Kurzer Titel des Problems

- **Datum:** YYYY-MM-DD
- **Kontext:** In welchem Projekt / welcher Technologie trat das Problem auf?
- **Falsche Annahme:** Was hat der Agent falsch angenommen oder gemacht?
- **Korrekte Lösung:** Was ist die richtige Vorgehensweise?
- **Beispiel:** (optional, Code-Snippet falls hilfreich)
- **Confidence:** 🔴 Neu | 🟡 Bestätigt (2×) | 🟢 Zuverlässig (3×+)
- **Geltungsbereich:** `global` | `projekt:NAME`
- **Tags:** #technologie #thema
```

Vollständige Kategorienliste und Beispieleinträge: siehe `references/format.md`

---

## Regeln für den Agenten

### Vor einer Aufgabe

1. Prüfe ob `tasks/lessons.md` existiert.
2. Extrahiere die relevanten Tags aus der aktuellen Aufgabe.
3. Lade nur Einträge, deren Tags zur Aufgabe passen — nie die gesamte Datei blind in den Kontext laden.

### Nach einer Aufgabe

1. Gab es einen Fehler, eine falsche Annahme oder eine unerwartete Schwierigkeit?
    - **Nein** → Nichts tun.
    - **Ja** → Weiter mit Schritt 2.
2. Existiert bereits ein ähnlicher Eintrag?
    - **Nein** → Neuen Eintrag mit Confidence 🔴 schreiben.
    - **Ja, bestätigt** → Confidence erhöhen (🔴 → 🟡 → 🟢).
    - **Ja, widerspricht** → Konflikt markieren (siehe unten), nicht überschreiben.
3. Triviale oder einmalige Situationen **nicht** als Lesson erfassen.

### Widerspruchserkennung

Wenn ein neues Learning einem bestehenden widerspricht, **niemals** stillschweigend überschreiben:

```markdown
## ⚠️ KONFLIKT – [KATEGORIE] Titel

Neues Learning widerspricht Eintrag vom YYYY-MM-DD.
Bitte manuell auflösen.

- **Bestehendes:** ...
- **Neues:** ...
```

---

## Confidence-Stufen

| Symbol | Bedeutung | Verhalten |
| --- | --- | --- |
| 🔴 Neu | Einmalig beobachtet, nicht bestätigt | Wartet auf menschliche Bestätigung |
| 🟡 Bestätigt | Zweimal unabhängig bestätigt | Wird als Referenz verwendet |
| 🟢 Zuverlässig | Dreimal oder öfter bestätigt | Hohes Vertrauen, aktiv anwenden |

Einträge mit 🔴 werden nicht als definitive Wahrheit behandelt bis ein Mensch sie bestätigt hat.

---

## Scope

| Scope | Wann | Beispiel |
| --- | --- | --- |
| `global` | Gilt für alle Projekte | SVG in React Native braucht Library |
| `projekt:NAME` | Nur für ein bestimmtes Projekt | Dieser Client will keine TypeScript Generics |

---

## [AGENTS.md](http://agents.md/) Snippet

Diesen Block in die `AGENTS.md` des Projekts eintragen:

```markdown
### Lessons-System

Dieses Projekt verwendet ein persistentes Lernregister unter `tasks/lessons.md`.

**Vor jeder komplexen Aufgabe:**
- Relevante Einträge aus `tasks/lessons.md` nach Tags laden und berücksichtigen.

**Nach jeder Aufgabe mit Fehler oder unerwarteter Schwierigkeit:**
- Strukturierten Eintrag nach dem Schema in `tasks/lessons.md` schreiben.
- Bestehende Einträge auf Überschneidungen prüfen und Confidence ggf. erhöhen.
- Bei Widersprüchen: Konflikt markieren, nicht überschreiben.

Eintragsformat und Kategorien: siehe SKILL.md des `lessons`-Skills.
```

---

## Kontext-Effizienz

Die Datei wächst mit der Zeit. Immer nach Tags filtern:

```
Aktuelle Aufgabe: React Native Navigation
→ Laden: #react-native #navigation
→ Ignorieren: #typescript #api #tooling
```

Für vollständige Formatreferenz und Beispieleinträge: `references/format.md`