## Comments

- Comments explain **why**, never **what**. A comment restating the line below it gets deleted, not reworded.
- Don't narrate the diff — no `// added for X`, no `// changed to handle Y`. That's what the commit message and blame are for.
- No divider or section-header comments unless the file already uses them.
- Match the surrounding file's comment density. A file with no comments is a decision, not an oversight.

## Doc Comments

- Doc comments (DocC, docstrings, JSDoc) on **public declarations only**. Internal and private get none.
- One line, unless a parameter or return value is genuinely non-obvious.
- Skip the ones that restate the signature: `/// Returns the user's name` on `func userName() -> String` is noise.
