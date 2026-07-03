# GEDCOM Glossary

Plain-language definitions for the GEDCOM terms used throughout this repo
in `ged/*.ged`, in `src/dc_genealogy/`, and in the viewer.

## The format itself

**GEDCOM**
GEnealogical Data COMmunication.
A text file format for exchanging family tree data between different genealogy programs
(Ancestry, MyHeritage, Gramps, this repo's own viewer, etc.).
It's the reason a `.ged` file made here can be opened
in any of those tools without retyping anything.

**GED**
The file extension for a GEDCOM file (`data.ged`, `p01.ged`).
"GED" and "GEDCOM" are used interchangeably in casual conversation;
the file extension is always `.ged`.

**Line, level, tag, value**
Every line in a GEDCOM file has the same shape:

```text
1 BIRT
2 DATE 04 JUL 1889
```

- The **level** is the leading number.
  - `0` is a top-level record.
  - `1` is a detail of that record.
  - `2` is a detail of the `1` line above it, and so on.
- The **tag** is the keyword (`BIRT`, `DATE`, `NAME`...).
  - Tags are how GEDCOM-reading software knows what a piece of data _is_,
    regardless of what order it appears in.
- The **value** is whatever comes after the tag, if anything.
  - `BIRT` alone has no value; it's just a container for the `DATE`/`PLAC` lines nested under it.

Indentation in this glossary is for readability only.
The actual file has no indentation, just the leading level number.

**Pointer / xref**
An ID in `@...@` form, like `@I3@` or `@F2@`,
used to link records to each other without repeating their data.
`@I3@` always means "the individual defined in the `0 @I3@ INDI` line,"
wherever it's referenced elsewhere in the file.

## Top-level file structure

**HEAD**
The first record in every GEDCOM file.
Metadata about the file itself:
what produced it, what character encoding it uses, which GEDCOM version it follows.
Not a person or family; every `.ged` file needs exactly one.

```text
0 HEAD
1 SOUR FamilyGenealogyProject
1 GEDC
2 VERS 5.5.1
1 CHAR UTF-8
```

**TRLR**
Trailer.
The last line of a valid GEDCOM file. Just marks the end - no data of its own.

```text
0 TRLR
```

This is why `merge.py` strips out every fragment's own `HEAD`/`TRLR`
before concatenating them, then adds back exactly one of each -
a merged file with five `HEAD`s and five `TRLR`s in the middle
would confuse most GEDCOM readers, even though ours tolerates it.

**GEDC / VERS / FORM**
Nested under `HEAD`. `GEDC` just introduces the version block;
`VERS` is the GEDCOM spec version (`5.5.1` throughout this project);
`FORM` is almost always `LINEAGE-LINKED`,
meaning "this file links people via family records,"
as opposed to some other, rarer GEDCOM structuring convention.

**CHAR**
Character encoding, nested under `HEAD`.
Always `UTF-8` in this project - required for the accented characters throughout (Québec, Périgueux, Bérard...).

**SOUR / SUBM**
`SOUR` (source) names the software or project that generated the file.
`SUBM` (submitter) names the person compiling it.
Both are free text, not meaningfully validated by GEDCOM readers.

## Record types

**INDI**
"Individual." A record for one person.
Always a level-0 line with a pointer:

```text
0 @I1@ INDI
1 NAME Guillaume /Cluseau/
1 SEX M
```

The slashes around the surname aren't decoration - that's how GEDCOM marks which part of the name string is the family name,
so software can sort/search by surname reliably even with multi-word given names.

**FAM**
"Family." A record for one couple/union, linking two parents to their children.
Not a person - a relationship container:

```text
0 @F1@ FAM
1 HUSB @I1@
1 WIFE @I2@
1 CHIL @I3@
```

## Family-record tags

**HUSB / WIFE**
Pointers to the individual records of the two parents in a `FAM`.
GEDCOM's tag names assume a husband/wife pairing regardless of the actual relationship.
This project follows that convention as printed in the source reports
rather than introducing different tags,
since the source material predates any alternative convention.

**CHIL**
Child.
A pointer to an individual who is a child within this `FAM`.
A family record can have any number of `CHIL` lines, one per child.

**MARR**
Marriage.
An event nested under `FAM`, structured the same way `BIRT`/`DEAT`
are nested under `INDI`, a container for a `DATE` and/or `PLAC` line.

## Individual-record tags

**NAME**
The person's name, surname wrapped in slashes: `1 NAME Jean /Cluseau/`.

**SEX**
`M`, `F`, or omitted if unknown/unrecorded.

**BIRT / CHR / DEAT / BURI**
Life events, each a container (like `MARR`) for nested `DATE`/`PLAC` lines:

- `BIRT` - birth
- `CHR` - christening/baptism (distinct from birth since Québec parish records often show both, sometimes days apart)
- `DEAT` - death
- `BURI` - burial

```text
1 DEAT
2 DATE 07 NOV 1733
2 PLAC Québec, Québec, Québec, Canada
```

**FAMC**
Family as child.
A pointer from an individual to the `FAM` record they were _born into_ - how GEDCOM encodes "who are this person's parents" without repeating the parents' data on every child's record.

**FAMS**
Family as spouse.
A pointer from an individual to a `FAM` record they're a _parent in_.
An individual with two marriages has two `FAMS` lines, one per union - this is how `@P01I9@` (Pierre Cluseau, two wives) is encoded in `p01.ged`.

## Shared/common tags

**DATE**
Always nested one level under an event tag (`BIRT`, `DEAT`, `MARR`...), never standalone. Free-text-ish but conventionally `DD MMM YYYY`,
with `Abt`/`Bef`/`Aft` prefixes for approximate dates -
both used throughout this project's source material (`Abt 1645`, `Abt 1820`).

**PLAC**
Place. Nested under an event tag alongside `DATE`.
Convention in this project's source reports is city, county, province/state, country -
e.g. `Québec, Québec, Québec, Canada`
(the repetition is the source document's own city/county/province naming, not a transcription error).

**NOTE**
Free-text commentary attached to a record. This project uses `NOTE` heavily for transcription-confidence flagging (`OCR AMBIGUOUS`, source citations,
date-tension flags) - that's a project convention, not something GEDCOM requires.

**CONT**
"Continued." When a value (usually inside a `NOTE`) runs longer than comfortably fits one line, `CONT` at one level deeper continues it on a new line without starting a new tag:

```text
1 NOTE Source: images/p01.jpg, generation 3. Two spouses listed, consistent
2 CONT with first wife's death (1731) preceding the second marriage.
```

## Not standard 5.5.1, used anyway

**RESN**
Restriction.
Living-persons privacy - `1 RESN privacy`
is a de facto convention many genealogy programs respect for hiding a record in their own UI, even though it isn't formally part of the
GEDCOM 5.5.1 spec this project otherwise targets.
Not currently used in this project's files;
`redact.py` takes a different approach (replacing the name and stripping dates outright)
since a plain-text `.ged` in a public repo can be read directly
regardless of what `RESN` says.
See the redaction discussion for why display-layer restriction isn't sufficient on its own.
