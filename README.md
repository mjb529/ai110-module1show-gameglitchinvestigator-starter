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

**Game purpose:** A number guessing game where the player tries to identify a secret number within a limited number of attempts. The player receives higher/lower hints after each guess and earns points based on how few attempts it takes to win.

**Bugs found:**
- Hint messages were swapped — "Go HIGHER" showed when the guess was too high, and "Go LOWER" when too low
- `attempts` initialized to `1` instead of `0`, silently stealing one attempt every game
- Hard difficulty range was `1–50`, easier than Normal's `1–100`
- Even-numbered attempts cast the secret to a string, breaking numeric comparison (e.g. `"9" > "10"` is `True` in Python)
- Scoring asymmetry: "Too High" on even attempts rewarded `+5` points instead of penalizing
- New Game button never reset `status`, so a finished game couldn't be restarted
- New Game always used `randint(1, 100)` regardless of difficulty
- Switching difficulty mid-game didn't reset the secret to the new range
- Info bar hardcoded `"between 1 and 100"` regardless of difficulty
- Out-of-range guesses were silently accepted and wasted an attempt
- Enter key didn't trigger submission (required wrapping input in `st.form`)
- Score, history, and hint display lagged by one render pass due to Streamlit's rerun order

**Fixes applied:**
- Corrected hint messages in `check_guess` in `logic_utils.py`
- Initialized `attempts` to `0`
- Set Hard range to `1–200`
- Removed the even-attempt string cast so secrets are always compared as integers
- Standardized scoring so both "Too High" and "Too Low" subtract 5 points
- Reset `status`, `history`, and `last_hint` on New Game
- New Game now uses `randint(low, high)` based on current difficulty
- Added `current_difficulty` tracking to session state so switching difficulty resets the secret
- Updated info bar to use `{low}` and `{high}` dynamically
- Added `is_in_range` check before accepting a guess
- Wrapped input and submit button in `st.form`
- Used `st.empty()` placeholders for attempts, debug info, and hints so they update on the same render pass they change
- Refactored all core logic (`check_guess`, `parse_guess`, `update_score`, `get_range_for_difficulty`, `get_attempt_limit`, `is_in_range`) out of `app.py` into `logic_utils.py`
- Added a High Score tracker that persists the best score to `high_score.txt` across sessions

## 📸 Demo

- [ ] <img width="2880" height="1562" alt="image" src="https://github.com/user-attachments/assets/126e5e12-72de-4cb4-884a-2c7d4ffd5923" />


## 🚀 Stretch Features

- [ ] <img width="2880" height="1562" alt="image" src="https://github.com/user-attachments/assets/2ecd8459-3cd0-4a9d-b8b7-64e59ef88e42" />

