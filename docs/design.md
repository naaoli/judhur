# Judhur design document

## Product vision

Judhur is a focused Arabic-learning product for exploring the Qur'an through meaningful language relationships. It lets a learner move from a word in a verse to its lemma and root, see related occurrences, and build an intuitive feel for how Arabic forms carry meaning across the text.

The product should feel calm, trustworthy, and fast. It is a learning and exploration tool: it makes the text and its linguistic structure legible without presenting itself as an authority on translation, exegesis, or Islamic law.

Its first audience is an English-speaking learner who can read Arabic script or is learning to do so, wants context instead of isolated vocabulary cards, and values source attribution and clear uncertainty.

## Product model and terminology

Judhur models four linked linguistic entities. Word, Lemma, and Root are navigable views; Segments preserve the source analysis within each Word:

| Term | Meaning | Primary use in the product |
| --- | --- | --- |
| **Word** | A single token as it appears in a Qur'anic verse, including its exact spelling and position. | Read a verse and inspect the form in context. |
| **Segment** | A source-defined morphological part of a Word, such as an attached particle, prefix, stem, or suffix, with its own part of speech and features. | Explain how a displayed Word is constructed without conflating the analyses of its parts. |
| **Lemma** | The dictionary headword that groups inflected forms with the same lexical item. | Learn a word's core meaning and browse every form of that lexical item. |
| **Root** | The consonantal root associated with one or more lemmas, normally three radicals. | Explore related vocabulary and recurring semantic patterns. |

These are distinct identities, not interchangeable labels. A Word may contain one or more ordered Segments in a selected source analysis. Part of speech and morphological features belong to the Segment that asserts them. A Word's selected primary lemma is the lemma of its lexical stem Segment; attached particles, prefixes, and suffixes retain their own analyses and do not replace or merge with that lemma. If a source supplies no identifiable stem, or the stem analysis remains disputed, the Word's primary lemma is unavailable. A lemma may be associated with one root, no root, or a disputed root. A root can group multiple lemmas whose meanings are related but are not necessarily interchangeable. The interface must show the source analysis and preserve uncertainty rather than silently forcing a relationship.

### Word view

The Word view starts from one highlighted token in a verse. It shows the token, transliteration, concise gloss, verse reference, surrounding verse text, and its ordered Segments. Morphology such as part of speech, person, number, gender, case, mood, and verb form is shown on the Segment that carries it; prefixes, stems, suffixes, and attached particles remain visibly distinct. The view links from the lexical stem to its selected Lemma and Root views and defaults to occurrences of the same exact surface form, with normalized variants available as a separate set.

### Lemma view

The Lemma view presents the Arabic headword, transliteration, concise learning glosses, its root when available, and a frequency count. It lists every Qur'anic occurrence grouped by form, with each result retaining verse context. Learners can move from the lemma to a form or a verse without losing their place.

### Root view

The Root view presents the radicals in Arabic, a transliteration, a cautious semantic orientation, and the lemmas associated with the root. It surfaces their frequencies and occurrence links. It must state that roots suggest historical and morphological relationships; they do not determine a word's meaning in every context.

## Core user flows

### Read and inspect a verse

1. A learner opens a surah or navigates directly to an ayah.
2. Judhur renders Arabic text with accessible token boundaries and the selected translation.
3. The learner selects a word.
4. A compact inspector gives its gloss and links to the Word, Lemma, and Root views; an expanded state provides morphology and occurrence links.
5. The learner follows a relationship or returns to uninterrupted reading.

### Search and explore

1. A learner enters Arabic, a transliteration, an English gloss, a root, or a verse reference.
2. Search recognizes the input type, evaluates exact and normalized forms separately, and returns the most useful matching Words, Lemmas, Roots, and verse references.
3. Results identify the matched level so a surface-form result cannot be mistaken for a lemma or root result.
4. The learner opens a result and can pivot across the three views.

### Follow a recurring pattern

1. From a Word or Lemma view, the learner chooses a root.
2. Judhur lists the root's associated lemmas and their occurrences.
3. The learner compares verse context for selected forms and returns to the original verse through a stable back-navigation path.

## Matching rules

Matching is deterministic, source-backed, and explainable.

- An **exact surface-form match** compares the query with retained canonical token text after Unicode canonical composition. It preserves harakat, tatweel, hamza and alif distinctions, attached particles, and all other letters; it does not imply a lemma match.
- A **normalized surface-form match** compares normalized Arabic tokens after removing tatweel and optional harakat and folding the configured common alif and hamza variants. It is a separate, lower-ranked match tier and does not imply exact spelling or a lemma match. Display text is never rewritten.
- A **lemma match** matches all Words whose selected lexical stem analysis has that lemma. Lemmas assigned only to non-stem Segments do not make the whole Word a lemma match.
- A **root match** matches lemmas explicitly linked to that root by the active source. It must not infer a root by stripping letters from a token.
- Segments, including attached particles, are inspectable within Word results but are not independent search targets in the MVP. An Arabic surface query therefore matches a complete canonical Word under the exact or normalized rules above.
- Diacritics, punctuation, attached particles, and orthographic variants are retained in source data so exact and normalized match sets remain reproducible.
- Where sources disagree, Judhur records each assertion with its source. The default analysis is configured deliberately and displayed as such; an unresolved relationship is shown as unavailable rather than guessed.

## Search behavior

Search is designed for discovery, then precision.

- Accept Arabic with or without diacritics, root radicals separated by spaces or hyphens, common transliteration spellings, English glosses, surah names, and references such as `2:255`.
- Autocomplete begins after a short input and groups suggestions into Verse, Word, Lemma, and Root sections.
- Match tiers are ranked explicitly: exact Arabic surface-form results first; exact lemma, root, and reference results next; normalized Arabic surface-form results next; and transliteration and English-gloss matches after them. Each result states the tier that produced it.
- English gloss search is intentionally approximate and labels its results as gloss matches, not translations of a specific source. It supports stemming and a small curated synonym set in the MVP.
- Result cards show Arabic, transliteration, a concise gloss, type, frequency where relevant, and one verse reference. Filters support result type and surah; morphology filters are a later milestone.
- Empty results offer normalization suggestions and nearby canonical forms; they never invent a linguistic analysis.

## Qur'anic occurrences

An occurrence is a stable record connecting a canonical Word and its selected analysis to its location: `surah`, `ayah`, token position, canonical Arabic display text, and source identifier. A Word can expose exact and normalized surface forms, ordered Segment analyses, one selected primary lemma/root analysis from its lexical stem, and links to the full verse.

Occurrence lists default to canonical Qur'anic order and show enough context to distinguish sense and grammar. A surface-form list is exact by default and includes only Words whose retained canonical text matches the selected form after the same Unicode canonical composition used for exact matching. A normalized-form list is a separate set; its total includes all exact variants that normalize to the query and shows a per-exact-form breakdown. Lemma occurrence lists and frequencies include Words whose selected lexical stem has that lemma. Counts always label whether they represent exact Word occurrences, normalized Word occurrences, lemma occurrences, distinct exact forms, or distinct lemmas. The MVP does not offer interpretive claims based only on frequency.

## Data-source strategy

Judhur separates immutable text, linguistic assertions, and presentation data.

1. **Qur'anic text:** ingest a verified, versioned Uthmani text source with stable surah, ayah, and token identifiers. Store the original form and a normalized search form separately. These canonical token identifiers define the Words displayed by the product.
2. **Morphology and relations:** ingest a provenance-preserving linguistic corpus that supplies source Words, ordered Segments, morphology, lemmas, and roots. The initial candidate is the Quranic Arabic Corpus, subject to confirming its license, coverage, and permitted redistribution. Other compatible sources may supplement or correct it only through a versioned import. Linguistic assertions attach to canonical Words only through the alignment contract, never by assuming equal token positions.
3. **Translations and glosses:** use clearly licensed translations with version and attribution metadata. Curated learning glosses are product data, separate from translations and linked to their editorial source.
4. **Alignment contract:** each pairing of a linguistic source version and canonical text version has a versioned mapping from source Words and Segments to canonical Word identifiers. The mapping supports one-to-one, one-to-many, and many-to-one relationships so source tokenization does not have to match canonical tokenization. Every verse is validated independently for complete canonical coverage, source order, text correspondence, and mapping cardinality; differences must be recorded as explicit, reviewable mapping decisions. Any unresolved mapping blocks publication of the combined canonical corpus version. A resolved mapping may still carry an explicitly unavailable lemma, root, or morphological assertion when the source does not provide one.
5. **Import contract:** every imported assertion and alignment decision carries source name, source version, license/attribution requirements, import timestamp, and stable external identifier where supplied. Imports are repeatable and produce a per-verse validation report for canonical and source token counts, alignment coverage, unresolved mappings, missing linguistic assertions, and relationship conflicts. Missing assertions and unresolved mappings are reported separately. An absent linguistic assertion alone does not block publication when the mapping is resolved and all other publication checks pass.

No source is treated as a hidden universal truth. The product credits sources where users encounter their data and provides a source-details page.

## Milestones

| Milestone | Outcome |
| --- | --- |
| **1. Corpus foundation** | Versioned Qur'anic text, stable verse/token identifiers, source registry, versioned source alignment, and per-verse publication validation. |
| **2. Linguistic graph** | Word, Segment, Lemma, and Root records; imported morphology; provenance and conflict representation. |
| **3. Exploration prototype** | Verse reader, token selection, and basic Word/Lemma/Root pages validated with a small learner cohort. |
| **4. MVP** | Publicly usable reader, three linked views, reliable search, occurrence lists, attribution, and essential accessibility/performance work. |
| **5. Learning loop** | Saved words, lightweight review, personal notes, and progress without making spaced repetition a prerequisite for exploration. |
| **6. Deeper comparison** | Morphology filters, occurrence comparison, richer transliteration controls, and improved search ranking. |
| **7. Editorial depth** | Curated glosses, usage notes, source comparisons, and a contributor/editorial workflow with review. |
| **8. Technical analysis platform** | Carefully scoped computational analysis, research exports, and transparent visualizations built on validated source data. |

## MVP scope and success criteria

Milestone 4 is the MVP. A learner must be able to open any verse in the selected canonical text, tap or click each token, understand its basic linguistic identity, navigate among Word, Lemma, and Root views, search by Arabic/transliteration/gloss/reference, and inspect every linked occurrence with source attribution.

The MVP succeeds when:

- all supported verses and tokens render correctly with stable references, and every published linguistic source version has fully resolved per-verse alignment to them;
- every displayed linguistic assertion identifies a source or is explicitly marked as editorial;
- common lookup paths return in under one second at typical load;
- a learner can complete the read-inspect-explore flow without needing an account;
- usability sessions show learners can distinguish Word, Lemma, and Root and correctly reach related occurrences; and
- the team can re-import source data, reproduce its alignment decisions, detect changes, and reproduce the published corpus version.

## Post-MVP technical analysis

Post-MVP analysis may add pattern discovery, co-occurrence views, form and lemma distributions, morphology-aware search, and exportable datasets. Each analysis must state its units, normalization, corpus version, exclusions, and whether it uses source assertions or a derived model.

Computational signals are navigation and research aids. They must not be framed as proof of meaning, theology, authorship, chronology, or intent. Any machine-generated clustering, semantic similarity, or transliteration heuristic requires evaluation against curated examples, a visible confidence level, and a path back to the underlying occurrences.

## Non-goals

The MVP does not attempt to provide tafsir, fatwa, doctrinal adjudication, translation replacement, a full Arabic grammar course, automatic root derivation, social discussion, or claims of linguistic finality. It also does not require accounts, personalized learning plans, offline sync, or a native mobile app.

## Open questions

1. Which text, morphology, translation, and transliteration sources meet the required licensing and attribution standards for the first public release?
2. Which linguistic source becomes the default when segmentation, lemma, or root analyses conflict, and how much alternate analysis should be visible in the MVP?
3. What transliteration convention should be primary, and which common input variants must resolve to it?
4. Which translations and learning glosses best serve the first audience while keeping their distinct roles clear?
5. What accessibility baseline is required for Arabic typography, keyboard navigation, screen readers, contrast, and right-to-left layouts?
6. How should learner feedback correct a gloss or relationship without turning the product into an unreviewed interpretive forum?
7. Which privacy model best supports future saved learning data while retaining useful anonymous product metrics?
