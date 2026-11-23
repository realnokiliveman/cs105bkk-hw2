# HW2 notes

## What did you try?

I did the parts in the order the assignment gave: errors.py, then event.py, then club.py, running python -m pytest after each. For event.py I first wrote the simplest version of each method. sign_up checked only `seats`, cancel used list.remove, and place was a single `self.waitlist.index(member)`. In club.py, handle did `line.split(",")` and picked the three parts by position, with a bare `except:` around the whole command.

## What broke, and how did you fix it?

My first run failed 13 of 20 tests.
- In errors.py I set `self.message` but never called `super().__init__(...)`, so `str(error)` printed a tuple like `('Nino', 'Muay Thai basics')` instead of the sentence. Calling super().__init__ with the f-string fixed it. I also forgot `{title!r}`, so UnknownEventError had no quotes around the title.
- In event.py, `place` raised a plain ValueError for a stranger instead of NotSignedUpError, and it forgot that a seat-holder is 0 and that waitlist numbers start at 1. `cancel` never moved the first waiting member up and returned nothing, and a stranger gave a ValueError from list.remove. I rewrote cancel with three branches (waitlist, seat, neither) and caught the ValueError in place to raise NotSignedUpError.
- In club.py, `attendance` crashed with a KeyError because `counts[member] += 1` fails on a new key (switching to Counter fixed it). `event()` leaked a raw KeyError until I wrapped it in try/except and raised UnknownEventError.
- `handle("hello")` crashed with an IndexError because I indexed `parts[1]` without checking the line had three parts. I now check `len(parts) == 3` and that the command is join or leave first.
- After the tests passed, trying the edge cases myself showed more problems. The bare `except:` would have hidden real bugs, so it is now `except SignupError`. `add_event("Board games", 0)` raised ValueError but had already stored the event, so I moved the checks before the store. `JOIN` in capitals needed `.lower()`.

## What would you do differently next time?

I would read the whole rule list before writing code. Most of my bugs were rules the assignment stated plainly, such as "changes nothing", "stores nothing" and "catch SignupError and nothing else". I would turn each rule into a small test or a quick try in the Python shell before trusting my code, instead of relying only on the visible tests, because the grading tests cover more cases. I would also write checks first and actions second, so a failed check can never leave half-finished state, as happened in add_event.