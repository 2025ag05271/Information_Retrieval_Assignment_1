# Preprocessed reference data (non-authoritative)

This folder is reserved for the vendor-preprocessed BBC dataset
(`datasets/bbc/*.mtx`, `*.terms`, `*.docs`, `*.classes` in the repo root)
used **only** as an optional cross-check appendix.

Rules:

- Do not use these files as the authoritative input for Parts A and B.
- The preprocessed archive has already undergone Porter stemming,
  stop-word removal, and filtering of terms with corpus frequency below three.
- Any comparison against these files must be clearly labeled as a
  non-authoritative reference comparison in the notebook.
