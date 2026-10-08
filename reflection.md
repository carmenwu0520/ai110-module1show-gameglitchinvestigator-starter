# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

The game loaded without errors and looked normal at first: a title, a difficulty dropdown, a guess box, Submit / New Game buttons, and a "Developer Debug Info" panel that shows the secret. But the numbers were off right away. The sidebar said "Attempts allowed: 8" while the main box said "Attempts left: 7" before I had guessed anything. Once I started playing, the hints contradicted each other, and after I won, the New Game button didn't actually let me play again. Running `pytest` also failed before a single test could run.

- **The hints lie:** with the secret at 46, guessing 100 said "Go LOWER!" but guessing 99 said "Go HIGHER!", even though both guesses were too high.
- **New Game is broken:** after winning, clicking New Game picked a new secret, but the page still said "You already won" and wouldn't accept guesses.

**Bug Reproduction Log**

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Ran `pytest` on the starter code | The 3 starter tests run | Test collection failed, no tests ran | `ModuleNotFoundError: No module named 'logic_utils'` |
| Secret = 46. Guessed 100, then 99 | "Go LOWER!" both times (both are too high) | 100 → "📉 Go LOWER!", 99 → "📈 Go HIGHER!" | None (wrong logic, no error) |
| Opened a fresh game on Normal | "Attempts left: 8", matching "Attempts allowed: 8" | Showed "Attempts left: 7" before any guess; debug panel showed Attempts = 1 | None |
| Typed `abc` and clicked Submit | Error message, no attempt used | Showed "That is not a number." but Attempts went from 3 to 4 and "abc" was added to History | None |
| Won the game (guessed 46), then clicked New Game | A fresh game I can play | New secret (72) and Attempts reset to 0, but the page still said "You already won. Start a new game to play again." and wouldn't take guesses. Score (30) and History were not reset | None |
| Refreshed, switched difficulty to Easy | Prompt says "between 1 and 20", secret is in 1–20 | Sidebar said "Range: 1 to 20", but the prompt still said "between 1 and 100" and the secret was 36 | None |

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion you did not accept as written (including what the AI suggested, why you rejected or changed it, and how you verified your version). It does not have to be a suggestion that was wrong: over-engineered, out of scope, harder to read, or a poor fit for this codebase all count.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
