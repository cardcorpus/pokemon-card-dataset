# CardCorpus API guide

CardCorpus is an open dataset of Pokémon Trading Card Game cards in English,
Japanese and Simplified Chinese, published as plain JSON files at
`https://pokemon.cardcorpus.com/v1/`. There is no key and no sign-up, every file
allows cross-origin reads, and no server code runs on a read.

This guide explains the ideas the files share. Every field of every file is
in the [field reference](https://pokemon.cardcorpus.com/v1/docs/reference.md), which
is generated from the JSON Schemas under `/v1/schema/`. The schema
descriptions point back here by section.

## Quick start

1. Open [`/v1/index.json`](https://pokemon.cardcorpus.com/v1/index.json). It lists
   every file with its schema (`files`), how to follow a pointer
   (`pointers`), and the url recipes for pictures, prices and marketplace
   links.
2. Open `/v1/sets/{catalog}.json` for a catalog's sets, then `/v1/sets/{set}.json` for one
   set with all its cards and each card's variants.
3. To find a card by name or number across every set, read
   `/v1/cards/{catalog}.json`, one file per catalog: `cards/english.json`,
   `cards/japanese.json`, `cards/zh-hans.json`.
4. To show a coded value (a rarity, a finish, a role) as words, look it up
   in `/v1/vocabulary.json`.

A set id contains a slash (`english/base-set`) and goes into a url as
it is: `https://pokemon.cardcorpus.com/v1/sets/english/base-set.json`. Do not
url-encode the id as a whole, since `english%2Fbase-set` is not a
path the server has. Encode each part between slashes if you encode at all.

## Conventions

- **Absent means unknown or not applicable.** There are no nulls, and an
  absent list is an empty one. The one exception is a reprint's `differs:
  []`, which says nothing we compare differs.
- **Every count is always present**, 0 and 1 included, and is named for its
  unit: `cardCount`, `variantCount`, `printCount`, `setCount`.
- **Ids are opaque and never change once published.** See
  [Identifiers](#identifiers).
- **Card and variant.** A *card* is one entry of a set's checklist. A
  *variant* is one physical version of that card you can own: a print run, a
  finish, a stamp, an error. In the files a variant is an entry of the card's
  `variants`, and a pointer to one has `$kind: "variant"`. The price files
  call a variant a copy: their `copies` are keyed by variant id.
- **Catalog is not language.** A set's `catalog` is the numbering structure
  it is filed in: `english`, `japanese`, `zh-hans`, `zh-hant`, `thai`, `indonesian`, `french` or `korean`. Its `language`
  is the language its cards are printed in. Almost every `english`
  set is English, but the French McDonald's sets are `english` too,
  so read `language` when you mean a language. The vocabulary's label for
  the `english` catalog is "English" because that is what a list of
  catalogs shows it as; it is a display name, not a claim about every card.
- **Lists have a stated order**, given in each file's schema description.
  Lists of cards across sets run oldest first.

## Identifiers

Public ids are slugs. An id never changes once published, even when the
name, number or finish it was first derived from is corrected, so treat an
id as opaque and don't read meaning out of it (`unlimited-holo` is a name,
not a statement about the finish).

| Thing | Id | Unique within |
| --- | --- | --- |
| Set | `<catalog>/<slug>`, `english/base-set` | everything |
| Card | usually its number, `4`; a name slug where a number repeats or is missing | its set |
| Variant | a slug of its physical axes, `first-edition-holo`, or `plain` | its card |
| Species | its National Pokédex number, **a number**: `6` | everything |
| Form | `"6"` for the default form, `"6:mega-x"` otherwise, a string | everything |
| Era | `<catalog>/<slug>`, `english/original-series` | everything |
| Artist, character, distribution, line, family | a slug | everything |

A card is addressed by its set's id and its own, a variant by its set's,
its card's and its own. Card ids are not unique across sets.

A species id is the only id that is a number. Every other id is a string,
so compare species ids as numbers (or convert both sides to strings) rather
than with a strict comparison against a string.

Only a merge, a split, a move or a removal retires an address.
`/v1/moved.json` lists every retired address and what became of it:
`{"from": <pointer>, "merged": <pointer>}`, `"moved"`, `"split": [<pointer>,
…]` or `"removed": true`. You only need it when a pointer you stored stops
resolving.

## Pointers

Wherever a file names another entity it uses a pointer: an object whose
`$kind` says what it points at and whose other keys are the ids that find
it.

```json
{ "$kind": "card", "set": "english/base-set", "id": "4" }
```

Whatever has a `$kind` says its own id as `id`, the same `id` its target has
in its own file. The ids beside it say what it is in: a card's `set`, a
variant's `set` and `card`. A card reads the same wherever you meet it, as a
pointer in a list or as the card itself in its set's file.

`index.json`'s `pointers` says how to resolve each kind: fill the `{…}` in
`file` from the pointer's ids and open it, then walk `path` in order, at
each step picking from `list` the item whose `match` field equals the
pointer's `by` field. One short function resolves every kind.

| `$kind` | Ids | File | Then pick |
| --- | --- | --- | --- |
| `set` | `id` | `sets/{id}.json` | the file |
| `card` | `set`, `id` | `sets/{set}.json` | `cards` by `id` |
| `variant` | `set`, `card`, `id` | `sets/{set}.json` | `cards` by `card`, then its `variants` by `id` |
| `set-version` | `set`, `id` | `sets/{set}.json` | `versions` |
| `painting` | `id` | `paintings/{id}.json` | the file |
| `era` | `id` | `eras.json` | `eras` |
| `line` | `id` | `lines.json` | `lines` |
| `species` | `id` | `pokemon.json` | `species` by `dex` |
| `form` | `species`, `id` | `pokemon.json` | `species` by `dex`, then its `forms` by `id` |
| `pokemon-family` | `id` | `pokemon-families/{id}.json` | the file |
| `artist` | `id` | `artists.json` | `artists` |
| `character` | `id` | `characters.json` | `characters` |
| `distribution` | `id` | `distributions/{id}.json` | the file |
| `distribution-series` | `id` | `distribution-series/{id}.json` | the file |
| `collection` | `id` | `collections.json` | `collections` |

A pointer often carries more than its ids, so you can show the target
without opening its file. Every extra key is one of two things:

- **a variant of the target's own field**, equal to it: a `name`, a `picture`,
  a `language`. The build fails if a variant differs from the target;
- **a fact about the link itself**, which the target doesn't have: a
  reprint's `differs`, a counterpart's `printCount`, a
  person's `roles` on a card, a set link's `relation`.

If the target has a field of that name, the key is a variant. If it doesn't,
the key is a fact of the link. A species or form pointer carries the
`name`, so most pages never need `pokemon.json` (about 200 KB compressed)
just to label a card's Pokémon.

## Sets

`sets/{catalog}.json` lists every set of a catalog by era, release date, then `position`
(which orders sets of one day), then name. Each has its `type`
(`expansion`, `special_expansion`, `promo`, `deck`, `box`, `prize`,
`tie_in` or `energy`), its `era` (an era pointer with its `name`), its
`language`, when it came out (`released`), and its `code`, `logo` and
`symbol`. A set is in one language: its `name` is as it prints it, and
`englishName` is its name in English ([Names](#names)).

The set list comes a catalog at a time: `sets/english.json`,
`sets/japanese.json`, `sets/zh-hans.json`. There is no file of every
catalog's sets; `index.json` lists the catalogs in `catalogs`, each with its
`id`, `name`, main `language`, the `languages` its cards are printed in and
its `setCount`, `cardCount` and `variantCount`, so for all of
them read each one's list. A set's id is two segments, `{catalog}/{slug}`,
so `sets/english.json` is a catalog's list and
`sets/english/base-set.json` is a set.

A set file adds its description, print runs, versions, sections, links to
other sets (`links`, set pointers with a `relation` of `equivalent`,
`subset_of`, `merged_into` or `companion`) and every card (`cards`).

## A card

A card says where it is the same way wherever you meet it: `$kind`
(`"card"`), `set` and `id`, which is also the pointer to it. A card in a
set file has those, `name` and `englishName` ([Names](#names)), `number` (`printed`, the number
as the card prints it, and `short`, that number without its set total:
`4` for `4/102`, and the whole number where it has no total, `No. 006`),
`rarity`, `cardType`, `language`, `credit` and `artists`, `released`,
`species` and `characters`, `picture`, its `variants` and its
`links` to other cards. A card in a list across sets is the same card with
fewer fields ([Card lists](#card-lists)), so code written for a listed
card also takes a set file's card.

### Names

Every card and set has a `name`, as it prints it in its language, and an
`englishName`. A Japanese Charizard is `"name": "リザードン", "englishName":
"Charizard"`; on an English card the two are the same. Search and sort by
`englishName` to find a card across languages, and show `name` to show the
card as printed. Where no source gives the printed name yet (most Simplified
Chinese cards before 2025, and every set so far), `name` is the English
name too. Every card pointer that copies a card's `name` copies its
`englishName` beside it.

### Subtitles

Some cards print a smaller name beside their own to say which of several
cards of one name they are: every Professor's Research from Sword & Shield on
prints its professor, and every Boss's Orders its boss. That is the card's
`englishSubtitle`: `"englishName": "Professor's Research",
"englishSubtitle": "Professor Oak"`. It isn't part of the name, because the
game counts every Professor's Research as one card whatever professor it
shows. A subtitle is always one of the card's `characters`. `subtitle` is
the same name as the card prints it, in its language, beside its printed
`name`: `"name": "Recherches Professorales", "subtitle": "Professeur Chen"`
on a French card. To show it the way the card does, join the two:
"Professor's Research (Professor Oak)". Card pointers that copy a card's
`name` copy its `subtitle` and `englishSubtitle` too.

### Which variant field do I use?

A card has several fields that name one of its variants. They answer
different questions, and each differs from the others in stated cases:

| Field | Question it answers | When it differs |
| --- | --- | --- |
| `variants` | What are all the variants of this card? | The full list, in role order. |
| `defaultVariant` | Which one variant stands for the card (a tile, a price)? | Always present. The standard variant, or the first test print on a test-only card. |
| `picture.front.of` | Whose photo is the card's picture? | Usually `defaultVariant`. It differs on about 390 cards whose default variant has no photo of its own: it then names the variant standing in, or the original card's variant on a reprint with no photo at all. Read it instead of assuming the picture shows the default variant. |
| `printRuns` | Which variants are the card's print runs, in printing order? | Only on a card printed in more than one run. |
| `collectingVariations` | Which variants are the same thing apart from their print run? | Groups of `variants` that differ only by edition: "Holo" holds 1st Edition, Shadowless and Unlimited holos. For apps that let someone record "the holo one" without saying which run. `masterSet` marks the groups in the master set. |
| `defaultCollectingVariation` | Which group is `defaultVariant` in? | Always present. |
| `defaultCollectingCopy` | Should a quick "I own this" save the exact variant as well as the group? | Present only where the default group has more than one print run, and then always equal to `defaultVariant`. Absent otherwise. It is a flag in the shape of an id. |

On a variant:

| Field | Meaning | When it differs |
| --- | --- | --- |
| `edition` | The edition the variant is, a physical fact about it: `first_edition`, `shadowless`, `unlimited`. | Absent where the variant's edition isn't recorded. |
| `printRun` | Which of the set's `printRuns` the variant came out in. | Equal to `edition` wherever both are present. It is `unlimited` on about 1,400 variants with no `edition` whose set has runs, and absent on about 1,000 variants whose `edition` has no known place among the set's runs. Use `printRun` to look a run up in the set's `printRuns`, and `edition` to say what the variant is. |

If you only want to show a card, read `defaultVariant` and `picture`. If you
only want to list its variants, read `variants`. The collecting fields are
there for collection apps and can be ignored otherwise.

## Version roles

Each variant has a `role`, what it is on its card, and `variants` lists them
in this order: `standard` (the variant with nothing of its own; one per card,
except on a test-only card), `print_run` (another edition in the standard
variant's finish), `parallel` (another finish from the same packs, such as a
reverse holo), `serial_code` (another printed code of the same version),
`special` (a stamp, material or distribution the standard variant lacks),
`error`, `test` (never sold) and `oversized`. The master set is the
standard variant plus its parallels. `vocabulary.json` labels each role.

A variant exposes only the physical axes that apply to it: `finish`,
`material`, `edition`, `serialCode`, `stamp` and `errorState`. A variant with
no `finish` has an unknown one, never silently non-holo.

## Telling variants apart

Each variant says how it differs from the card's other variants:

- `name`: what to call it beside the others ("1st Edition", "Reverse
  Holo", "Standard").
- `differsFromStandard`: its axes the standard variant doesn't share, as
  `{"finish": "reverse_holo"}`.
- `tell`: how to spot it on the card, in a sentence (`{"text": …}`).
- `printRun`: see [Which variant field do I use?](#which-variant-field-do-i-use).

A card whose set prints its painting on other cards too says what tells it
from them in `differsInSet`, on the card: the axes of its default variant
that not all of those cards share, such as `{"finish": "pokeball_pattern"}`.
A card with parallels always has it, `{}` where nothing tells it from them.

## Print runs

A set some of whose cards were printed more than once lists its
`printRuns`, earliest first, each by `edition`, with `released` and a
`tell` where they have been researched, and `standard: true` on the
run whose variants are the set's standard ones where that isn't Unlimited.
A card printed in more than one run lists its own `printRuns` as variant
pointers in the same order.

## Edition mark

A variant's `editionMark` is the edition mark it prints: `first_edition` for
the 1st Edition symbol. It usually follows from `edition`, and is stated
apart because a few variants print a mark their edition doesn't imply.

## e-Reader strip

Cards from the e-Reader years print a strip along the bottom. A variant's
`eReaderStrip` says what is printed there, as its own picture shows it:
`dot_code`, `holo_bars` or `blank`; absent where nobody has looked. A
card's `eReaderLayout` says where the strip is: `e_card` (along the bottom
and down the left edge) or `ex` (one strip along the bottom).

## Rarity

A card's `rarity` is the rarity mark printed on it, in its era's wording
("Holo Rare", "Double Rare", "Illustration Rare"). A card that prints no
mark has no `rarity`. The finish is not part of the rarity: a foil Common
is a Common. To sort or filter by how showy a card is, look the word up in
`vocabulary.json`'s `rarity` group for its `tier`, then read that tier's
`rank` in `rarityTier` (from 0; "holo and up" is a rank of 3 or more).

## Release dates

`released.date` is when something first came out, as precisely as is
known: `2001`, `2001-09` or `2001-09-22`. A card in an ordinary set has its
set's date. A promo series' cards came out one by one, so each has a date
of its own, or no date where it isn't known yet.

Every card also has `released.sort`, a string to sort cards oldest first
by. Compare it as plain strings and read nothing else from it. Every list
of cards across sets is already in this order.

## Pictures

A picture is an id, 32 hex digits. `index.json`'s `pictures` turns it into
a url: `sized` with `{id}` and a `{width}` from `widths` (240, 480 or
960 px, AVIF). Show the smallest variant at least as wide as you draw it.

Pictures belong to variants. A card's `picture.front.image` is the picture
to show for the card, and `picture.front.of` names the variant it is a
picture of. A variant whose `picture.front` has an `of` shows another variant's
photo standing in for one it lacks; say so rather than presenting it as
that variant's. `picture.front.watermark` names whose watermark a picture
carries, `picture.back.image` is a photo of an unusual back, and
`oversized: true` marks a jumbo.

## Which link is which

A card's links say one of two things about another card.

**It is the same card.** The same card printed again, so the name, the
text and the picture are all the same card's:

- `counterparts`: the card in another language.
- `reprintOf` and `reprints`: the card printed again later in the same
  language, with what `differs` (a stamp, a finish, a number).
- `parallels`: the card again in the same set, in another finish under
  another number.

**It only shares the picture.** A different card, with its own name, text
or number, that happens to print the same painting:

- `painting`: a pointer to the painting itself, which lists every print of
  it, in every language, grouped into the cards they are.

So a reprint always shares its original's painting, but two cards sharing a
painting are not always reprints of each other: a promo with new text, or a
later card reusing an old picture, is another card of the painting. Those
are not listed on the card: follow `painting` and read the painting's other
`cards` ([Paintings](#paintings)).

### Links

A card's `painting` is in its set file. Its links (`counterparts`,
`reprintOf`, `reprints`, `parallels`) are in that painting's file, `/v1/paintings/{id}.json`, on the card's print
there: every print of a painting lists its own. What a list of a set's
cards shows of them is a field of the card: `differsInSet` on every card
with parallels, and `relation` on a borrowed picture's `of`. So a page of a set's cards reads only the set
file. The links are most of a card's weight, so they are fetched only by
those that want them, and with them come the cards they name.

A painting file's `prints` are keyed by set id and then card id, so a card
finds its own print, with its links, as `prints[card.set][card.id]`:

```js
const painting = await get(`paintings/${card.painting.id}.json`);
const links = painting.prints[card.set][card.id].links ?? {};
const french = links.counterparts?.french;           // the same card in French
const tile = french && painting.prints[french.set][french.id];
```

Each link is a card pointer and what the link itself says, nothing else.
Every card it names is in the same file's `prints`, found the same way, with
its name, number, language and picture, ready to draw. A card redrawn for
another language prints another painting; its print is in `prints` too,
with its own `painting`, and in none of the file's `cards`. To show a
linked card's set name, read its catalog's set list,
`/v1/sets/{catalog}.json`.

A card that no other card shares its painting with has no `painting`, and
no links, except where it is the same card as one redrawn in another
language: then it has a painting file of its own, with just itself.

## Counterparts

A card's `counterparts` (on its print in its painting's file) name the card it
is the same card as in each other catalog, keyed by the catalog's id
(`index.json`'s `catalogs`), one a catalog, in the catalogs' order:
`counterparts.japanese`, `counterparts.french`. Each entry is a card pointer
with `printCount`, how many prints it has in its language, and, where the
link is borrowed from another print of this card, `via` (`original`, from
the card it reprints, or `reprint`, from its first print in its set). Its
print in the same file says its `language`: its set's, or a foreign print's
own. Where a card has several prints in a
language, the entry is the one we researched as the match, else the
earliest; the others are that card's `reprints` and `parallels`.

## Reprints

A later print of a card in another set of its language lists
`reprintOf` on its print in its painting's file: a card pointer to the first print, with `differs`, what
sets the reprint apart (`stamp`, `finish`, `number`, `border`, `text` and
so on, or an empty list where the two are identical). The original lists the same links,
pointing back, under `reprints`. A reprint shares its original's painting,
so both are prints in that file.

## Parallels

A card printed more than once in its own set, each print under its own
number, lists the others as `parallels` on its print in its painting's file, in number order.
On the card in its set file, `differsInSet` says what tells it from them.

## Paintings

A card's `painting` names the painting it prints, where another card
prints it too. A painting has an id of its own, the English name of its
first print and a short code, such as `charizard-2dke`, and its file is
`paintings/{id}.json`. The id never changes, so a stored painting pointer
keeps working.

The file's `firstPrint` is a card pointer to the card that first printed
it. Base Set Charizard prints a painting first seen in the Japanese
Expansion Pack, so following the card's `painting` pointer opens
`paintings/charizard-2dke.json`, whose `firstPrint` is Expansion Pack's
No. 006. If an earlier print is found, `firstPrint` changes and the id
stays. `paintings.json` lists every painting printed on more than one
card.

A painting's `cards` list its prints card by card, one list of card
pointers for each different card that uses it, each in order. A card is one
card with its reprints, its parallels and the same card in other languages,
which all play the same. Most paintings have one. A second is a card that
reuses the picture with other game text: the Charizard painting has Base
Set's Charizard and its reprints in one list (38 prints) and Evolutions'
Charizard in another (7 prints). To show "other cards with this picture" on
a card page, find the list holding the card and show the others, each by one
of its prints (`prints[set][id]`).

## Pieces

A LEGEND or V-UNION is printed in pieces, each its own card. Each piece
lists `pieces`: the `format` (`legend` or `v_union`), the pieces as card pointers in reading
order, and this card's `position` among them, from 1.

## Versions

A set sold in more than one version, where the version decides some of the
cards, lists them in `versions`. A card found in only some names them in
`onlyInSetVersions` as set-version pointers; a card without it is in every
version. A numberless card whose name another version's card shares is told
apart by those versions: show it as "Pikachu (blue)".

A few sets hold two different cards with the same name that the numbering
doesn't tell apart. Each says which of the pair it is in `printVersion`, 1
or 2:

- The Celadon City Gym deck has two Erika's Oddish, different cards both
  numbered No. 043. One has `printVersion: 1`, the other `printVersion: 2`.
- Pikachu World Collection has two numberless Pikachu. The blue binder's
  has `printVersion: 1` and the green binder's `printVersion: 2`; here
  `onlyInSetVersions` tells them apart as well.

`printVersion` counts within the pair. It is not a pointer into the set's
`versions`; that is `onlyInSetVersions`.

## Set tells

A set whose cards' paintings are printed elsewhere too says how to tell
its print from the others in `tell`: its set symbol and numbering. A card that differs carries its own `tell`.

## Printed codes

`printedCode` on a set is the set code and language mark its cards print
where they name their set, as printed (in the set code's letter case):
`"printedCode": "PBL FR"`. The same set prints
"PBL EN" in English, "PBL LA" in Latin American Spanish; Japanese and
Korean cards print the code alone ("SV9a"), Simplified Chinese joins its
"C" to it ("CSV3C"). It is `null` when the cards print no code (Base
Set). A set with no `printedCode` hasn't been checked yet. Each language's
mark is its `codeMark` in `vocabulary.json`, and a set's `printedCode` is
its `code` with that mark, with a few exceptions (the Sun & Moon era Asian
sets print "AS1a" with no mark).

## Lines

Some products came out as several sets, each with its own checklist: the
four decks of a year's World Championships, the two halves of a Trainer
Kit. Each such set names its `line` (a line pointer with the line's `name`
and the set's `part`), and `lines.json` lists every line with its sets.

## Foreign prints

A set holds the cards its product held, in whatever language each is
printed. Every card gives the language its text is in as `language`: its
set's, or another for a foreign print.

## Distributions

A distribution is how variants reached people outside booster packs: a
product, a program, an event, a shop, a campaign, a magazine. A variant's
`distributions` are distribution pointers with the `name` and `category`,
plus `as` (`prize` or `staff`) and `region` where they apply.
`distributions.json` lists every one; `distributions/{id}.json` has its
description, the sets it handed out whole and every other variant it handed
out; a distribution that recurs belongs to a series
(`distribution-series/{id}.json`).

## Descriptions

A set, card, variant or distribution with a story has a `description`:
`text`, plain text with paragraphs split by a blank line.

## Card lists

Files that look across sets list cards so that you open one file, not
every set file: `artists/{catalog}/{artist}.json`,
`characters/{catalog}/{character}.json`,
`pokemon-cards/{catalog}/{species}.json`, `paintings/{id}.json` and
`distributions/{distribution}.json`. Each card in a list is its card
pointer with copies of the fields a tile draws from: `name`, `number`,
`rarity`, `cardType`, `language`, `released`, `credit`, `artists`,
`species`, `picture`, `defaultVariant` and its painting. A distribution's
variants add the variant under `variant`. Every list runs oldest first.

An artist's, a character's and a Pokémon's cards come a catalog at a time:
one file for each catalog that has cards of it. The entry in `artists.json`,
`characters.json` or `pokemon.json` lists them as `catalogs`, so open the one
you show, or each of them and merge by `released.sort` for every language.

The schemas name the two shapes. `CardSummary` is the card of a list, and
`Card`, the card of a set file, is a `CardSummary` with the rest of its
fields; a type generator writes `Card = CardSummary & { … }`.

`cards/{catalog}.json` lists every card of a catalog in one slim list, for
search: one a catalog (`english`, `japanese`, `zh-hans` and the rest), each a few megabytes.

## Species and forms

`pokemon.json` lists every species in National Pokédex order with its
names by language, types, forms in brief and how many cards show it.
`pokemon-families/{family}.json` gives an evolution family in full, with
the `transitions` between forms. A card's `species` lists the species it
shows, each once with its `name`. An entry has `forms` only when the card
shows something other than the ordinary form alone, as on a Gigantamax
Flapple: `{ "species": 841, "name": "Flapple", "forms": [{ "id":
"841:gmax", "name": "Gigantamax Flapple" }] }`. So `forms` is usually
absent (an ordinary Pokémon), sometimes one entry (a Mega, a Gigantamax,
a regional form), and now and then two or more, when the card shows
several forms of the species at once. The ordinary form is listed with
the others where the card shows it too: Tatsugiri's card of all three has
`978`, `978:droopy` and `978:stretchy`. A card's `characters` lists the characters on it, each
with the `name` the card gives them and a `role` of `subject` or `owner`
(Misty is the `owner` on Misty's Psyduck).

## Vocabulary

`vocabulary.json` has a `label` for every coded value the other files use,
and where there is one a `tell` (how to spot it on a card). Its groups are `role`, `differs`, `finish`, `material`,
`edition`, `stamp`, `errorState`, `distribution`, `rarity`, `rarityTier`,
`catalog`, `language`, `setType`, `setLink`, `cardType` and
`piece`. The vocabulary only gains values: a value is never renamed in
place.

## Marketplace ids

`marketplaces/{set}.json` names the TCGplayer and Cardmarket products that
sell a set's cards: per card, every product id, and per variant the one
product that prices it (`tcgplayer: {product, subtype}`, `cardmarket:
{product, reverseHolo?}`). `index.json`'s `marketplaces` turns a product
id into a link. Only a set with at least one paired card has a file;
`marketplaces.json` lists them.

## Prices

Prices change daily, so they are served from their own host,
`https://prices.cardcorpus.com`, by the same set, card and variant ids.
`index.json`'s `prices` gives the urls:

- `latest`, `/{set}.json`: a set's prices on each marketplace's newest
  day, as `cards[card][variant].tcgplayer` (US dollars) and `.cardmarket`
  (euros). `price` is the number to show: TCGplayer's market price, or
  Cardmarket's trend.
- `sets`, `/sets.json?sets={sets}`: the `price` of every variant in up to 64
  sets at once, their ids joined by commas.
- `history`, `/history/{set}/{card}.json`: a card's daily `price` for each
  variant, one number or `null` per day of `days`.
- `rates`, `/rates.json`: exchange rates per euro.

A variant with listings but no recent sales has no `price`, only TCGplayer's
`low`, `mid` and `high`, which are asking prices and can be anything a
seller types. Don't show them as the card's value. A set or card with no
prices answers 404.

## Schemas

Each file has a JSON Schema (draft 2020-12) under `/v1/schema/`, named in
`index.json`'s `files` and in each response's `Link: <…>;
rel="describedby"` header. Every field has a `description`. An `x-cc`
annotation says what JSON Schema can't: `ref` (the kind a pointer points
at), `vocabulary` (the group a value comes from), `picture` (a picture
id), `label` (the field that names an object) and `record` (a list item
that sums up a record with its own file). The shapes a file shares are
defined once under `$defs` with a `title` (`CardSummary`, `Card`,
`Variant`), which a type generator uses as the type's name.
