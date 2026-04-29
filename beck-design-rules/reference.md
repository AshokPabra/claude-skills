# Beck Design Rules — Reference

## Origin

- **Author**: Kent Beck, developed during the creation of Extreme Programming in the late 1990s.
- **Authoritative source**: *Extreme Programming Explained* (1st edition, "The White Book"), p. 57, under the XP practice of Simple Design.
- **Summarized by**: Martin Fowler (2 March 2015) at martinfowler.com/bliki/BeckDesignRules.html

## The Rules

| Priority | Rule | Also Known As |
|----------|------|---------------|
| 1 | Passes the tests | Runs all the tests |
| 2 | Reveals intention | States every intention important to the programmer |
| 3 | No duplication | DRY (Don't Repeat Yourself), SPOT (Single Point of Truth), Once and Only Once |
| 4 | Fewest elements | Has the fewest possible classes and methods |

## Original Formulation (White Book, p. 57)

1. Runs all the tests
2. Has no duplicated logic. Be wary of hidden duplication like parallel class hierarchies
3. States every intention important to the programmer
4. Has the fewest possible classes and methods

## Related Principles

- **DRY** — "Don't Repeat Yourself" from *The Pragmatic Programmer*
- **SPOT** — "Single Point Of Truth"
- **YAGNI** — "You Aren't Gonna Need It" (aligns with rule 4)
- **XP Simple Design** — The broader Extreme Programming practice these rules define

## Further Reading

- J.B. Rainsberger's summary and discussion of the interplay between rules 2 and 3
- Ron Jeffries' writings on simple design
- Ward's Wiki — where these rules were originally discussed and refined
