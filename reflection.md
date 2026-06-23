# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").

The game looked alright, with a clean UI overall. The cracks only started to show after running the game and starting to make guesses.

With the game itself:
- The hints were backwards if you didn't guess the secret # correctly. A guess lower than the secret would prompt "Go HIGHER!", and vice versa.
- Entering numbers outside the range always gave the "Go LOWER!" hint, regardless of whether or not that's correct. 
- The difficulty is always stuck on Normal, shown by the range of secrets always being 1-100. Changing the difficulty doesn't change that range; it only changes the range of guesses.
- Entering strings takes attempts, even though it shouldn't do anything except re-prompt the user. The amount of attempts can go into the negatives this way instead of stopping at 0.
- The game doesn't end when the amount of attempts hits 0; it must end with a valid input of a number.
- Making a guess after starting a new game doesn't work. The Submit button does nothing, even if the answer is correct.


With the dev debug info:
- On Easy or Normal difficulty, each guess lags by 1 in the guess history.
- The score doesn't track correctly after guessing something invalid, like an OOB number.
- Guesses that are way too high or low (ex. 12341234123412341234) don't show up correctly in the guess history.

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input    |    Expected Behavior   | Actual Behavior  | Console Output / Error |
|----------|------------------------|------------------|------------------------|

| Guess > Secret |  Hint says "Go LOWER!" | Hint says "Go HIGHER!" | None |

| Guess < Secret | Hint says "Go HIGHER!" | Hint says "Go LOWER!"  | None |

| OOB Guess | Invalid guess due to being out of range |  Hint says "Go LOWER!" | None |

| Guess is a string | Invalid guess due to not being a number | Hint says "That is not a number", but an attempt is still taken | None |

| Difficulty changed to Easy/Hard | Guess range changes | Guess range stays the same (1 - 100) | None |

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

I used Claude Code as a teammate this time, and probably will for the foreseeable future in this course.

When asked about the bugs with guess range, it was correct in suggesting that the function for getting range was actually fine, and it was a later step in the game logic causing the problem of all guesses being in the range 1-100. It explained that the function was effectively decorative because the logic for creating a new secret to guess was hard-coded to pick a random number 1-100 instead of using a range selected by difficulty.

I checked this myself and found that, sure enough, it was hard-coded with parameters for min and max of 1 and 100. I then changed this to the results of get_range_for_difficulty() with the current chosen difficulty, and it seems to work fine, with the ranges for Easy difficulty and Hard difficulty being 1-20 and 1-50, respectively.

In contrast, when asked to build a pytest test for invalid guesses, it returned a file with one function that couldn't be run without an error, and three that asserted the wrong value in a short-sighted way. It also gave incorrect instructions on the first pass on how to run the tests.

With the functions...
  - test_invalid_guess_none() couldn't run because None is not an acceptable parameter value. I "fixed" this by commenting the whole thing out.
  - "test_winning_guess()", test_guess_too_high()", and "test_guess_too_low()" all had assert statements that were simply "assert result = " + the first value in check_guess()'s returned tuple. I had to change this to two separate statements checking both values in the tuple because as it was, a whole tuple was being compared against a string, which would always fail.

With the tests, it initially said to run the test file as a regular python file, that is, with "python tests\test_game_logic.py". After I tried this and it failed to run, I asked again, and it suggested to use "pytest tests\test_game_logic.py -v" instead. This wound up working. 

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

I decided whether or not a bug was really fixed by testing the features in every way I found them to fail or give the wrong result. Primarily, I tested by using the app itself rather than pytest, since it was easier to try different approaches and log what went wrong. For instance, when I was testing the ways invalid guesses (e.g., strings, OOB guesses) and wrong guesses were processed, I tried entering several values of each type to see how things were broken first. In doing this, I found they were consistent in giving the same errors across values of the same type. After logging that and implementing a fix, namely adjusting how the total attempts were tracked to not count invalid guesses, running the same tests provided expected results without messing with the total attempts or hints. It was then that I deemed the bug fixed, since nothing failed or happened unexpectedly.

Claude Code helped by designing the pytest tests, since I didn't have prior experience with writing them. By doing this, it gave me a baseline understanding of how pytest tests are written and how they work, from the file to the terminal.

It also helped me understand the cause of an error I found with the guess ranges. Where I only saw the guess ranges as being stuck at 1-100 and difficulty being stuck on Normal, Claude Code found that the problem stemmed from a single line outside where I was looking. It suggested to change the hard-coded bounds of 1-100 in the secret generator to that of the chosen difficulty, and after implementing that, everything worked correctly with the ranges.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

I'd say Streamlit reruns are simpler, almost brute-force means of reacting to input in an app. Rather than having an ongoing event loop to listen for input and react in the same session like in JavaScript, the reruns just change a given value based on input and run the app's entire script again using that value, plus whatever saved. In other words, it's like drawing a whole new picture with one thing changed from the original, rather than erasing a bit and drawing it again in the same picture.

Session state is what tracks those changes and allows this method to work. It holds the values that need to remain, like attempts, score, difficulty, and game state here, so Streamlit can use them again in the next rerun. Whenever a value changes or input is given, session state preserves it too, which gives the illusion of instant reaction from the app when combined with the reruns happening almost instantaneously.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

I'd definitely make a habit of commenting what's been fixed on the lines where/near something has been fixed, especially with the tag "FIXED" or so. I've already been making notes in comments near the top of the file about fixes and updates as well as thoughts, but this new approach seems more intuitive.

Where AI is concerned, I'll try to rely on it less for generating code and more for helping read and examine code for weak points or bugs. While Claude Code is pretty solid in generating basic code like pytest tests or refactoring, it's inherently short-sighted with what needs to be tested or done. This led me to take more precaution in reading through the code to see if it actually does what it's intended to do, and to have lower expectations with what it generates.

In other words, this project made me realize that AI-generated code is pretty good as a baseline for more complex code. It'll rather reliably get the job done where accuracy isn't too big a deal, but it's still best practice to double-check it, and skip it where more complex operations or expectations are taking place.
