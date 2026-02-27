# Skill 02: Object Creation Layer — Controlling How Components Are Born

## WHY

Uncontrolled `new` calls scattered across a codebase create **hidden dependencies**. When Engineer A writes `new MySQLConnection()` inside a service, Engineer B cannot swap it for a test double or a different database. Creational patterns create **seams** — points where implementations can be swapped without changing consumers.

This is the creation half of Inversion of Control (the wiring half comes in [Skill 06](06-dependency-injection-and-ioc-container.md)).

## WHICH Patterns

| Pattern | Solves | Example |
|---------|--------|---------|
| **Abstract Factory** | Swapping families of related objects at runtime | DB driver + pool + query builder |
| **Factory Method** | Single creation point needs polymorphism | Logger that creates ConsoleLogger or FileLogger |
| **Builder** | Constructing complex configuration objects step by step | HTTP client config, DB connection options |
| **Singleton** | Exactly one shared instance (use sparingly) | Configuration registry, connection pool |
| **Prototype** | Creating objects by cloning an existing template | Default settings objects |

## HOW

### Abstract Factory — The Star Example

`B05337_03/AbstractFactory.ts` is the best example in the repository:

```typescript
// Interface defines the contract — consumers depend only on this
export interface IRulingFamilyAbstractFactory {
  getKing(): IKing;
  getHandOfTheKing(): IHandOfTheKing;
}

// Concrete factory 1: Lannister family
export class LannisterFactory implements IRulingFamilyAbstractFactory {
  getKing(): IKing { return new KingJoffery(); }
  getHandOfTheKing(): IHandOfTheKing { return new LordTywin(); }
}

// Concrete factory 2: Targaryen family
export class TargaryenFactory implements IRulingFamilyAbstractFactory {
  getKing(): IKing { return new KingAerys(); }
  getHandOfTheKing(): IHandOfTheKing { return new LordConnington(); }
}

// Consumer depends on the INTERFACE, not concrete classes
export class CourtSession {
  constructor(public abstractFactory: IRulingFamilyAbstractFactory) {}
  complaintPresented(complaint) {
    if (complaint.severity < this.COMPLAINT_THRESHOLD) {
      this.abstractFactory.getHandOfTheKing().makeDecision();
    } else {
      this.abstractFactory.getKing().makeDecision();
    }
  }
}
```

**Production mapping:** Replace `IRulingFamilyAbstractFactory` with `IDatabaseDriverFactory`. Replace `LannisterFactory`/`TargaryenFactory` with `MySQLDriverFactory`/`PostgresDriverFactory`. The `CourtSession` becomes any service that needs database access — it never knows which database it's talking to.

### Singleton — Cautionary Tale

`B05337_03/Singleton.ts` demonstrates the pattern but has a **critical bypass bug**:

```typescript
// The book's Singleton allows direct construction:
var wall = new Westeros.Wall();        // bypasses getInstance()!
var wall2 = Westeros.Wall.getInstance(); // correct usage
// wall !== wall2 — two instances exist!
```

**Production fix:** Use a private constructor (TypeScript enforces this):

```typescript
export class Configuration {
  private static instance: Configuration;
  private constructor(private settings: Map<string, string>) {}

  static getInstance(): Configuration {
    if (!Configuration.instance) {
      Configuration.instance = new Configuration(new Map());
    }
    return Configuration.instance;
  }
}
// new Configuration() → compile error
```

**When to use:** Configuration, connection pools, service registries. **When NOT to use:** Anything that should be testable with different instances — prefer DI ([Skill 06](06-dependency-injection-and-ioc-container.md)) over Singleton.

### Builder — Enhancement Needed

`B05337_03/Builder.ts` is skeletal. A production Builder uses a **fluent interface**:

```typescript
export class HttpClientConfigBuilder {
  private config: Partial<HttpClientConfig> = {};

  baseUrl(url: string): this { this.config.baseUrl = url; return this; }
  timeout(ms: number): this { this.config.timeout = ms; return this; }
  retries(count: number): this { this.config.retries = count; return this; }
  auth(token: string): this { this.config.authToken = token; return this; }

  build(): HttpClientConfig {
    if (!this.config.baseUrl) throw new Error('baseUrl is required');
    return { timeout: 5000, retries: 3, ...this.config } as HttpClientConfig;
  }
}

// Usage:
const config = new HttpClientConfigBuilder()
  .baseUrl('https://api.example.com')
  .timeout(3000)
  .retries(5)
  .build();
```

## TEAM Convention

1. **No raw `new` for cross-layer dependencies.** If a class in `application/` needs something from `infrastructure/`, it receives it through constructor injection, not `new`.
2. **Factory interfaces defined at layer boundaries.** The `domain/` layer defines `IDatabaseDriverFactory`; the `infrastructure/` layer implements it.
3. **Singleton only for true global state.** Prefer DI container-managed singletons ([Skill 06](06-dependency-injection-and-ioc-container.md)) over static `getInstance()`.

## References

- `B05337_03/AbstractFactory.ts` — Complete abstract factory with interface + 2 concrete factories + consumer
- `B05337_03/Singleton.ts` — Singleton with constructor bypass vulnerability
- `B05337_03/Builder.ts` — Skeletal builder (needs fluent enhancement)
- `B05337_03/FactoryMethod.ts` — Factory method pattern
- `B05337_03/Prototype.ts` — Prototype pattern
- `B05337_06/LazyInstantiation.ts` — Deferred creation

## Next

With object creation controlled, [Skill 03](03-shared-utilities-and-functional-core.md) builds the shared utility layer that any part of the system can safely call.
