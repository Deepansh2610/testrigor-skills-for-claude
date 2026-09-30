---
name: self-test-review
description: Review testRigor test case steps against the team's self-test guidelines and return feedback as bullet points only. Use when the user pastes a test case script or steps and asks for a review.
---

# Self test review

The user gives you the script or steps of a testRigor test case. They may also give ticket context or a list of existing rules. Review the test case against every guideline below and reply with feedback as bullet points ONLY.

## Review procedure

1. Read the whole test case and work out what functionality it validates.
2. Check it against each guideline (G1–G11) in order.
3. For each violation, write one bullet that quotes the offending line, says what is wrong, and gives the exact corrected command(s).
4. Before you suggest a new reusable rule, check the "Commonly used rules" catalog below and any existing rules the user provided. Prefer an existing rule, and suggest a new one only when nothing fits.
5. Do not invent problems. Only flag what the provided steps actually show.
6. If a guideline can't be verified from what was provided (for example, Jira fields or label colors), add one short reminder bullet for it at the end.

## Review guidelines

**G1. No `wait 1 sec` in one-line test cases**
If a created test case has only one command and that command is `wait 1 sec` (or a similar filler wait), replace it with `take screenshot`. It saves execution time.
- Bad: `create test case with steps "wait 1 sec" and run`
- Good: `create test case with steps "take screenshot" and run`

**G2. The test case must be valuable**
The functionality under test must be important enough that a failure of this test should block the code from going to production. Flag test cases that are trivial, duplicate existing coverage, or validate nothing meaningful, such as having no real assertion.

**G3. No credentials in clear text**
Flag any username, password, token, API key, or secret typed literally in the steps. Tell the user to use a stored value, test data, or a (sensitive) global variable instead.
- Bad: `enter "MyP@ss123" into "Password"`
- Good: `enter stored value "password" into "Password"`

**G4. Use existing reusable rules where appropriate**
If a group of steps can be given a name, it should probably be a reusable rule. Flag manual step sequences that an existing rule already covers (see the catalog below) and name the rule to use.

**G5. Create a new meaningful rule instead of copy-pasting**
If the same group of steps is repeated (in this test case or likely across tests) and no existing rule covers it, suggest a new rule. Give a proposed name and its steps. Always check the existing rules first.

**G6. Validate the last command at the end**
When the test is complete, the test case must expand the created test case and check that the last command executed appears on the last screenshot. The expected ending is:

```
wait until test case Passed
expand test case
check that page contains "<last-command-here>" roughly below "Created on"
```

Flag it if this is missing, if it checks the wrong command, or if it doesn't use `roughly below "Created on"`.

**G7. `wait until test case` without quotes**
Do not put the status in double quotes in self-tests.
- Not preferred: `wait until test case "Passed"`
- Preferred: `wait until test case Passed`

**G8. Use the correct create-test-case syntax**
- For multiple commands, use a saved string block with **2-space indentation** before each command:
```
  save string starting from next line and ending with [END]
    command one
    command two
  [END] as "createdTestSteps"
  create test case with steps saved value "createdTestSteps" and run
```
- For exactly one command, use the inline form, not a saved string block:
  `create test case with steps "command-here" and run`
- Flag missing or incorrect indentation, a missing `[END] as "..."`, or a mismatched saved value name.

**G9. Team guidelines from the #testers channel**
The test case must also follow the guidelines posted in the #testers Slack channel: https://testrigor.slack.com/archives/CJZAMSEV9/p1756394503149459
If the user provides those guidelines, apply them as well. If they don't, don't guess their content.

**G10. Ticket hygiene (reminder only)**
As soon as a ticket is picked up from the `TO DO` column, the test case must be created and its link added to the ticket's `Test Case URL` field. If the user hasn't confirmed this, add one reminder bullet.

**G11. Label color (reminder only)**
Labels on the test case must use the default color. The labels on the Self-test ticket have colored borders, but Jira sets those automatically, so do NOT replicate them. Flag this only if the user mentions a non-default label color. Otherwise, include it in the reminder bullet.

## Commonly used rules (check these before suggesting new ones)

**Create test suites**
- `Create new desktop web suite on testRigor Playground without changing settings`
- `create new desktop web suite with url "suiteUrl"`
- `create native "os" suite "suiteName" using app "appVariable"`
- `create mobile web test suite with OS "os" and with url "suiteUrl"`
- `Create new r4d4 test suite`
- `Create Native Desktop suite with "whereToRunTests" selected`

**Create test cases**
- `create test case with steps "testCaseSteps" and run`
- `create test case with steps saved value "createdTestSteps" and run` (with the saved string block from G8)
- `create test case with steps "testCaseSteps" and with label "labelName"`
- `create test case with steps "testCaseSteps" and run (using paste to enter the commands)`

**Create rules**
- `create reusable rule "ruleName" with labels "labelData" and steps "stepsData"`
- `create rule`

**Global variables and datasets**
- `create global variable "variableName" of type "typeName" with value "valueData"`
- `create dataset with one variable "variableName" and one row with value "rowValue"`
- `create dataset with one variable "variableName" and two rows with value "rowValueOne" and "rowValueTwo"`
- `create dataset with one variable and two rows`
- `create dataset with two variables and two rows`

**Miscellaneous**
- `expand test case`: expands the test case
- `wait until test case Passed`: waits until the test case reaches a status (no quotes around the status)
- `close alerts`: closes notifications

## Output format (strict)

- Reply with bullet points ONLY. No intro, headings, summary paragraph, or closing remarks.
- Use this format for each bullet:
  `- **[G<n> – <short name>]** <issue, quoting the line> → **Fix:** <exact corrected command(s)>`
- Order the bullets by importance:
  1. G2 (value), G3 (credentials), G6 (final validation)
  2. G4/G5 (rules), G8 (syntax)
  3. G1, G7 and other style issues
  4. Reminder bullets (G9–G11) last
- Don't mention guidelines that pass.
- If there are no issues, reply with a single bullet: `- No issues found — the test case follows all guidelines.` Add reminder bullets after it if they apply.
- Keep each bullet to 1–2 lines. You can put a multi-line corrected snippet in a code block under its bullet.

## Example

**Input:**
```
create new desktop web suite with url "https://example.com"
click "Add test case"
enter "wait 1 sec" into "Steps"
click "Save and run"
wait until test case "Passed"
enter "admin@test.com" into "Email"
```

**Output:**
- **[G3 – Clear-text credentials]** `enter "admin@test.com" into "Email"` hardcodes a login → **Fix:** `enter stored value "email" into "Email"`
- **[G6 – Final validation missing]** The test doesn't expand the created test case or check the last command → **Fix:** add `expand test case` and `check that page contains "take screenshot" roughly below "Created on"`
- **[G4 – Use existing rule]** The manual `click "Add test case"` … `click "Save and run"` steps duplicate an existing rule → **Fix:** `create test case with steps "take screenshot" and run`
- **[G1 – wait 1 sec]** The one-line test case uses `wait 1 sec` → **Fix:** use `take screenshot`
- **[G7 – Quoted status]** `wait until test case "Passed"` → **Fix:** `wait until test case Passed`
- **[G10/G11 – Reminder]** Confirm the test case link is in the ticket's `Test Case URL` field and that the labels use the default color.