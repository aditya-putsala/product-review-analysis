# Product Review Analysis

Analysing the sentiment of about 23,000 customer reviews of eight mobile phones, using text cleaning, lemmatisation and the VADER sentiment analyser.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Results at a Glance](#results-at-a-glance)
- [Dataset](#dataset)
- [Data Preparation](#data-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Approach](#approach)
- [Sentiment Results](#sentiment-results)
- [Known Issues and Limitations](#known-issues-and-limitations)
- [Key Takeaways](#key-takeaways)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Tech Stack](#tech-stack)

---

## Problem Statement

Online shoppers leave thousands of reviews, which are too many to read by hand. The task is to turn the reviews of eight mobile phones into something a product team can use: how customers rate each phone, what they talk about most, and whether the written feedback is positive, negative or neutral.

This is a **text-analysis (NLP) problem using a pre-built, lexicon-based sentiment tool**. No model is trained, so there is no train/test split and no accuracy score in the notebook.

---

## Results at a Glance

| Item | Value |
|------|-------|
| Reviews / columns in the CSV | 23,777 / 5 (one is an index column) |
| Reviews analysed after removing missing values | 23,158 |
| Phones | 8 |
| Star ratings | 5★ 39.2%; 4★ 20.5%; 3★ 11.4%; 2★ 6.6%; 1★ 22.3% † (mean 3.48 †) |
| Sentiment from the notebook's pipeline † | Positive 15,057 (65.0%); Neutral 3,646 (15.7%); Negative 4,455 (19.2%) |
| Sentiment from VADER on the raw review text † | Positive 57.9%; Neutral 15.1%; Negative 27.0% |
| Most-reviewed phone | Redmi 9 Activ (4,450 reviews analysed) |
| Highest share of positive reviews † | OPPO A31 (69.4%) |
| Highest share of negative reviews † | Samsung Galaxy M32 (26.0%) |

Figures marked † were calculated for this README by re-running the notebook's pipeline on the CSV; they are not saved as output in the notebook. **The saved notebook stops with an error part-way through** (see [Known Issues and Limitations](#known-issues-and-limitations)), and the pipeline's sentiment labels turn out to over-state positivity, so treat the 65% positive figure with care.

---

## Dataset

`Mobile_Reviews.csv` has **23,777 rows and 5 columns**, one row per customer review. The source and the date range are not stated in the notebook. The rating text ("5.0 out of 5 stars") and some review text (which mentions Amazon) suggest the reviews were collected from Amazon India listings.

| Column | Description | Values in the data |
|--------|-------------|--------------------|
| `Unnamed: 0` | Row index saved with the file | 0 to 23,776 (dropped by the notebook) |
| `Review-Title` | Short title of the review | 3 missing; the most common title is "Good" (1,295 times) |
| `rating` | Star rating as text | "1.0 out of 5 stars" to "5.0 out of 5 stars" |
| `Review-Body` | Full review text | 618 missing; the most common body is "Good" (962 times) |
| `Product Name` | Phone being reviewed | 8 different phones (below) |

**Reviews per phone**

| Phone | In the CSV | After removing missing values |
|-------|-----------:|------------------------------:|
| Redmi 9 Activ (4GB RAM, 64GB) | 4,460 | 4,450 |
| OPPO A31 (6GB RAM, 128GB) | 4,330 | 4,273 |
| Redmi 10 Prime (4GB RAM, 64GB) | 3,768 | 3,597 |
| OnePlus Nord CE 2 5G (8GB RAM, 128GB) | 3,570 | 3,395 |
| Redmi Note 11 (4GB RAM, 64GB) | 2,724 | 2,642 |
| realme narzo 50A (4GB RAM, 64GB) | 2,166 | 2,100 |
| vivo iQOO Z6 5G (6GB RAM, 128GB) | 1,419 | 1,365 |
| Samsung Galaxy M32 | 1,340 | 1,336 |

---

## Data Preparation

The notebook applies these steps in order:

1. Drop the index column `Unnamed: 0`.
2. Drop the 3 rows with a missing `Review-Title`, then the 616 further rows with a missing `Review-Body`. This leaves **23,158 reviews** (619 rows removed).
3. Convert `rating` from text to a number (`rating_number`, 1 to 5) by taking its first character.
4. Lower-case the review text.
5. Remove web links.
6. Remove punctuation.
7. Remove English stop words (the NLTK list).
8. Try to remove digits (this step does not work, see [Known Issues and Limitations](#known-issues-and-limitations)).
9. Tag each word's part of speech and **lemmatise** it with WordNet (for example "batteries" becomes "battery").
10. Score the cleaned text with VADER and label it: compound score below 0 is Negative, exactly 0 is Neutral, above 0 is Positive.

The word clouds for 5★ and 1★ reviews are drawn from the raw review text, not the cleaned text.

---

## Exploratory Data Analysis

Figures marked † were calculated from the CSV for this README. The rest are printed in the notebook.

**Rating distribution** † (23,158 reviews)

| Rating | Reviews | Share |
|:------:|--------:|------:|
| 5★ | 9,068 | 39.2% |
| 4★ | 4,749 | 20.5% |
| 3★ | 2,650 | 11.4% |
| 2★ | 1,530 | 6.6% |
| 1★ | 5,161 | 22.3% |

**Most frequent words** (after cleaning, before lemmatisation; as printed in the notebook)

| Word | Count | Word | Count |
|------|------:|------|------:|
| good | 9,836 | price | 1,912 |
| phone | 8,527 | buy | 1,785 |
| camera | 6,128 | like | 1,781 |
| quality | 4,335 | dont | 1,484 |
| battery | 3,457 | bad | 1,464 |
| mobile | 3,096 | also | 1,441 |
| product | 2,743 | best | 1,431 |
| nice | 2,144 | life | 1,345 |
| one | 1,340 | display | 1,305 |
| money | 1,246 | performance | 1,107 |

**Topics mentioned** † (reviews containing a keyword; a keyword match shows a topic was mentioned, not that it caused the rating)

| Topic (keyword) | Reviews | Share of reviews | Mean rating | Reviews rated 1★ or 2★ |
|-----------------|--------:|-----------------:|------------:|----------------------:|
| camera | 5,763 | 24.9% | 3.22 | 32.4% |
| battery | 3,456 | 14.9% | 3.65 | 22.0% |
| price or money | 3,092 | 13.4% | 3.61 | 25.1% |
| display or screen | 2,136 | 9.2% | 3.10 | 36.6% |
| charge or charger | 1,511 | 6.5% | 3.20 | 33.0% |
| hang or lag | 1,490 | 6.4% | 2.17 | 64.0% |
| sound or speaker | 792 | 3.4% | 3.05 | 35.2% |
| heating | 642 | 2.8% | 2.61 | 50.8% |
| *All reviews* | 23,158 | | 3.48 | 28.9% |

**Review length** † (words in `Review-Body`)

| Length | Reviews | Mean rating |
|--------|--------:|------------:|
| 1 to 3 words | 6,741 | 4.10 |
| 4 to 10 words | 6,491 | 3.51 |
| 11 to 30 words | 6,887 | 3.11 |
| Over 30 words | 3,039 | 2.85 |

**Key observations**
- **Mixed ratings:** the most common rating is 5★ (39.2%), but 1★ is the second most common (22.3%), ahead of 4★ (20.5%). Overall, 28.9% of reviews are 1★ or 2★. This matches the notebook's note that 5★ is most common, followed by 1★ and 4★.
- **What people mention:** "good", "phone", "camera", "quality" and "battery" are the most frequent words. The camera is the most discussed topic (about a quarter of reviews).
- **Problem topics:** reviews that mention hanging or lag (mean rating 2.17) or heating (2.61) are the lowest rated, and about two-thirds of the "hang or lag" reviews give 1★ or 2★. Reviews mentioning the battery are rated above average (3.65).
- **Short reviews are happier:** 29% of reviews are three words or fewer, and they average 4.10 stars. Longer reviews are more critical (2.85 stars above 30 words). Many reviews carry little detail (the one-word body "Good" appears 1,016 times, ignoring case and spacing †).
- **Word counts lack context:** a count of "life" or "money" does not say whether it comes from "battery life" and "value for money" or from complaints, so these words need a closer look before drawing conclusions.
- **Messy text:** about 10% of reviews contain non-English characters †, many reviews mix Hindi and English written in Latin script (for example one visible in the notebook's output), and 795 reviews contain the page text "The media could not be loaded." †, which is not part of what the customer wrote.

---

## Approach

| Step | Tool |
|------|------|
| Tokenising, stop words, part-of-speech tagging, lemmatising | NLTK |
| Sentiment score | VADER (`SentimentIntensityAnalyzer`), using the compound score |
| Labels | compound below 0 is Negative; exactly 0 is Neutral; above 0 is Positive |
| Visuals | Word clouds for 5★ and 1★ reviews; rating histogram; sentiment pie chart; per-phone counts |

VADER is a rule-based tool tuned for short English social-media text. It needs no training data, but it was not trained on phone reviews and it cannot read Hindi. The VADER authors suggest cut-offs of ±0.05 for the positive and negative labels. The notebook uses 0. Switching to ±0.05 moves the shares by less than one percentage point †.

---

## Sentiment Results

**Overall** † (notebook pipeline)

| Sentiment | Reviews | Share |
|-----------|--------:|------:|
| Positive | 15,057 | 65.0% |
| Neutral | 3,646 | 15.7% |
| Negative | 4,455 | 19.2% |

The notebook's text states 65% positive, 15.7% neutral and 19.3% negative. My re-run matches except for the negative share (19.2%), and the per-phone counts differ from the notebook's by at most 6 reviews, probably because of library versions.

**By phone** † (shares of that phone's reviews; sorted by number of reviews)

| Phone | Reviews | Mean rating | Positive | Neutral | Negative | Rated 1★ or 2★ |
|-------|--------:|:-----------:|---------:|--------:|---------:|--------------:|
| Redmi 9 Activ | 4,450 | 3.4 | 67.6% | 11.7% | 20.7% | 29.8% |
| OPPO A31 | 4,273 | 3.7 | 69.4% | 16.8% | 13.8% | 23.8% |
| Redmi 10 Prime | 3,597 | 3.3 | 57.4% | 18.8% | 23.8% | 35.8% |
| OnePlus Nord CE 2 5G | 3,395 | 3.7 | 67.9% | 16.9% | 15.2% | 23.0% |
| Redmi Note 11 | 2,642 | 3.3 | 61.1% | 15.8% | 23.2% | 32.1% |
| realme narzo 50A | 2,100 | 3.7 | 68.0% | 16.7% | 15.2% | 24.0% |
| vivo iQOO Z6 5G | 1,365 | 3.3 | 59.6% | 18.8% | 21.7% | 32.3% |
| Samsung Galaxy M32 | 1,336 | 3.2 | 64.2% | 9.7% | 26.0% | 36.6% |

**Sentiment label against star rating** † (share of reviews at each star level that VADER labelled each way)

| Stars | Negative | Neutral | Positive |
|:-----:|---------:|--------:|---------:|
| 1★ | 51.9% | 21.9% | 26.3% |
| 2★ | 36.9% | 23.3% | 39.8% |
| 3★ | 24.3% | 20.5% | 55.2% |
| 4★ | 6.9% | 13.1% | 80.0% |
| 5★ | 2.6% | 11.0% | 86.3% |

**Same check with VADER run on the raw, uncleaned text †**

| Stars | Negative | Neutral | Positive |
|:-----:|---------:|--------:|---------:|
| 1★ | 63.6% | 20.8% | 15.6% |
| 2★ | 53.1% | 22.2% | 24.7% |
| 3★ | 40.1% | 19.4% | 40.6% |
| 4★ | 13.0% | 12.5% | 74.5% |
| 5★ | 5.3% | 10.8% | 83.9% |

**Agreement with star ratings †** (treating 4★ and 5★ as positive and 1★ and 2★ as negative; 3★ reviews are left out)

| Version | Matches the star-based label | Matches, ignoring reviews labelled Neutral |
|---------|-----------------------------:|-------------------------------------------:|
| Notebook's cleaned text | 72.5% | 85.4% |
| Raw text | 74.3% | 87.0% |

**How to read these numbers**
- **No ground truth:** nobody checked the VADER labels by hand. Star ratings are only a rough yardstick, since a customer can give 1★ for a delivery problem and still write kind words about the phone, but they are the best check available in this data.
- **The cleaned-text labels are too positive:** 1,965 of the 6,691 reviews rated 1★ or 2★ (29%) were labelled Positive by the notebook's pipeline. Running VADER on the raw text gives a more negative picture (27.0% negative against 19.2%) and agrees slightly better with the star ratings. See issue 2 below.
- **Differences between phones are modest.** The mean ratings only run from 3.2 to 3.7, and these reviews come from customers who chose to write one, so they do not necessarily represent all buyers.
- **Count against share:** the notebook's top-3 lists use raw counts, which mostly follow how many reviews each phone has. Shares give a fairer comparison.

---

## Known Issues and Limitations

These affect how far the results can be trusted and are listed so that anyone reusing the code knows about them.

1. **The saved notebook stops with an error, so its results are not saved.** The part-of-speech tagging cell fails with `LookupError: Resource punkt_tab not found`. Newer NLTK versions need the `punkt_tab` and `averaged_perceptron_tagger_eng` resources, but the notebook only downloads the older `punkt` and `averaged_perceptron_tagger`. Every cell after it has no saved output, so the sentiment percentages and top-3 lists written in the notebook's text cells are not backed by saved results. I re-ran the pipeline with the missing resources downloaded and got matching figures (see [Sentiment Results](#sentiment-results)).
2. **The cleaning steps weaken VADER.** VADER is built to read raw text, including negations, punctuation and capitalisation, but the notebook strips these out first. The NLTK stop-word list includes "not", "no" and "nor", so they are deleted. 28.4% of reviews contain at least one of these words †. For example, VADER scores "not good" at -0.34, but once "not" is removed the text is just "good", which scores +0.44. In total, 1,919 reviews that the pipeline labels Positive are labelled Negative when VADER reads the raw text, against 284 in the other direction †. VADER should be run on the raw text (or at least with negations kept).
3. **The "Neutral" label is a catch-all.** It means the compound score is exactly 0. It covers genuinely neutral reviews, very short reviews (34.5% of the Neutral reviews are three words or fewer †), reviews in Hindi or mixed Hindi and English that VADER cannot read, and reviews that mix praise and complaint. Neutral reviews are spread across the whole rating scale (30.9% are 1★ and 27.4% are 5★ †).
4. **Digit removal does not work.** `str.replace('\d+', '')` is used without `regex=True`, so in current pandas versions it looks for the literal text `\d+` and changes nothing. After that step, 4,585 reviews still contain digits †. The string should also be written as a raw string (`r'\d+'`), which is what the `SyntaxWarning` in the notebook is about. VADER does not care much about digits, but the word counts and any later text analysis would.
5. **Comparing phones by counts is misleading.** The notebook lists the "top 3" phones for each sentiment by number of reviews. Redmi 9 Activ appears at the top of both the positive and the negative lists mostly because it has the most reviews. By share, Samsung Galaxy M32 has the highest negative share (26.0%) yet does not appear in the notebook's top-3 negative list at all, and Redmi 10 Prime has the lowest positive share (57.4%).
6. **Duplicates were not checked.** The CSV contains 1,162 rows that are identical in title, rating, body and phone †, and 1,125 of them remain after cleaning †. They may be scraping duplicates or separate customers who wrote the same short review (for example "Good"); the data cannot say which.
7. **Page text in the reviews.** 795 reviews contain "The media could not be loaded." †, which is page text picked up when the reviews were collected. It adds the words "media", "could" and "loaded" to those reviews.
8. **Rows removed that were not needed.** `Review-Title` is never used, but 3 rows were removed for missing titles. The 618 reviews with no body still carry a star rating, so they could stay in any rating analysis. (Their ratings: 331 are 5★, 137 are 4★, 53 are 3★, 28 are 2★ and 69 are 1★ †.)
9. **Phone names are untidy.** Some contain trailing spaces or missing brackets (for example `Redmi 10 Prime (Bifrost Blue 4GB RAM 64GB ROM `), and the name includes the colour and storage option. The notebook does not say whether different colours or storage options of the same phone were merged.
10. **Source and sampling unknown.** The notebook does not say where or when the reviews were collected. Reviews are written by self-selected customers, so the results describe the people who reviewed, not all buyers.
11. **Efficiency.** The stop-word list is rebuilt for every word in the part-of-speech step, which makes the cell much slower than needed.

---

## Key Takeaways

- Star ratings are mixed: 39.2% of reviews give 5★, but 28.9% give 1★ or 2★, so the average (3.48) hides two quite different groups.
- The camera is the most discussed topic (about a quarter of reviews), while reviews that mention hanging or lag (64% rated 1★ or 2★) and heating (51%) are the most negative.
- Using the notebook's own method, about 65% of reviews are Positive, 19% Negative and 16% Neutral. This most likely **over-states** positivity because stripping "not" and "no" makes some complaints read as praise. On raw text the split is about 58% Positive, 27% Negative and 15% Neutral.
- OPPO A31, OnePlus Nord CE 2 5G and realme narzo 50A have the highest mean ratings (3.7) and Samsung Galaxy M32 the lowest (3.2). Redmi 10 Prime and Samsung Galaxy M32 have the largest shares of 1★ and 2★ reviews. The gaps are modest.
- Before relying on the sentiment labels, run VADER on the raw text, fix the NLTK downloads, handle Hindi-English reviews, and remove duplicate and page-text noise.

---

## Getting Started

**1. Install the libraries**
```bash
pip install pandas numpy matplotlib seaborn nltk vaderSentiment wordcloud jupyter
```

**2. Download the NLTK resources** (newer NLTK versions need the last two as well as the first five)
```python
import nltk
for pkg in ["punkt", "stopwords", "wordnet", "omw-1.4",
            "averaged_perceptron_tagger", "punkt_tab",
            "averaged_perceptron_tagger_eng"]:
    nltk.download(pkg)
```

**3. Place the dataset next to the notebook.** The notebook loads the data with `pd.read_csv('Mobile Reviews.csv')` (with a space), so rename `Mobile_Reviews.csv` to match, or change the file name in that cell.

**4. Open the notebook**
```bash
jupyter notebook Product_Review_Analysis.ipynb
```

**5. Run the cells in order.** The part-of-speech and lemmatising cells take a while on 23,000 reviews.

---

## Repository Structure

```
.
├── Product_Review_Analysis.ipynb   # Cleaning, word clouds, text processing and VADER sentiment
├── Mobile_Reviews.csv              # 23,777 reviews x 5 columns
└── README.md
```

---

## Tech Stack

Python, pandas, NumPy, Matplotlib, Seaborn, NLTK (tokenising, stop words, part-of-speech tagging, WordNet lemmatiser), VADER Sentiment, WordCloud, Jupyter Notebook.
