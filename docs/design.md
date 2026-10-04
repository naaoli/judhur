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

Show the Lemma, its definition, a Root link, and all Words belonging to it. The Lemma may be associated with or derived from a particular Root Form (Form I–X), where applicable. For example, مُسْلِم is derived from Form IV of س ل م, while إِسْلَام is the verbal noun associated with that Root Form. A noun, adjective, participle, or verbal noun may derive from a Form without itself being a verb. Root Form grouping arrives in M4; detailed morphological explanation remains in M5/M6.

Group occurrence information by Word. For the Lemma مُؤْمِن, show separate entries such as مُؤْمِن, مُؤْمِنُون, مُؤْمِنِين, مُؤْمِنَة, and مُؤْمِنَات, each with its occurrence count. Selecting a Word reveals or navigates to every Qur'anic occurrence of that Word.

### Root View

Organize the Root around its attested Derived Forms I–X. For every form that occurs, show the Form number, sarf pattern where useful, root-specific meaning or definition, Words and Lemmas derived from it, and Qur'anic occurrences grouped under it.

Show only Derived Forms attested for that Root in the selected corpus; do not render empty Form I–X sections for unattested forms.

For س ل م, example groups are:

| Root Form | Pattern | Example members |
| --- | --- | --- |
| **Form II** | فَعَّلَ | سَلَّمَ، تَسْلِيم |
| **Form IV** | أَفْعَلَ | أَسْلَمَ، إِسْلَام، مُسْلِم |

Each group includes its own root-specific definition and Qur'anic usage. The examples describe derivational membership, not interchangeable meanings. This organization is a defining MVP feature.

## Matching Rules

### Word matching — fi‘l

A fi‘l Word represents the grammatical realization of the verb itself. Its conceptual identity includes, where applicable:

- Lemma or lexical verb;
- aspect: perfect, imperfect, or imperative;
- person: first, second, or third;
- gender;
- number: singular, dual, or plural;
- voice: active or passive; and
- mood for imperfect verbs when the morphology changes: indicative, subjunctive, or jussive.

External conjunctions, prepositions, and particles are excluded from this identity: وَكَتَبَ, فَكَتَبَ, and كَتَبَ may resolve to the same Word. A subject marker that belongs to the conjugation contributes to Word identity. An attached object pronoun does not by itself create another underlying verb Word: the verb in كَتَبَهُ resolves to the relevant realization of كَتَبَ, with ـه retained as additional grammatical information. These are conceptual rules, not a database column specification.

Different conjugations remain different Words. كَتَبَ, كَتَبُوا, كَتَبْتُ, يَكْتُبُ, and اُكْتُبْ must not all collapse into one Word.

### Word matching — ism

A Word match requires the same lexical item, number (singular, dual, or plural), and gender (masculine or feminine). ال and identified external clitics do not automatically create a separate Word. Case or status creates a different Word only when it changes the actual morphological form: مُؤْمِنُون and مُؤْمِنِين are different Words. Irregular noun behavior and other edge cases will be refined during implementation against real data.

### Lemma matching

For fi‘l, all conjugations of the same lexical verb map to one Lemma. For ism, all grammatical or morphological realizations of the same lexical noun, adjective, or participle map to one Lemma.

### Root Form grouping

Group all words derived from the same sarf Form of the same Root together. For س ل م, أَسْلَمَ, إِسْلَام, and مُسْلِم belong to Form IV; سَلَّمَ and تَسْلِيم belong to Form II.

These identity rules support grouping from the early milestones; detailed analysis displays remain post-MVP. Harakat-insensitive retrieval must not merge distinct linguistic identities. External additions must be identified rather than removed by blindly stripping initial letters.

Domain rules should be validated against real Qur'anic examples before being generalized. If a real counterexample exposes an incorrect abstraction, update the model intentionally rather than forcing the data to fit it.

## Search Behavior

MVP search supports Arabic Words and Roots, with or without harakat. Preserve the original Arabic exactly for display. For search only, maintain a normalized representation that:

- removes harakat/tashkeel;
- removes tatweel;
- removes Qur'anic recitation or annotation marks that are not lexical letters; and
- applies Unicode canonical normalization while preserving core letter distinctions.

The MVP does not aggressively fold ا / أ / إ / آ, ى / ي, or ة / ه. Unicode normalization and mark removal must not accidentally erase those distinctions, including hamza or madda encoded as combining marks. The intended result is that الْمُؤْمِنِينَ is discoverable through المؤمنين, not fuzzy spelling correction. Any broader orthographic normalization added after testing belongs in a lower-confidence search tier and must not silently collapse Word identities.

Word search results show the matched Word and its Root. Root results clearly identify the Root and link to Root View. Use “Root,” never “root word.” If normalization yields several possible Words, preserve them as distinct candidates rather than guessing their identity.

English gloss search, transliteration search, verse-reference search, complex ranking tiers, advanced filters, and sophisticated autocomplete are optional future enhancements.

## Occurrences

An Occurrence is a specific use of a Judhur Word at a corpus location, not the Word identity itself. For Qur'anic occurrences, retain the Surah, Ayah, position or token reference sufficient to distinguish repeated uses within the same Ayah, original Arabic from the source, and associated Judhur Word. Different source spellings can map to one Word under the matching rules. These location details support identity; the user-facing list only needs its total count, ordered Surah/Ayah references, and Quran.com links.

Every occurrence list shows its total number of occurrences at the top and references in Qur'anic text order: ascending Surah number, then Ayah number. This is text order, not an inferred chronology of revelation. Count occurrences, not merely distinct verses; repeated uses within a verse remain separate occurrences. Each reference links to the corresponding verse on Quran.com.

Word occurrence counts follow Judhur's Word identity and matching rules, not raw Arabic surface-string equality. Different surface spellings or attached external clitics count toward the same Word when they resolve to the same Word identity under those rules.

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

Prefer ingesting an open Qur'anic linguistic dataset into Judhur's own database rather than depending on an external API at runtime. Final dataset selection and schema inspection are implementation research tasks before M2/M3.

### Dataset evaluation

Evaluate **QuranMorph first** as the initial lexical/lemma source. It is a newer manually annotated corpus with expert lemmatization and POS tagging, described in the [QuranMorph paper](https://arxiv.org/abs/2506.18148) and listed as CC BY 4.0 by [SinaLab](https://sina.birzeit.edu/resources/). Before selecting it, inspect the actual downloadable schema and confirm support for Word → Lemma, Lemma → Root, Root → Derived Form, and Qur'anic occurrence mapping. Its suitability for all these relationships is not yet established.

Evaluate **Quranic Arabic Corpus (QAC)** for complementary Root, Form, and morphological annotations. Its [morphology documentation](https://corpus.quran.com/documentation/morphologicalfeatures.jsp) covers roots, person/gender/number, aspect, mood, voice, and Forms I–X. It also states that verbs are identified by roots and morphological features rather than explicit verb lemmas as supplied for nouns/adjectives. Judhur may therefore need to derive or obtain verb Lemma mappings elsewhere. Review the [download and usage terms](https://corpus.quran.com/download/) before redistribution or transformation; license suitability remains unresolved. QAC is not yet an authoritative database or runtime dependency.

The intended sequence is to evaluate QuranMorph, assess QAC's complementary annotations, and map the selected data into Judhur's Word/Lemma/Root/RootForm model while retaining source attribution. Do not build a complicated multi-corpus reconciliation system in advance of actual integration needs.

### Definitions

For M1–M4, use a small manually curated definition set for the Words, Lemmas, Roots, and Root Forms included during development. Attribute definitions drawn from external lexicons. Do not block MVP implementation on importing a complete dictionary. Long-term sources, such as Lane's Lexicon, can be evaluated separately, including the license of the specific digital edition or dataset.

### Lightweight provenance

Keep source name, source/version where useful, attribution/link, and whether information was imported or manually curated. Preserve original Arabic separately from normalized search text, and mark missing or disputed relationships rather than guessing them. Check that imported records refer to the intended Words and valid corpus locations.

Do not add publication pipelines, generalized assertion graphs, many-source conflict engines, corpus release management, complex alignment infrastructure, editorial workflows, or distributed infrastructure unless actual integration requires them.

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

1. Which final dataset or combination will supply the complete Word/Lemma/Root/RootForm relationships?
2. After inspecting QuranMorph and QAC schemas, what transformations are required to map them into Judhur's domain model?
3. Which trusted long-term sources should supply definitions for Words, Lemmas, Roots, and Root Forms?
4. What frontend, backend, and database stack should Judhur use?
5. What URL/routing scheme should the Word, Lemma, Root, and occurrence views use?
6. Which edge cases require refinement of Word identity rules after testing against real Qur'anic data?
7. Should Word, Lemma, and Root URLs use stable human-readable slugs or opaque/internal IDs?
8. Should definitions be allowed to vary by source/corpus in the future, or should Judhur maintain one canonical learning definition per entity?
