# Can a credit model be unfair even if it never sees gender?

An interactive teaching notebook for financial service providers, built by Women's World Banking. You design a loan portfolio, plant a known amount of historical bias in it, hide gender from a credit scoring model — and then watch whether the model discriminates anyway.

It usually does. This notebook shows you why, measures the damage in your own currency, and demonstrates one honest way to repair it.

## Who this is for

Anyone who has heard — or said — the sentence *"our model can't be biased against women, we don't even use gender."* That belief is common, reasonable-sounding, and wrong in a specific, demonstrable way. The notebook is written for credit risk teams, data scientists, and decision-makers at financial service providers, but it assumes no machine learning background. Every result is explained in plain words, and the numbers are yours: you choose the portfolio size, the gender split, the bias level, the loan economics.

It is a teaching tool. It scores no real people and should not be used to build a production model.

## The idea in three sentences

Bias reaches a gender-blind model through two ingredients working together. First, **biased history**: creditworthy women who were rejected in the past by prejudiced decisions, with those wrong calls written into the records as fact. Second, **proxies**: features like business sector, occupation, or location that quietly reveal gender even when no column names it. With both ingredients present, the model learns the old prejudice from the proxies and repeats it — no gender column required. Remove either ingredient, and the problem largely disappears. You can verify both halves of that claim yourself, inside the notebook, in about two minutes.

## What the notebook does

You answer eight questions: how many customers, what share are men, what share of applicants would truly repay, how much historical bias to plant, how strongly the features reveal gender, the average loan size, the interest earned on a repaid loan, and the loss given default. Every input is validated — type a word, a blank, or an impossible number and it simply explains the problem and asks again.

From there it runs the full story:

**It builds the population honestly.** One shared pool of synthetic applicants, so men and women are equally creditworthy by construction. Every applicant carries two labels: `truth` (would they really repay — something only a simulation can know) and `recorded` (what the bank's files say). The bias you chose is injected only into the records of creditworthy women. The bank sees `recorded`; fairness is always judged against `truth`. This matters, because a bank auditing itself against its own biased records would find nothing wrong.

**It trains a gender-blind model.** Logistic regression on the fourteen features and the recorded labels — exactly what a real lender would have. Gender never enters the model. Scores come from five-fold cross-validation, so every applicant is scored by a model that never saw them.

**It sets the cutoff the way a business would.** With your loan economics, the profit-maximising cutoff has a formula — loss ÷ (loss + profit) — and the notebook derives it, then confirms it against a brute-force scan of every cutoff. When the two disagree, that gap is itself a lesson: it means the model's probabilities are not well calibrated, and the notebook says so. A calibration chart, drawn separately for men and women, shows whether a predicted 70% is a real 70% for both groups.

**It delivers a verdict in your numbers.** Four fairness checks — demographic parity, equal opportunity, predictive equality, predictive parity — each judged in plain language. The notebook doesn't print canned text: each check lands in one of three states (fair, unfair against women, unfair against men) and the sentence you read is chosen by what your inputs actually produced, along with an overall summary, the US regulators' four-fifths rule, and a caution if your groups are too small to trust the percentages.

**It tests whether a fancier model would help.** The same data is run through a calibrated random forest and calibrated gradient boosting. If the gap survives all three models — and with meaningful bias and proxies, it does — the problem was never the algorithm.

**It puts money on the harm.** Every truly creditworthy woman the model wrongly rejects is a good loan the lender didn't make. The notebook counts them and prices the forgone profit, including losses the bank's own biased records would hide.

**It repairs the model, and prices the repair.** The fix is the post-processing approach of Hardt, Price and Srebro (2016): separate cutoffs for men and women, chosen to equalise a fairness measure at the least cost. The cost is measured against the honest baseline — the best that two cutoffs can earn with no fairness rule at all — because two cutoffs beat one on flexibility alone, and that flexibility shouldn't be credited to fairness. Sometimes fairness turns out to be free; the notebook explains why when it happens. It also re-measures the other three fairness definitions after the repair, and explains when — and why — fixing one can bend the others.

## Three experiments worth running

The whole lesson lives in re-running the notebook with different answers:

1. **Set the bias to 0.** The model comes out fair. The algorithm was never the villain.
2. **Keep the bias, set proxy strength to 0.** The surprise: gender-blindness *works*. With no feature revealing gender, bias has no channel into the model. Hiding gender fails in practice only because real features are never this clean.
3. **Turn both up — bias 0.4, proxy 0.9.** Every check fails, the four-fifths rule breaks, and the repair gets expensive. This is inherited discrimination at full strength.

## How to run it

You need Python 3.7 or later with numpy, pandas, scikit-learn, matplotlib and seaborn (see `requirements.txt`), and Jupyter.

```
pip install -r requirements.txt
jupyter notebook gender_bias_in_credit_scoring_v2.ipynb
```

Run the cells from top to bottom. The Step 1 cell asks its questions interactively, so run cells one at a time rather than "Run All" — the notebook will pause and wait for your answers. Suggested starting inputs are printed with each question (2000 customers, 0.6 male share, 0.55 base repayment rate, 0.2 bias, 0.7 proxy strength, loan 1000, interest 0.2, LGD 0.6). The model-comparison step cross-validates three models and takes a minute or two; everything else is fast. Results are fully reproducible: the random seed is fixed, so the same inputs always give the same answers.

## What this notebook is not

The data is synthetic and the features are anonymous, so the notebook demonstrates a *mechanism*, not a measurement — no gap you see here says anything numeric about a real portfolio. The economics are simplified: one loan size, approve or reject only, no rate tiering or repeat lending. The bias modelled is label bias; real credit data also suffers selection bias (you never observe repayment for the people you rejected), which is not covered here. And group-specific cutoffs, the repair demonstrated, are legally contested in some jurisdictions — the notebook presents them as an option with a price tag, not as compliance advice. Numbers come from a single random draw; gaps of a few points can wobble across seeds, which is why the notebook treats differences under five points as noise.

## References

- Hardt, M., Price, E., & Srebro, N. (2016). *Equality of opportunity in supervised learning.* arXiv:1610.02413.
- Chouldechova, A. (2017). *Fair prediction with disparate impact.* Big Data, 5(2).
- Kleinberg, J., Mullainathan, S., & Raghavan, M. (2016). *Inherent trade-offs in the fair determination of risk scores.* arXiv:1609.05807.
- Verma, S., & Rubin, J. (2018). *Fairness definitions explained.* IEEE/ACM International Workshop on Software Fairness.
- Wattenberg, M., Viégas, F., & Hardt, M. *Attacking discrimination with smarter machine learning.* Google Research.

## Contact

Questions? Lots of people have them. Reach Mehrdad Mirpourian at mm@womensworldbanking.org.
