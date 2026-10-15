# Clothing Review Sentiment Classifier

A rule-based sentiment classifier that labels women's clothing reviews as **positive**, **negative** or **neutral** using keyword matching, negation handling and an override rule for returned items. Built in Python as part of my AI coursework.

## Overview

Rather than training a machine learning model, this project uses a hand-crafted lexicon approach:

1. **Load** reviews from a CSV file and assign each a true label based on its star rating.
2. **Preprocess** the text (lowercase, strip punctuation, remove stop words).
3. **Classify** each review by counting positive and negative keywords.
4. **Evaluate** the predictions against the true labels and report accuracy.

## How it works

### 1. Loading the data
`loading_reviews(clothing_reviews, limit=400)` reads up to 400 reviews from the CSV and labels them by rating:

| Rating | Label |
|--------|-------|
| 4–5 | positive |
| 3 | *skipped* |
| 1–2 | negative |

Rating 3 reviews are excluded because they are ambiguous and could reasonably be read either way, which would make the evaluation unfair. Reviews with empty text are also skipped.

### 2. Preprocessing
`preprocess_cleaning_reviews(reviews_list)` cleans each review by:
- converting it to lowercase
- removing punctuation
- removing common stop words (e.g. *the, a, is, and, it*) that carry no sentiment

This makes sure different forms of the same word match the keyword lists.

### 3. Classification
`classifying_reviews(cleaned_review)` scores each review word by word:

- **Positive keywords** – e.g. *wonderful, comfortable, love, pretty, gorgeous, perfect, cute, soft, cozy*
- **Negative keywords** – e.g. *bad, unflattering, uncomfortable, cheap, awkward, disappointed, itchy, seethrough*
- **Regex patterns** (e.g. `love\w*`, `comfortabl\w*`) catch word variants such as *loved*, *lovely*, *comfortably*.
- **Negation handling** – if a keyword is directly preceded by *not, never, no, doesnt, wont* or *dont*, its sentiment is flipped (e.g. "not comfortable" counts as negative).
- **Override rule** – any mention of returning, refunding or sending the item back immediately classifies the review as **negative**, since a returned item signals overall dissatisfaction regardless of any positives mentioned.

The final label is whichever count is higher; a tie (including no keywords at all) gives **neutral**.

### 4. Evaluation
`evaluate(cleaned_reviews)` compares each prediction to the true label, prints every misclassified review for error analysis, and returns:
- accuracy
- total reviews
- number correctly classified

## Test cases

The second notebook cell runs six edge-case tests:

| # | Scenario | Expected |
|---|----------|----------|
| 1 | Clear positive review | positive |
| 2 | Clear negative review | negative |
| 3 | Negation ("not comfortable and not pretty") | negative |
| 4 | Returned item despite positive words | negative |
| 5 | Equally mixed review | neutral |
| 6 | No sentiment keywords | neutral |

Run the main cell first, then the test cell. It prints each result and a final `Passed: X/6` summary.

## Getting started

### Requirements
- Python 3
- No external libraries – only the standard library (`csv`, `string`, `re`, `urllib`)

### Running it
1. Open the notebook in Google Colab or Jupyter.
2. Make sure `clothing_reviews.csv` (included in this repo) is in the same directory as the notebook, or let the code download it.
3. Run the main cell to classify the reviews and see the accuracy.
4. Run the test cell to check the edge cases.

### Dataset
`clothing_reviews.csv` must contain at least these columns:
- `Rating` – integer star rating (1–5)
- `Review Text` – the written review

## Limitations

- Keyword lists are hand-picked, so reviews using sentiment words outside the lexicon are classified as neutral.
- Negation only checks the single word immediately before a keyword (e.g. "not very comfortable" is not caught).
- The return override can misfire on reviews that mention returning *other* items or say they *won't* return it.
- Sarcasm and context-dependent phrases are not understood.
- Neutral is never a true label in the dataset, so any neutral prediction counts as incorrect during evaluation.

## Project structure

```
├── README.md
├── clothing_reviews.csv      # review dataset
└── <notebook>.ipynb          # classifier and tests
```

## Author

Jess Kellett
