# Skill 01: Foundation — Modules, Namespaces, and Project Structure

## WHY

Before any pattern can be applied, the team needs a **shared module system**. Without it, two engineers create conflicting globals, duplicate utilities, and import paths become a tangled mess.

The module system is the skeleton that all subsequent layers plug into.

## WHICH Patterns

| Pattern | Use When | Book Example |
|---------|----------|-------------|
| IIFE Module | Legacy browser code, no bundler | `B05337_03/AbstractFactory.js` |
| TypeScript `module` / `namespace` | Legacy TS (pre-ES modules) | `B05337_03/AbstractFactory.ts` |
| ES Modules (`import`/`export`) | Modern projects (recommended) | Not in book — enhancement needed |
| Barrel Pattern (`index.ts`) | Public API per directory | Not in book — enhancement needed |

## HOW

### The Book's Approach: TypeScript `module` Keyword

Every file in the book uses this pattern:

```typescript
// From B05337_03/AbstractFactory.ts
module Westeros.Ruling {
  export interface IKing {
    makeDecision();
    marry();
  }
  // ...
}
```

The compiled JavaScript uses an IIFE:

```javascript
// From B05337_03/AbstractFactory.js
var Westeros;
(function (Westeros) {
    var Ruling;
    (function (Ruling) {
        // ...
    })(Ruling = Westeros.Ruling || (Westeros.Ruling = {}));
})(Westeros || (Westeros = {}));
```

**Why this works:** Prevents global pollution. Creates a hierarchical namespace.

**Why this is outdated:** TypeScript `module` keyword for namespacing is deprecated in favor of ES modules. Modern bundlers (webpack, esbuild, vite) work with `import`/`export`.

### Modern Equivalent: ES Modules

The same AbstractFactory in ES module style:

```typescript
// ruling/interfaces.ts
export interface IKing {
  makeDecision(): void;
  marry(): void;
}

export interface IHandOfTheKing {
  makeDecision(): void;
}

export interface IRulingFamilyAbstractFactory {
  getKing(): IKing;
  getHandOfTheKing(): IHandOfTheKing;
}
```

```typescript
// ruling/lannister.ts
import { IKing, IHandOfTheKing, IRulingFamilyAbstractFactory } from './interfaces';

export class KingJoffery implements IKing { /* ... */ }
export class LordTywin implements IHandOfTheKing { /* ... */ }
export class LannisterFactory implements IRulingFamilyAbstractFactory { /* ... */ }
```

```typescript
// ruling/index.ts  (barrel export)
export * from './interfaces';
export { LannisterFactory } from './lannister';
export { TargaryenFactory } from './targaryen';
```

### Project Skeleton for Layered Architecture

```
src/
  core/              ← Skill 03: pure utilities, zero dependencies
  domain/            ← Skill 08: business logic, state machines
  infrastructure/    ← Skill 04: adapters for DB, HTTP, filesystem
  application/       ← Skill 09: controllers, view-models
  crosscutting/      ← Skill 05: decorators, middleware
  communication/     ← Skill 07: event bus, message types
  config/            ← Skill 06: DI container, composition root
  index.ts           ← Application entry point
```

Each directory has an `index.ts` barrel that exports its public API. Internal files are not imported directly from other layers.

## TEAM Convention

1. **One module per bounded context.** Don't dump everything in a `utils/` folder.
2. **Barrel exports per layer.** Other layers import from `infrastructure/index.ts`, never from `infrastructure/adapters/mysql-adapter.ts`.
3. **Dependency direction flows inward.** `application/` → `domain/` → `core/`. Never the reverse.
4. **No circular imports.** If two modules need each other, extract the shared interface into `core/`.

## References

- `Mastering-JavaScript-Design-Patterns-Second-Edition-master/B05337_03/AbstractFactory.ts` — TypeScript `module` keyword usage (lines 1-18)
- `Mastering-JavaScript-Design-Patterns-Second-Edition-master/B05337_03/AbstractFactory.js` — IIFE compilation output

## Next

With the module foundation set, [Skill 02](02-object-creation-layer.md) addresses how objects are created across these modules.
