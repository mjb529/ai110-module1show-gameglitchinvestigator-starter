# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").

---

The hints appeared backwards, the number of attempts allowed are one less than they were promised to be, the new game button does not work

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

---

I used Claude Code (Anthropic's AI CLI) throughout this project. I described bugs conversationally and Claude traced the root cause in the code, then made the edits directly.

One correct suggestion: Claude identified that the hint messages in `check_guess` were swapped — "Go HIGHER" was returned when the guess was too high and "Go LOWER" when it was too low. I verified this by reading the updated `logic_utils.py` and manually testing a guess I knew was above the secret.

One misleading suggestion: Claude initially moved the debug expander below the submit handler to fix the score/history display lag. This introduced a new bug — any widget interaction (like toggling "Show hint") would collapse the expander. Claude had fixed one problem while creating another, and I had to point it out before it was corrected using `st.empty()` placeholders instead.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

I decided a bug was fixed by manually running the game and reproducing the original broken behavior, then confirming it no longer occurred after the change. For example, after fixing the attempt counter I started a new game and confirmed the display showed the correct number of remaining attempts from the very first guess.

I ran `pytest tests/test_game_logic.py` after all the logic was refactored into `logic_utils.py`. One test that was immediately revealing was `test_check_guess_single_digit_vs_two_digit` — it tested `check_guess(9, 10)` and confirmed the outcome was `"Too Low"`, which would have returned `"Too High"` under the old string-comparison bug.

Claude helped design the tests: after identifying each bug, it generated targeted pytest cases for each one, including edge cases like boundary values for `is_in_range` and cross-difficulty secret range validation.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

As I learned from Claude, you can imagine a web page that completely reloads every single time you click a button or type anything — that's Streamlit. Every interaction triggers a full re-run of your Python script from line 1, so without protection, variables like the secret number would reset on every click. `st.session_state` is a dictionary that survives these reruns; you store anything persistent there and guard initialization with `if "key" not in st.session_state`. The subtler issue is render order: if you display a value at the top of the script, it shows the state from before the current interaction ran. We fixed this using `st.empty()` placeholders that get filled in after the logic executes.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

---

One habit I want to reuse is describing bugs in plain English to the AI before touching any code. Explaining what I observed (e.g. "the hint shows correctly but score and history only update after the next guess") let Claude pinpoint the render-order cause immediately, rather than me spending time guessing.

One thing I'd do differently is test AI suggestions incrementally instead of accepting several fixes at once. The debug expander bug showed that one fix can introduce a new problem — smaller changes with immediate manual testing would have caught that faster.

This project changed how I think about AI-generated code, it's a strong first draft that still requires you to understand what it's doing. Claude caught most bugs but also introduced one, and I only caught it because I was actively using the app rather than just reading the code.
