## Coding Philosophy

- **Strongly typed.** Maximize type safety. Prefer strict type checking over linting rules. Pyright strict for python.
- **Concise, but not at the cost of clarity.** Be economical, but don't golf.
  - Think of Dijkstra's quote: If we are to count lines of code, we should treat them as lines spent, not lines produced.
  - Prefer taking advantage of standard library or established libraries and practices in the ecosystem to minimize code.
  - Every line of code is cost. Every cost has to be justified. Default to YAGNI on everything unless I ask - I usually have more context.
- **Abstractions that earn their weight are appreciated.** An abstraction that meaningfully provides better DX, eliminates a real category of bugs, or is sufficiently general is well received. Three similar lines are fine; premature abstractions aren't necessary; boilerplate-indicuing abstractions don't carry their weight well.
- **Great DX matters.** APIs should be discoverable, composable, and hard to misuse. The bar is high however - good docs, typing support, non-functional concerns and edge-case coverage must be good. Choose wisely if building something new, the cost is not trivial if done wrong.
- **Separation of concerns** - Design with an eye of future extensibility and keeping the inner "README" intent of modules stable.
- **Language-idiomatic by default**, but open to innovations that demonstrably improve the experience.
- **Design top down** - Start with defining what the actual units are (see Testing section), design their API, wire things together.
  - Be careful when modifying existing code. Existing units might or might not make sense in a new context. Rather than bending existing modules to fit the new case, you might need my input on design.

## Readability

Readability is the most important property of code, second only to performance and only in some situations

- **Match the readability effort to the complexity.** Simple code needs no special care.
  When code gets hard to follow (state that changes over iterations, math,
  several steps mixed in one loop), put more effort into clarity:
  - Names that say what a value is in the domain.
  - Structure the code so that it shows the steps declaratively, in the order of the idea it implements.
  - Local immutability is worth it only when it makes the code clearer.
    Otherwise prefer plain mutation of local state, which is also faster.
  - One meaning for each variable and collection.
- **Check it as a reader.** Before you finish complex code, ask: can a reader say what each value holds
  and why each operation exists without tracing the code? If not, change the structure first.
  Comments come after that. Also check - do all the names make sense?
- **Keep incidental concerns brief and out of the way.** Validation, templating and similar
  concerns use declarative, pre-built tools, so the main logic stays visible.

## Error Handling

Follow language conventions. EffectTS projects are an exception, where we follow EffectTS conventions.

## Testing

- We use a very different approach to unit testing:
  - Not everything is a unit. Not every class, or function, or method is an actual unit.
  - A unit is defined by a stable API with a somewhat increased level of permanency. If its not clear that the API is stable or reusable, or that the proposed unit even represents an API to any other part of the system - its not a unit.
    - This could be one function, or one class, or a facade hiding multiple classes and functions.
    - Illustratively, a unit is also something you'd be proud writing a small README for (although you'd not necessarily always write one)
  - We avoid, as much as possible, testing "units" that don't fit the above definition.
  - Dijkstra's quote applies here too, lines spent must be worth spending.
  - Test ONLY according the API spec of the unit that you're willing to expose for consumption.
    - Assuming an illustrative README exists, only the API surface described in the README is tested.
- When there are business rules that must always hold true, prefer **property tests** over unit tests
  - Small number of LOC, large coverage means a good cost-benefit ratio - so long as the property actually matters as a business rule.
  - Good property tests work on a realistic possible range of inputs.
  - Keep an eye on the performance of property tests, especially when they are integration tests.
- If its not possible to express units in the above described way, prefer **integration tests** over unit tests
  - For example, if logic is spread between SQL and code significantly for performance reasons, its often better to test with DB testcontainers
  - However, if logic to fetch data is simple, but the code manipulating the data is complex, the complex code can clearly benefit from unit tests.
- Sometimes tests are not worth writing at all. Project context defines this. Ask me if unsure.

## Project Structure

1. **Follow existing patterns** in the project first, if we must.
2. If starting fresh, prefer **domain-driven organization** (group by feature/domain, not by technical layer).
3. If domain-driven is awkward for the language/framework, fall back to **language-idiomatic** conventions.

## Communication Style

- **Provide references** - I like to read good source materials, especially when I ask questions and when making bigger plans.
- **Challenge me.** If there's a better approach than what I asked for, say so directly. Don't just comply.
- **One thought per sentence.** Do not chain clauses. Split a compound thought into separate sentences.

## Proposed flow of work for new features

- Depending on the size of the task, decompose into modules (units) first, if needed.
- For each proposed module or unit, start with a README.md if one doesn't exist already
- Consider business invariants that must hold true, for property tests.
  - Before writing each property test, state the business rule it covers and why that rule is important. Then write the test.
- When writing the tests, base them on the API described by the README
  - Its okay if the tests handle a few more edge cases than described in the README, if those edge cases are important.
- Write the implementation
- Do another pass on the implementation checking for readability.
