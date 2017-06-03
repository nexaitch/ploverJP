# An EN-steno compatible system for typing Japanese

## Contents

1. Description
2. Algorithmic Generation of Output from Strokes
3. Details

## Description

**Note:** This document uses Nihonshiki romanisation, but
modified to make parsing / generation of text easier, i.e.

- "tyou" instead of "chou" / "chō" / "chô", and
- "zinn" instead of "jin".

The traditional steno layout is divided into 4 sections:

- The left-hand side (**lhs**) `STKPWHR`
- The vowels (**vowel**) `AO*EU`
- The godan consonant keys (**godan**) `-RBG`
- and the inflection keys (**rhs**) `-FPLTSDZ`

The **lhs** section decides
the initial consonant of the first syllable, for example:

- `S` "s"
- `TPH` "n"
- `TPKW` "g" 
- `TR` "tt"

The **vowel** section decides
the vowel of the first syllable,
with a possible additional vowel / wa / medial y:

- `SEU` "s**i**"
- `KAO*EU` "k**ae**"
- `SAU` "s**awa**"
- `T*E` "t**you**"

The **godan** section decides
the following consonant,
with a default u:

- `SEUBG` "si**k**u"
- `KAO*EUR` "kae**r**u"
- `MARBG` "ma**t**u"
- `KERB` "ke**s**u"

The **rhs** section decides any inflectional endings,
and if the **godan** section is empty
has a different set of endings assigned to it:

(with **godan**)

- `SEUPBG` "sik**anai**"
- `KAO*EURPTS` "kaer**imasendesita**"
- `MAFRPBG` "mat**a**"

(without **godan**)

- `HOEF` "hou**suru**"
- `KAO*EUTD` "kae**tte**"
- `MEUPT` "mi**nakatta**"
- `KAFT` "ka**nn**"
- `*EST` "you**desita**"

Briefs have either a **vowel** of `*` or nothing,
or an impossible **lhs** chord
or **lhs+vowel** chord
(`KWR` and `WE` for instance)

Some briefs are (or end with) regular verbs
and take on all normal inflections with the **rhs** chords:

- `KWR-BG` "itadaku"
  - `KWR-BGS` "itadakimasu"
  - `KWR-BGSZ` "itadakimasyou" etc.
- `KWRO*RB` "yorosikuonegaiitasu" etc.
- `WER` "wakedearu" etc.

Some briefs are slightly irregular verbs,
all of which follow a similar pattern:

- `TKPW-RB` "gozaru", but
`TKPW-RBS` "gozaimasu", not "goza**r**imasu"
- `KWR-R` "irassyaru", but `KWR-RS` "irassyaimasu"
- etc.

We can store these as "gozaq" and "irassyaq" respectively
and replace "q" with an "r" or with nothing before output depending on what follows it.

Some briefs do not inflect like verbs and take **rhs** endings without **godan**:

- `TPH*P` "nakerebanaranai"
  - `TPH*PS` "nakerebanaranaidesu"

Some briefs do not inflect or take any **rhs** endings at all:

- `SKR-D` "desukeredo"
- `P-P` "."

## Algorithmic Generation of Output from Strokes

### Memory

Store 8 dictionaries:

1. Valid **lhs** chords and their corresponding initials;
2. Valid **vowel** chords and their corresponding vowels;
3. Valid **godan** chords and their corresponding consonants;
4. Valid **rhs** chords and their corresponding inflections (with **godan**);
5. Ditto, but without **godan**;
6. List of verb briefs;
7. List of nonverb briefs;
8. List of uninflectable briefs.

### Algorithm

1. Split the chord up into its respective sections.
2. Check if **lhs+vowel** chord is valid.
3. If it is:
   1. Check if the **godan** chord is empty;
	if so, check dictionary 1, 2, and 5 for the corresponding endings and sum up all the sections.
   2. Otherwise, check dictionary 1, 2, 3, and 4 instead and sum up all the sections.
4. Otherwise it is a brief. Then:
   1. Check if the chord matches an uninflectable brief.
	If so, take the brief text.
   2. Check if the **lhs+vowel+godan** chord matches a nonverb brief.
	If so, take the brief text plus the corresponding **rhs** ending in dictionary 5.
   3. Check if it matches a verb brief.
	If so, do the same as the above step except with dictionary 4. 
5. If you still do not have any match, it is an invalid stroke and you should just output the stroke text.
6. Otherwise, apply any orthographic rules (conversion to a nicer romanisation; making things nicer for your ime etc) and output your text.

## Details

Actual tables for each of the clusters to be done soon.