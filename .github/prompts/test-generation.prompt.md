---
mode: agent
description: Generate tests for existing code
tools: ['edit', 'search', 'runCommands', 'runTasks', 'context7/*', 'iseplaybook/*']
---

Please generate tests for the code I'm about to share.

**First**: Use the `iseplaybook` MCP server to get the latest testing best practices. Use `context7` MCP server for the specific test framework documentation (xUnit, Jest, pytest).

Follow these guidelines:

## Test Requirements

1. **Test Framework**: Use the appropriate testing framework for the language:
   - C#: xUnit with Moq
   - TypeScript/JavaScript: Jest or Vitest
   - Python: pytest

2. **Test Structure**: Follow AAA pattern (Arrange, Act, Assert)

3. **Naming Convention**: `MethodName_Scenario_ExpectedBehavior`

4. **Coverage Goals**:
   - Happy path scenarios
   - Edge cases (null, empty, boundary values)
   - Error conditions
   - Integration points (with mocks)

5. **Test Quality**:
   - Each test should test ONE thing
   - Tests should be independent
   - Use descriptive names
   - Include comments explaining complex test scenarios

## Output Format

```
// Test file: [filename].test.[ext]

// Test: [test name]
// Scenario: [what we're testing]
// Expected: [expected outcome]

[test code]
```

## Additional Considerations

- Mock external dependencies
- Use test fixtures for complex setups
- Consider parameterized tests for similar scenarios
- Include both positive and negative test cases
