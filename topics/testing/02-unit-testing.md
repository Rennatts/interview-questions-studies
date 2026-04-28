# Unit testing (foundations)

Testing questions come up in interviews because they reveal how you balance **confidence**, **speed**, and **maintainability**. Unit tests are small, fast tests focused on a single unit of behavior.

## Questions & answers

### 1) What is a unit test?

**Short answer**: A unit test verifies a small unit of behavior (function, module, class method) in isolation, aiming to be **fast**, **deterministic**, and **easy to debug**.

---

### 2) What should you unit test (and what shouldn’t you)?

**Short answer**:
- Unit test **pure logic** and business rules (formatters, validators, reducers, selectors).
- Unit test **edge cases** (nulls, empty arrays, invalid inputs, time zones, rounding).
- Avoid unit testing trivial wrappers or third-party library behavior.

**Rule of thumb**: “If a test fails, I want it to point to a specific unit and a clear reason.”

---

### 3) What makes a good unit test?

**Short answer**:
- Clear arrange/act/assert structure
- Minimal setup; easy to read
- Deterministic (no real network/time/random)
- Tests behavior, not implementation details

---

### 4) What’s the difference between mocks, stubs, and spies?

**Short answer**:
- **Stub**: returns a predefined value (replace a dependency’s output).
- **Spy**: records how something was called (arguments, call count) while optionally calling through.
- **Mock**: a test double with expectations (often includes both behavior + call assertions).

**Notes / variations**:
- Terminology varies by tool; what matters is whether you’re asserting **outputs** or **interactions**.

---

### 5) When should you mock dependencies?

**Short answer**: Mock when the dependency is:
- slow (network, filesystem),
- nondeterministic (time, random),
- hard to set up reliably,
- or outside the unit’s responsibility.

**Pitfall**: Over-mocking can make tests brittle and tightly coupled to internal implementation.

---

### 6) How do you test time, randomness, and async code?

**Short answer**:
- Time: fake timers or inject a clock dependency.
- Randomness: seed or inject a random generator.
- Async: await promises; test success + failure paths.

**Interview-friendly line**: “Make nondeterminism injectable; avoid sleeping in tests.”

---

### 7) What is “testing behavior vs implementation details” (especially for UI)?

**Short answer**: Prefer asserting on **observable behavior** (rendered text, ARIA roles, emitted events, returned values) instead of internals (private state, exact DOM structure, class names).

**Example guidance**:
- Good: “Clicking Save calls onSave and shows success.”
- Brittle: “Internal state field `isSaving` flips to true.”

---

### 8) How do you think about unit test coverage?

**Short answer**: Coverage is a signal, not the goal. Aim for coverage on critical logic, but measure success as **reduced defects** and **confidence to refactor**.

**Common pitfalls**:
- High line coverage with low behavioral confidence
- Spending time testing code that changes often and provides little value

