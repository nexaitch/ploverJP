# An EN-steno compatible system for typing Japanese

## Contents

1. Description
2. Algorithmic Generation of Output from Strokes

## Description

__Note:__ This document uses Nihonshiki romanisation, but
modified to make parsing / generation of text easier, i.e.

- "tyou" instead of "chou" / "chō" / "chô", and
- "zinn" instead of "jin"

The traditional steno layout is divided into 4 sections:

- The left-hand side (__lhs__) `STKPWHR`
- The vowels (__vowel__) `AO\*EU`
- The godan consonant keys (__godan__) `-RBG`
- and the inflection keys (__rhs__) `-FPLTSDZ`

The __lhs__ section decides
the initial consonant of the first syllable, for example:

- `S` "s"
- `TPH` "n"
- `TPKW` "g" 
- `TR` "tt"

The __vowel__ section decides
the vowel of the first syllable,
with a possible additional vowel / wa / medial y:

- `SEU` "s__i__"
- `KAO*EU` "k__ae__"
- `SAU` "s__awa__"
- `TA*U` "t__you__"

The __godan__ section decides
the following consonant,
with a default u:

- `SEUBG` "si__k__u"
- `KAO*EUR` "kae__r__u"
- `MARBG` "ma__t__u"
- `KERB` "ke__s__u"

The __rhs__ section decides any inflectional endings,
and if the __godan__ section is empty
has a different set of endings assigned to it:

(with __godan__)

- `SEUPBG` "sik__anai__"
- `KAO*EURPTS` "kaer__imasendesita__"
- `MAFRPBG` "mat__a__"

(without __godan__)

- `HOEF` "hou__suru__"
- `KAO*EUTD` "kae__tte__"
- `MEUPT` "mi__nakatta__"
- `KAFT` "ka__nn__"
- `A*UST` "you__desita__"

Briefs have either a __vowel__ of `*` or nothing,
or an impossible __lhs__ chord
or __lhs+vowel__ chord
(`KWR` and `WE` for instance)

Some briefs are (or end with) regular verbs
and take on all normal inflections with the __rhs__ chords:

- `KWR-BG` "itadaku"
 - `KWR-BGS` "itadakimasu"
 - `KWR-BGSZ` "itadakimasyou" etc.
- `KWRO*RB` "yorosikuonegaiitasu" etc.
- `WER` "wakedearu" etc.

Some briefs are slightly irregular verbs,
all of which follow a similar pattern:

- `TKPW-RB` "gozaru", but
`TKPW-RBS` "gozaimasu", not "goza__r__imasu"
- `KWR-R` "irassyaru", but `KWR-RS` "irassyaimasu"
- etc.

We can store these as "gozaq" and "irassyaq" respectively
and replace "q" with an "r" or with nothing before output depending on what follows it.

Some briefs do not inflect like verbs and take __rhs__ endings without __godan__:

- `TPH*P` "nakerebanaranai"
 - `TPH*PS` "nakerebanaranaidesu"

Some briefs do not inflect or take any __rhs__ endings at all:

- `SKR-D` "desukeredo"
- `P-P` "."

## Algorithmic Generation of Output from Strokes

### Memory

Store 8 dictionaries:

1. Valid __lhs__ chords and their corresponding initials;
2. Valid __vowel__ chords and their corresponding vowels;
3. Valid __godan__ chords and their corresponding consonants;
4. Valid __rhs__ chords and their corresponding inflections (with __godan__);
5. Ditto, but without __godan__;
6. List of verb briefs;
7. List of nonverb briefs;
8. List of uninflectable briefs.

### Algorithm

1. Split the chord up into its respective sections.
2. Check if __lhs+vowel__ chord is valid.
3. If it is:
 1. Check if the __godan__ chord is empty;
	if so, check dictionary 1, 2, and 5 for the corresponding endings and sum up all the sections.
 2. Otherwise, check dictionary 1, 2, 3, and 4 instead and sum up all the sections.
4. Otherwise it is a brief. Then:
 1. Check if the chord matches an uninflectable brief.
	If so, take the brief text.
 2. Check if the __lhs+vowel+godan__ chord matches a nonverb brief.
	If so, take the brief text plus the corresponding __rhs__ ending in dictionary 5.
 3. Check if it matches a verb brief.
	If so, do the same as the above step except with dictionary 4. 
5. If you still do not have any match, it is an invalid stroke and you should just output the stroke text.