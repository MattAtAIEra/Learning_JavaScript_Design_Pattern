<p align="center">
  <img src="banner.svg" alt="Design Patterns for AI Agents" width="100%"/>
</p>

<p align="center">
  <strong>Bringen Sie Ihrem KI-Agenten bei, Code zu schreiben, der Bestand hat — ohne ihn auszubremsen.</strong>
</p>

<p align="center">
  <a href="README.md">English</a> · <a href="README_zh.md">繁體中文</a> · <a href="README_ja.md">日本語</a> · <a href="README_ko.md">한국어</a>
</p>

---

## Das Problem, über das niemand spricht

KI-gestütztes Programmieren ist schnell. Unglaublich schnell. Aber Geschwindigkeit ohne Struktur erzeugt versteckte Kosten:

> *„Der Code funktioniert, aber niemand kann ihn warten — nicht einmal die KI, die ihn geschrieben hat."*

Ein KI-Agent ohne Design-Pattern-Anleitung produziert Code, der **kompiliert und Tests besteht**, aber still technische Schulden anhäuft: eng gekoppelte Module, verstreute Geschäftslogik, duplizierte Muster und inkonsistente Abstraktionen. Sechs Monate später zahlen Sie Zinsen auf diese Geschwindigkeit — in Debugging-Zeit, Token-Kosten und Rewrite-Zyklen.

**„Einfach neu schreiben"** ist der teuerste Satz im Software-Engineering. Er war teuer mit menschlichen Entwicklern. Er ist immer noch teuer mit KI — nur zahlen Sie in Token statt in Gehältern.

## Warum Design Patterns in der KI-Ära wichtiger sind

### Das Paradox der KI-Geschwindigkeit

Traditionelle Entwickler lernen Design Patterns durch jahrelange schmerzhafte Erfahrung — Spaghetti-Code, gescheiterte Refactorings, Produktionsausfälle. Der Schmerz ist ein Lehrer. KI-Agenten überspringen den Schmerz komplett, was bedeutet: **Sie überspringen auch die Lektionen.**

Ohne explizite Anleitung wird ein KI-Agent:
- In jeder Funktion eine neue Datenbankverbindung erstellen, statt einen **Singleton**-Pool zu verwenden
- API-Aufrufe tief in die Geschäftslogik hart codieren, statt sie hinter einem **Adapter** zu isolieren
- Konfiguration durch 8 Schichten von Funktionsparametern durchreichen, statt **Dependency Injection** zu verwenden
- Event-Handling über 20 Dateien verstreuen, statt es mit einem **Observer** oder **Mediator** zu zentralisieren

Jedes davon „funktioniert". Jedes davon ist ein zukünftiger Bug, der darauf wartet, auszubrechen.

### Das Token-Wirtschafts-Argument

Hier ist etwas, das Vibe-Coder selten bedenken: **Design Patterns reduzieren direkt den Token-Verbrauch.**

| Szenario | Ohne Patterns | Mit Patterns |
|----------|--------------|--------------|
| „Stripe-Zahlung hinzufügen" | Agent liest 30 Dateien, um herauszufinden, wo die Zahlungslogik hingehört | Agent liest die Adapter-Schicht — 3 Dateien |
| „Von MySQL zu PostgreSQL wechseln" | Agent schreibt 15 Dateien um, in denen SQL verstreut ist | Agent ändert 1 Adapter. Fertig. |
| „Logging zu allen API-Aufrufen hinzufügen" | Agent modifiziert jeden Endpoint einzeln | Agent fügt eine Decorator/AOP-Middleware hinzu. 1 Datei. |
| „Warum scheitern Bestellungen am Wochenende?" | Agent verfolgt Spaghetti-Code über 50+ Runden | Agent prüft das State Pattern — findet ungültigen Übergang in 2 Runden |

Strukturierter Code bedeutet, der Agent **liest weniger, berührt weniger und liegt in weniger Iterationen richtig**. Weniger Iterationen = weniger Token = geringere Kosten. Das ist keine Theorie — das ist Arithmetik.

### Agent Discipline: Das fehlende Konzept

Wir reden viel über „KI-Alignment". Aber es gibt eine praktischere Version davon für Software-Engineering: **Agent Discipline (Agenten-Disziplin)**.

Agent Discipline bedeutet, dass Ihr KI-Coding-Assistent konsequent Architekturregeln befolgt — nicht weil er sie „versteht" wie ein Senior-Ingenieur, sondern weil Sie **die Muster, die er verwenden soll, explizit definiert haben**.

- **Ohne Disziplin:** Sie geben dem Agenten eine Aufgabe. Er schreibt etwas, das funktioniert. Jedes Mal anders. Technische Schulden wachsen leise.
- **Mit Disziplin:** Sie geben dem Agenten eine Aufgabe *plus eine Design-Pattern-Anleitung*. Er schreibt etwas, das funktioniert **und in die bestehende Architektur passt**. Jedes Mal. Konsistent.

Die 13 Skill-Dateien in diesem Repo sind diese Anleitung.

## Inhalt

13 strukturierte Skill-Dateien, organisiert als **Schichtenarchitektur** — von Grundlagen bis Teamintegration:

```mermaid
block-beta
  columns 5

  s00["00 Entscheidungsfluss des Architekten — Hier starten"]:5
  space:5
  s01["01\nModule &\nNamespaces"]
  s02["02\nObjekterzeugung\nFactory · Builder\nSingleton"]
  s03["03\nGemeinsame Utilities\n& Funktionaler Kern"]
  s04["04\nIO & Infra-\nAdapter"]
  s05["05\nQuerschnitts-\nbelange"]
  space:5
  s06["06 Dependency Injection & IoC — Schlussstein"]:5
  space:5
  s07["07\nKommunikation\nObserver · Mediator"]
  s08["08\nZustand &\nGeschäftslogik"]
  s09["09\nMVC / MVP\nMVVM"]
  s10["10\nAsync &\nResilienz"]
  s11["11\nTest-\nstrategie"]
  space:5
  s12["12 Team- & Framework-Integration — Abschluss"]:5

  s00 --> s01
  s00 --> s02
  s00 --> s03
  s00 --> s04
  s00 --> s05
  s01 --> s06
  s02 --> s06
  s03 --> s06
  s04 --> s06
  s05 --> s06
  s06 --> s07
  s06 --> s08
  s06 --> s09
  s06 --> s10
  s06 --> s11
  s07 --> s12
  s08 --> s12
  s09 --> s12
  s10 --> s12
  s11 --> s12

  style s00 fill:#7c3aed,color:#fff,stroke:#7c3aed
  style s06 fill:#7c3aed,color:#fff,stroke:#7c3aed
  style s12 fill:#7c3aed,color:#fff,stroke:#7c3aed
```

## Schnellstart

Lesen Sie die [englische README](README.md) für detaillierte Integrationsanleitungen mit Claude Code, Cursor, Windsurf, GitHub Copilot und git submodule.

## Langfristiges Denken

Manche sagen, Design Patterns seien unwichtig, wenn Code-Lebenszyklen kurz sind und KI alles neu schreiben kann. Wir widersprechen.

**Code-Qualität hat Zinseszins-Effekt.** Jedes gut strukturierte Modul macht das nächste Feature schneller zu bauen, günstiger zu testen und einfacher zu verstehen — für Menschen und KI gleichermaßen.

Ein KI-Agent mit Design Patterns schreibt nicht nur besseren Code. Er schreibt Code, der **seine eigenen zukünftigen Token-Kosten senkt**, weil strukturierter Code weniger Kontext zum Verstehen und weniger Aufwand zum Ändern braucht.

**Das ist nichts, was die meisten Vibe-Coder selbst entdecken. Aber mit 13 Skill-Dateien kann Ihr KI-Agent verinnerlichen, was menschliche Ingenieure Jahre brauchen, um zu lernen — und es bei jedem einzelnen Commit anwenden.**

## Lizenz

Die SKILL.MD-Lehrinhalte in diesem Repository sind Originalarbeit. Die Design-Pattern-Codebeispiele beziehen sich auf *Mastering JavaScript Design Patterns, Second Edition* (Packt). Der Original-Quellcode und die PDFs des Buches sind nicht in diesem Repo enthalten.

---

<p align="center">
  Wenn dies Ihrer KI hilft, besseren Code zu schreiben, vergeben Sie gerne einen ⭐
</p>
