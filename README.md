# Can an LLM check whether a translation follows a styleguide?

A small experiment testing where an LLM-based "judge" succeeds and fails at
checking Spanish→English translations against a localization styleguide that the LLM generated and I checked.

Built by someone from a localization background with no prior coding experience,
with help from Claude for the reasoning and Gemini as the judge model.

## The question

LLMs are being used to check translation quality. But *quality* is
vague. This project tests something specific and checkable: **does a translation
follow a styleguide** — the terminology, register, and formatting rules a human
reviewer enforces?

## What's here

```
data/            Spanish source text + 3 machine translations (DeepL, Google, Gemini)
styleguide/      the 40-rule Spanish→English styleguide, written for this text
results/         human scores + the LLM judge's output
notebook/        the Colab notebook that runs the judge
```

## Method

1. Wrote a specialised Spanish source text (pottery studio copy).
2. Derived a 40-rule styleguide from it — rules spanning:
   - **mechanical** (spelling, number formats, glossary terms)
   - **accuracy** (mistranslation, added meaning, omission)
   - **pace / register** (rhythm, tone — judgment calls)
3. Translated the source with DeepL, Google Translate, and Gemini.
4. Scored the translations by hand, rule by rule (the human baseline).
5. Built an LLM judge to do the same
   automatically, and compared it to the human scores.

## What we found

- **Mechanical rules — reliable.** The judge caught glossary, spelling, and
  number-format errors consistently. It even caught some the human missed.

- **Pace / structure — missed at first, then recovered.** A single judge checking
  all 40 rules at once was blind to rhythm rules. Giving it *one focused job*
  (only pace) recovered them, across 5/5 segments. The failure was task design,
  not model capability.

- **Judgment-call rules — noisy.** On interpretive rules (has the tone shifted?
  was meaning added?), the judge's answers varied run to run — exactly where a
  human reviewer would hesitate too.

- **Validation catches format, not correctness.** Pydantic confirmed the output's
  shape and that rule IDs were real. It cannot tell whether a rule was *applied
  correctly* — that still needs a human.

## Scope — what this is *not*

Building the glossary is a solved problem. Dedicated terminology-extraction
tools (Anchovy, Xbench, memoQ, and others) pull candidate terms from a corpus
faster and more thoroughly than an LLM, and for that job they're the right tool.

But the glossary is the *easy* part of a styleguide. This project is about the
rest — the rules a term extractor can't produce or check:

- **pace and rhythm** ("keep the calm, short-sentence tone")
- **register and address** (the impersonal *se*, forms of "you")
- **context-dependent terms** (*horno* = "kiln" in the studio, "oven" in the
  care instructions — same word, decided by who owns the object)
- **added meaning / omission** (did the translation say something the source
  didn't?)

The experiment is testing
whether an LLM can **apply** a full styleguide to a translation, and finding
where it breaks. Terminology extraction builds the rulebook; it doesn't referee
the match.

## Takeaway

An LLM judge is steady where the answer is objective, noisy where it needs
judgment. It extends a reviewer's reach; it doesn't replace their ear.

Also worth noting: several conclusions in this project were **wrong on the first
pass** and corrected by testing — a "hallucinated" rule ID that turned out to be
real, and a "bad" terminology judge that was just run-to-run variance. Single
runs mislead. The notebook keeps the corrections visible rather than hiding them.

## Reproduce it

1. Open `notebook/` in Google Colab.
2. Get a free Gemini API key (aistudio.google.com/apikey) and add it as a Colab
   secret named `GOOGLE_API_KEY`.
3. Upload the files from `data/` and `styleguide/`.
4. Run the cells top to bottom.

Free tier has rate limits — expect to process a handful of segments at a time.

## Tools

Python · Google Colab · Gemini API · Pydantic · DeepL · Google Translate · Claude
