# Judhur product contract

## Product Vision

Judhur is a calm, learner-focused Qur'anic Arabic lexicon and explorer. Given a Qur'anic word, it helps a learner understand the Word, its Lemma, its Root, its meaning, every place it occurs in the Qur'an, and its derivational relationships with other words from the same Root.

The value is a coherent learning workflow for information that may otherwise require several disconnected tools. This is a first side project: the priority is a clear, useful MVP, with trustworthy sources and visible uncertainty. Judhur supports language study without claiming authority over translation, exegesis, or Islamic law.

## Core Concepts

| Concept | Product meaning |
| --- | --- |
| **Word** | A normalized linguistic word identity used for grouping and search, distinct from a single Qur'anic token occurrence. |
| **Lemma** | The lexical or dictionary identity grouping all grammatical realizations of the same lexical item. |
| **Root** | The consonantal root associated with a Lemma, such as مُؤْمِن → أ م ن. |
| **Derived Form** | Form I–X in the traditional sarf sense. A Root Form is a particular Root's derivational family within one of these forms. |

External additions such as و، ف، ب، ل، and ال do not automatically create a new Word. For example, وَالْمُؤْمِنَات, الْمُؤْمِنَات, and مُؤْمِنَات may resolve to one Word when the underlying noun identity is the same.

The Words مُؤْمِن, مُؤْمِنُون, مُؤْمِنِين, مُؤْمِنَة, and مُؤْمِنَات all belong to the Lemma مُؤْمِن. A Word and its Lemma can share the same Arabic spelling while remaining conceptually distinct.

“Form” is reserved for Derived Forms I–X; it is not the name of the Word layer. A noun or participle can derive from a verb form without itself being a verb. Not every Root has every Derived Form attested, and a generic pattern does not determine the meaning of every derivative.

## MVP User Flows

1. Search a Qur'anic Word in Arabic, with or without harakat.
2. See the Word, its definition, its Lemma, and its Root, with links between their views.
3. View every Qur'anic occurrence of that Word and follow a reference to Quran.com.
4. Explore all Words belonging to the Lemma and their occurrences.
5. Explore the Root's derivational families, meanings, and Qur'anic usage grouped by Forms I–X.

A learner can also search for a Root directly. There is no dedicated Ayah or verse reader in the MVP; occurrence references link out to Quran.com.

## Product Views

### Word View

Show the Arabic Word, its definition, a linked Lemma, and a linked Root. Show occurrences across sources: Qur'an in the MVP, with Hadith added in M7. The Qur'an occurrence list has its total count at the top, followed by references ordered by Surah then Ayah, each linked to Quran.com.

A technical analysis section or link is added in M6, not required in the MVP.

### Lemma View

Show the Lemma, its definition, a Root link, its Derived Form I–X association where applicable, and all Words belonging to it. Morphological identity is added in M5.

Group occurrence information by Word. For the Lemma مُؤْمِن, show separate entries such as مُؤْمِن, مُؤْمِنُون, مُؤْمِنِين, مُؤْمِنَة, and مُؤْمِنَات, each with its occurrence count. Selecting a Word reveals or navigates to every Qur'anic occurrence of that Word.

### Root View

Organize the Root around its attested Derived Forms I–X. For every form that occurs, show the Form number, sarf pattern where useful, root-specific meaning or definition, Words and Lemmas derived from it, and Qur'anic occurrences grouped under it.

For س ل م, example groups are:

| Root Form | Pattern | Example members |
| --- | --- | --- |
| **Form II** | فَعَّلَ | سَلَّمَ، تَسْلِيم |
| **Form IV** | أَفْعَلَ | أَسْلَمَ، إِسْلَام، مُسْلِم |

Each group includes its own root-specific definition and Qur'anic usage. The examples describe derivational membership, not interchangeable meanings. This organization is a defining MVP feature.

## Matching Rules

### Word matching — fi‘l

A Word match requires the same conjugational identity. Identified external clitics or additions do not automatically create a different Word: وَكَتَبَ, فَكَتَبَ, and كَتَبَ may resolve to the same Word.

Different conjugations remain different Words. كَتَبَ, كَتَبُوا, كَتَبْتُ, يَكْتُبُ, and اُكْتُبْ must not all collapse into one Word.

### Word matching — ism

A Word match requires the same lexical item, number (singular, dual, or plural), and gender (masculine or feminine). ال and identified external clitics do not automatically create a separate Word. Case or status creates a different Word only when it changes the actual morphological form: مُؤْمِنُون and مُؤْمِنِين are different Words.

### Lemma matching

For fi‘l, all conjugations of the same lexical verb map to one Lemma. For ism, all grammatical or morphological realizations of the same lexical noun, adjective, or participle map to one Lemma.

### Root Form grouping

Group all words derived from the same sarf Form of the same Root together. For س ل م, أَسْلَمَ, إِسْلَام, and مُسْلِم belong to Form IV; سَلَّمَ and تَسْلِيم belong to Form II.

These identity rules support grouping from the early milestones; detailed analysis displays remain post-MVP. Harakat-insensitive retrieval must not merge distinct linguistic identities. External additions must be identified rather than removed by blindly stripping initial letters.

## Search Behavior

MVP search supports Arabic Words and Roots, with or without harakat. Preserve original Arabic for display and maintain a separate normalized search representation.

Word search results show the matched Word and its Root. Root results clearly identify the Root and link to Root View. Use “Root,” never “root word.” If normalization yields several possible Words, preserve them as distinct candidates rather than guessing their identity.

English gloss search, transliteration search, verse-reference search, complex ranking tiers, advanced filters, and sophisticated autocomplete are optional future enhancements.

## Occurrences

An occurrence is a use of a Word at a source location, not the Word identity itself. Preserve the original source Arabic and its reference. Different source spellings can map to one Word under the matching rules.

Every occurrence list shows its total number of occurrences at the top and references in Qur'anic text order: ascending Surah number, then Ayah number. This is text order, not an inferred chronology of revelation. Count occurrences, not merely distinct verses; repeated uses within a verse remain separate occurrences. Each reference links to the corresponding verse on Quran.com.

Word View lists every Qur'anic occurrence of the Word. Lemma View groups occurrences by Word, and Root View groups them under their Derived Forms. No internal Ayah page or token inspector is required.

## Milestones

| Milestone | Deliverable |
| --- | --- |
| **M1** | Functioning UI with hardcoded Words, Lemmas, and Roots; Word and Root search; navigation Word → Lemma → Root. |
| **M2** | Store Words, Lemmas, Roots, and definitions in the database; populate views from the database instead of hardcoded data. No technical analysis or morphological identity display yet. |
| **M3** | Show Qur'anic occurrences of Words. |
| **M4 — MVP** | Add derived Forms I–X of Roots, meanings/definitions for each Root Form, Words/Lemmas organized by Form, and Qur'anic usage/occurrences grouped by Form. |
| **M5** | Add morphological identity of Lemma. |
| **M6** | Add technical analysis of Word. |
| **M7** | Add Hadith occurrences. |
| **M8** | Add cross-root semantic relationships and related concepts. |

## MVP Scope

M1–M4 together define the MVP. A learner can search Arabic Words or Roots, read definitions, navigate Word/Lemma/Root relationships, explore all Words of a Lemma, view every Qur'anic occurrence of a Word, and explore root-specific meanings and usage through Forms I–X.

M5–M8 remain post-MVP. Small curated fixtures are appropriate for early milestones, but they do not replace the MVP requirement to show every Qur'anic occurrence of a Word.

## Data Strategy

Prefer ingesting an open Qur'anic linguistic dataset into Judhur's own database rather than depending on an external API at runtime. Candidates to evaluate include the Quranic Arabic Corpus downloadable morphology data, QuranMorph, and maintained or corrected forks where useful. Dataset choice, coverage, and license suitability remain open implementation questions; listing a candidate is not approval to redistribute it.

Preserve provenance and attribution for definitions, relationships, and occurrences. Keep original Arabic separately from normalized search text. Mark missing or disputed linguistic relationships explicitly rather than silently guessing them. Use lightweight checks that imported records map to the intended Words and valid references; do not build a generalized alignment or conflict-resolution system.

Definitions are a separate data problem. Early milestones may use a small curated set of trusted definitions for selected Words, Lemmas, and Roots; M4 also needs definitions for Root Forms.

Do not add versioned corpus publication pipelines, editorial workflows, learner accounts, saved-word systems, or distributed infrastructure unless directly required by the current milestone.

## Post-MVP Features

M5 adds Lemma morphological identity. M6 adds Word technical analysis iteratively, starting with part of speech: فعل, اسم, and حرف.

- For ism, later analysis can include morphology, case/status, number, gender, definiteness, and related grammatical properties.
- For fi‘l, later analysis can include past/present/imperative, person, gender, number, voice, mood, and Derived Form. Derived Form grouping already exists in M4; its technical analysis presentation is later.

M7 adds Hadith occurrences. M8 adds cross-root semantic relationships and related concepts. An Ayah grammar view and richer search are possible future enhancements, not prerequisites for the MVP.

## Non-Goals

The MVP excludes Hadith and Classical Arabic books corpora, cross-root semantic relationships, full technical morphology or grammar analysis, full i‘rab, an Ayah grammar view, dependency trees, accounts, saved words, spaced repetition, editorial workflows, and community contribution systems.

It does not provide a dedicated Qur'an reader or make interpretive or theological claims from morphological relationships.

## MVP Success Criteria

A learner can:

1. Search a Qur'anic Word with or without harakat.
2. See its definition.
3. See and navigate to its Lemma.
4. See and navigate to its Root.
5. View every Qur'anic occurrence of the Word, with a total count and ordered Quran.com references.
6. Explore all Words belonging to the Lemma, with occurrences grouped by Word.
7. Explore the Root organized by Derived Forms I–X.
8. Understand the meaning or definition associated with each Root Form.
9. See Qur'anic usage and occurrences grouped under those Root Forms.

The same experience supports direct Root search, uses persisted data by M2, preserves source Arabic, identifies sources, and makes uncertainty visible.

## Open Questions

1. Which dataset and license best support the required Word/Lemma/Root relationships and complete Qur'anic occurrence lists?
2. Which trusted sources will supply definitions for Words, Lemmas, Roots, and Root Forms?
3. How should entries with missing or disputed Root Form assignments appear while keeping the uncertainty explicit?
