# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

A learning repository for JavaScript/TypeScript design patterns, based on "Mastering JavaScript Design Patterns Second Edition" (Packt). The owner is studying these patterns and building a series of SKILL.MD files to teach design patterns to engineers.

## Repository Structure

Code examples live under `Mastering-JavaScript-Design-Patterns-Second-Edition-master/`:

| Directory    | Topic                                                        |
|-------------|--------------------------------------------------------------|
| B05337_03   | Creational Patterns (Singleton, Factory Method, Abstract Factory, Builder, Prototype) |
| B05337_04   | Structural Patterns (Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy) |
| B05337_05   | Behavioral Patterns (Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor) |
| B05337_06   | Functional Patterns (Filter, Accumulator, LazyInstantiation, Immutable, Memento) |
| B05337_07   | Architectural Patterns — MVC, MVP, MVVM                      |
| B05337_08   | Advanced Architectural Patterns — MVC, MVP, MVVM (continued) |
| B05337_09   | Asynchronous Patterns (Promises, Web Workers)                |
| B05337_10   | Messaging Patterns (PubSub, RequestResponse, FanOutIn)       |
| B05337_12   | Testing Patterns (AAA, Mock, Spy, Stub)                      |
| B05337_13   | AOP, Dependency Injection, DSL                               |
| B05337_14   | Modern JS/TS Features (Decorators, Async/Await, Arrow Functions, Babel transpilation) |

Two PDF books are also at the repo root for reference.

## Code Conventions

- Every pattern has **dual files**: `.ts` (TypeScript source) and `.js` (compiled/equivalent JavaScript).
- All examples use a **Westeros (Game of Thrones) domain** as the running namespace (e.g., `Westeros.Ruling`, `Westeros.Armor`).
- JavaScript files use the **IIFE module pattern**: `var Westeros; (function(Westeros) { ... })(Westeros || (Westeros = {}));`
- TypeScript files use `module` / `namespace` keywords for the same structure.

## Build & Tooling

- **No root-level package.json, build, test, or lint commands.** Each chapter is self-contained.
- B05337_14 contains a `.babelrc` with `babel-preset-es2015` and local `node_modules` for ES6→ES5 transpilation.
- TypeScript files can be compiled individually with `tsc <file>.ts`.

## SKILL.MD Files

The owner is building a `skills/` directory at the repo root containing English-language SKILL.MD files that teach design patterns to engineers. When creating or editing SKILL.MD files:

- Place all files under `skills/` at the repo root.
- Write in **English**.
- Each file covers one pattern or a cohesive group of related patterns.
- Reference the corresponding code examples in `Mastering-JavaScript-Design-Patterns-Second-Edition-master/`.

## Communication Language

Communicate with the user in **繁體中文**. SKILL.MD content is written in English.
