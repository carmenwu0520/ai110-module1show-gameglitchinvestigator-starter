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

I used GitHub Copilot Chat in Agent mode inside VS Code to make the code changes, and Claude as a guide to plan the steps, explain bugs, and review Copilot's diffs with me. I started a new Copilot chat for each bug so it stayed focused.

**Correct suggestion:** For the string-secret bug, I asked Copilot to always pass the integer secret to `check_guess` and remove the `except TypeError` fallback. It replaced the 5-line `% 2` block with one line, deleted the fallback, and added a test that `check_guess(100, 46)` returns "Too High". I checked the diff line by line, ran `python -m pytest` (6 passed), and then guessed 100 twice in the live game. Both times it said "Go LOWER!", where before the fix the same guess gave two different hints.

**Suggestion I did not accept as written:** When I asked Copilot to move the functions into `logic_utils.py` and fix the backwards hints, its first attempt only changed the hint text and didn't move the functions at all (the diff was just +5 -3). I had to follow up and ask again. After the move, it swapped the words but left the emojis, so "Too High" showed "📈 Go LOWER!" with an up-arrow chart. I fixed the emojis by hand so 📉 goes with LOWER and 📈 goes with HIGHER, then confirmed in the pytest output and the game that the messages matched.

---

## 3. Debugging and testing your fixes

I counted a bug as fixed only when two things were true: the pytest tests passed, and I could see the correct behavior in the running game using the same input from my bug log. Running `pytest` directly failed with `ModuleNotFoundError: No module named 'logic_utils'`, so I used `python -m pytest` instead, which runs from the project folder. After moving the functions, the 3 starter tests still failed because `check_guess` returns a tuple like `('Win', '🎉 Correct!')` but the tests compared it to just `'Win'`. I decided to update the tests to unpack `(outcome, message)` instead of changing `check_guess`, because `app.py` needs the message to show the hint. Copilot then wrote the updated tests plus new ones checking that a too-high guess says "LOWER", a too-low guess says "HIGHER", and that `check_guess(100, 46)` is "Too High". Final result: 6 passed.

---

## 4. What did you learn about Streamlit and state?

Streamlit reruns the whole script from top to bottom every time you click a button or type something, so normal variables get reset on every click. `st.session_state` is like a small notebook that survives those reruns, which is why the secret, attempts, score, and status are stored there. This also explained the New Game bug: the button reset some values in the notebook but left `status` as "won", so on the next rerun the script saw "won" and stopped. It also explains why "Attempts left" lags one step behind, because that box is drawn near the top of the script before the Submit code further down updates the count.

---

## 5. Looking ahead: your developer habits

- **Habit to keep:** One bug per AI chat, with a `# FIXME` marking the spot first, and reviewing the diff before keeping anything. Smaller, focused prompts gave me changes I could actually check.
- **What I'd do differently:** Use two terminals from the start (one for the Streamlit app and one for git and pytest). I kept pasting git commands into the terminal that was still running the game, so they never ran.
- **How this changed my thinking:** AI-generated code can look finished and still be wrong in small ways, like an emoji that contradicts the text or a fallback that hides the real bug. I now treat AI output as a draft that I have to verify with tests and by actually running the program.
