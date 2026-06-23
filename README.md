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

- [ ] Describe the game's purpose.
- [ ] Detail which bugs you found.
- [ ] Explain what fixes you applied.

## 📸 Demo Walkthrough

Describe your fixed game in numbered steps so a reader can follow along without watching a video:

1. User enters a guess of 50
2. Game returns "Too High"
3. User guesses 25 --> Game returns "Too Low"
4. Score decreases by 5 every wrong guess, attempts decreases by 1 with every valid guess
5. Repeat until attempts reach 0 or the correct guess is guessed

**Screenshot** *(optional)*: <!-- Insert a screenshot of your fixed, winning game here -->

## 🧪 Test Results

```
# Paste your pytest output here, e.g.:
# pytest tests/
# ========================= 7 passed in 2.15s
collected 7 items                                                                                                                                                    

tests/test_game_logic.py::test_winning_guess PASSED                        [ 14%]
tests/test_game_logic.py::test_guess_too_high PASSED                       [ 28%]
tests/test_game_logic.py::test_guess_too_low PASSED                        [ 42%]
tests/test_game_logic.py::test_invalid_guess_non_numeric PASSED            [ 57%]
tests/test_game_logic.py::test_invalid_guess_empty_string PASSED           [ 71%]
tests/test_game_logic.py::test_valid_guess PASSED                          [ 85%]
tests/test_game_logic.py::test_valid_guess_decimal PASSED 
=========================
```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
