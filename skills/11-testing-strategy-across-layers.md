# Skill 11: Testing Strategy — Verifying Each Layer Independently

## WHY

DI-enabled layers ([Skill 06](06-dependency-injection-and-ioc-container.md)) are **naturally testable** because every dependency can be replaced with a test double. The question is: what kind of test for which layer?

Without a testing strategy, teams write either zero tests or fragile end-to-end tests that break on every change. A layered architecture enables **targeted testing** — fast, focused tests for each layer.

## WHICH Test Types for WHICH Layers

| Layer | Test Type | Dependencies | Speed |
|-------|-----------|-------------|-------|
| Core / Shared Utilities ([Skill 03](03-shared-utilities-and-functional-core.md)) | Pure unit tests | None — pure functions | Instant |
| Domain / Business Logic ([Skill 08](08-state-management-and-business-logic.md)) | Unit tests with state verification | None or simple stubs | Fast |
| Infrastructure Adapters ([Skill 04](04-io-and-infrastructure-adapters.md)) | Integration tests | Real DB/API (or testcontainers) | Slower |
| Application Layer ([Skill 09](09-application-layer-mvc-mvp-mvvm.md)) | Unit tests with mocked services | Mocks for domain services | Fast |
| Communication ([Skill 07](07-inter-component-communication.md)) | Unit tests with spy subscribers | Spies for event handlers | Fast |
| Cross-Cutting ([Skill 05](05-cross-cutting-concerns-aop-and-decorators.md)) | Decorator composition tests | Mocks for inner service | Fast |

## HOW

### AAA Pattern — Arrange, Act, Assert

`B05337_12/AAA.ts` demonstrates the fundamental test structure:

```typescript
// Arrange — set up the test scenario
const builder = new CastleBuilder();

// Act — perform the action under test
const castle = builder.buildCastle('Winterfell');

// Assert — verify the result
assertEqual(castle.name, 'Winterfell');
```

Every test in every layer follows this structure. No exceptions.

### Test Doubles Taxonomy

| Type | Purpose | Book Reference |
|------|---------|---------------|
| **Dummy** | Fills a parameter slot, never used | Not in book |
| **Stub** | Returns pre-configured data | `B05337_12/stub2.ts` |
| **Spy** | Records calls for later assertion | `B05337_12/spy.ts`, `spy2.ts` |
| **Mock** | Pre-programmed expectations + verification | `B05337_12/mock.ts` |
| **Fake** | Working implementation (e.g., in-memory DB) | Not in book — see Skill 04 |

### Testing the Domain Layer (State Machine)

Using the `BankAccountManager` from [Skill 08](08-state-management-and-business-logic.md):

```typescript
describe('BankAccountManager', () => {
  it('starts in GoodStanding state', () => {
    // Arrange
    const account = new BankAccountManager();

    // Act
    account.Deposit(100);

    // Assert
    expect(account.getBalance()).toBe(100);
  });

  it('transitions to Overdrawn when withdrawing more than balance', () => {
    const account = new BankAccountManager();
    account.Deposit(50);

    account.Withdraw(100);

    expect(account.getBalance()).toBe(-50);
    // Further withdrawal should transition to OnHold
    expect(() => account.Withdraw(10)).toThrow();
  });

  it('transitions back to GoodStanding when depositing enough', () => {
    const account = new BankAccountManager();
    account.Deposit(50);
    account.Withdraw(100);  // now overdrawn

    account.Deposit(200);   // back to positive

    account.Withdraw(10);   // should succeed (good standing)
    expect(account.getBalance()).toBe(140);
  });
});
```

### Testing the Application Layer (Mocked Services)

From `B05337_12/mock.ts` concept — test a Controller with mocked domain service:

```typescript
describe('OrderController', () => {
  it('creates order and emits event', async () => {
    // Arrange — create mocks
    const mockOrderService: IOrderService = {
      createFromForm: jest.fn().mockReturnValue({ id: '123', items: [] }),
      save: jest.fn().mockResolvedValue(undefined),
    };
    const spyEventBus: IEventBus = {
      emit: jest.fn(),
      on: jest.fn(),
    };

    const controller = new OrderController(mockOrderService, spyEventBus);

    // Act
    await controller.createOrder({ items: [{ productId: 'A', qty: 1 }] });

    // Assert
    expect(mockOrderService.save).toHaveBeenCalledWith({ id: '123', items: [] });
    expect(spyEventBus.emit).toHaveBeenCalledWith('order:created', { id: '123', items: [] });
  });
});
```

### Testing Infrastructure Adapters (Integration)

Infrastructure tests hit real systems (or testcontainers). They verify the adapter correctly translates between your interface and the external system:

```typescript
describe('MySQLUserRepository', () => {
  let pool: mysql.Pool;
  let repo: MySQLUserRepository;

  beforeAll(async () => {
    pool = await createTestPool();  // test database
    repo = new MySQLUserRepository(pool);
  });

  afterAll(async () => { await pool.end(); });

  it('saves and retrieves a user', async () => {
    const user = { id: 'test-1', name: 'Ned Stark', email: 'ned@winterfell.com' };

    await repo.save(user);
    const found = await repo.findById('test-1');

    expect(found).toEqual(user);
  });
});
```

### Testing Cross-Cutting Decorators

```typescript
describe('LoggingUserService', () => {
  it('delegates to inner service and logs', async () => {
    const mockInner: IUserService = {
      findById: jest.fn().mockResolvedValue({ id: '1', name: 'Test' }),
      save: jest.fn(),
    };
    const spyLogger: ILogger = { info: jest.fn(), error: jest.fn() };

    const service = new LoggingUserService(mockInner, spyLogger);

    const result = await service.findById('1');

    expect(result).toEqual({ id: '1', name: 'Test' });
    expect(mockInner.findById).toHaveBeenCalledWith('1');
    expect(spyLogger.info).toHaveBeenCalledTimes(2);  // before + after
  });
});
```

### Testing PubSub (Spy Subscribers)

```typescript
describe('TypedEventBus', () => {
  it('notifies all subscribers', () => {
    const bus = new TypedEventBus();
    const spy1 = jest.fn();
    const spy2 = jest.fn();

    bus.on('user:created', spy1);
    bus.on('user:created', spy2);

    bus.emit('user:created', { id: '1', name: 'Test' });

    expect(spy1).toHaveBeenCalledWith({ id: '1', name: 'Test' });
    expect(spy2).toHaveBeenCalledWith({ id: '1', name: 'Test' });
  });

  it('returns unsubscribe function', () => {
    const bus = new TypedEventBus();
    const spy = jest.fn();

    const unsubscribe = bus.on('user:created', spy);
    unsubscribe();

    bus.emit('user:created', { id: '1' });

    expect(spy).not.toHaveBeenCalled();
  });
});
```

### DI Enables Testing

The connection between [Skill 06](06-dependency-injection-and-ioc-container.md) and testing:

```typescript
// Production composition root:
container.register('IUserRepository', () => new MySQLUserRepository(pool));

// Test composition root:
container.register('IUserRepository', () => new InMemoryUserRepository());

// Same application code, different wiring — zero changes to business logic.
```

### The Book's Testing Gaps

- `B05337_12/stub.ts` is **empty** (0 bytes). A stub should return canned data:
  ```typescript
  class StubUserRepository implements IUserRepository {
    async findById(id: string) { return { id, name: 'Stub User' }; }
    async save(user: User) { /* no-op */ }
  }
  ```
- `B05337_07/MVC.ts` has bugs (lines 85, 91) that a test would catch immediately. Writing tests for the MVC Controller is a good exercise.

## TEAM Convention

1. **Each PR includes tests for the layer it touches.** Domain change → domain tests. Adapter change → integration tests.
2. **Mock interfaces, not implementations.** If you need to mock a concrete class, your code is too coupled.
3. **Test behavior, not implementation.** Assert "user was saved" not "save was called with these exact SQL parameters."
4. **Fast tests run on every save.** Integration tests run in CI.
5. **Test the State machine transitions** ([Skill 08](08-state-management-and-business-logic.md)) — every valid and invalid transition should have a test.
6. **Follow AAA strictly.** Arrange, Act, Assert — with clear separation. No interleaving.

## References

- `B05337_12/AAA.ts` — Arrange-Act-Assert pattern with CastleBuilder
- `B05337_12/mock.ts` — MockCredentialFactory with Verify
- `B05337_12/spy.ts` — Method call tracking
- `B05337_12/spy2.ts` — Spy variant
- `B05337_12/stub.ts` — Empty file (stub needed)
- `B05337_12/stub2.ts` — Stub with canned responses

## Next

With testing strategy in place, [Skill 12](12-team-framework-integration.md) ties everything together into a team application framework.
