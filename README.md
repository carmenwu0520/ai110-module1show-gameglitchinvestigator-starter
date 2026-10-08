# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [x] **Game purpose:** A Streamlit number guessing game. You pick a difficulty, guess a secret number, and the game tells you to go higher or lower until you win or run out of attempts.
- [x] **Bugs I found:**
  - The hints were backwards ("Too High" told you to "Go HIGHER").
  - On every other guess the secret was turned into a string, so numbers were compared as text ("100" < "46"), which made the same guess give different hints.
  - New Game didn't really restart: status stayed "won"/"lost", so the page stayed stuck. It also always picked a secret from 1–100 regardless of difficulty.
  - `pytest` couldn't import `logic_utils`, and the starter tests compared a tuple to a string.
  - Also noticed (not fixed yet): attempts start at 1 instead of 0, invalid input like `abc` uses up an attempt, and the prompt always says "between 1 and 100".
- [x] **Fixes I applied:**
  - Moved `get_range_for_difficulty`, `parse_guess`, `check_guess`, and `update_score` from `app.py` into `logic_utils.py`.
  - Fixed the hint messages (and swapped the emojis by hand so 📉 goes with LOWER).
  - Always pass the integer secret to `check_guess` and removed the `except TypeError` string-comparison fallback.
  - New Game now resets status, score, history, and attempts, and picks the secret from the current difficulty's range.
  - Updated the starter tests to unpack `(outcome, message)` and added 3 new tests.

## 📸 Demo Walkthrough

1. Run `python -m streamlit run app.py` and open the "Developer Debug Info" panel to see the secret (in my game it was 42).
2. Guess 100 → the game shows "📉 Go LOWER!"
3. Guess 100 again → it still shows "📉 Go LOWER!" (before the fix, the same guess gave a different hint).
4. Guess 20 → the game shows "📈 Go HIGHER!"
5. Guess 42 → balloons and "You won! The secret was 42."
6. Click "New Game" → status, score, and history reset and I can keep playing.
7. Switch to Easy and click "New Game" → the new secret is between 1 and 20.

## 🧪 Test Results

```
$ python -m pytest
============================= test session starts ==============================
platform darwin -- Python 3.13.7, pytest-9.1.1, pluggy-1.6.0
rootdir: /Users/carmenwu/ai110-module1show-gameglitchinvestigator-starter
plugins: anyio-4.15.1
collected 6 items

tests/test_game_logic.py ......                                          [100%]

============================== 6 passed in 0.02s ===============================
```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
