# CardCorpus API field reference

Every file of `https://pokemon.cardcorpus.com/v1/`, with each field's type and
description. This page is generated from the JSON Schemas under `/v1/schema/`.
The ideas the files share are explained in the guide, https://pokemon.cardcorpus.com/v1/docs/guide.md.

`?` marks a field that can be absent; absent means not known or not
applicable, and null never appears. A pointer names what it points at by
`$kind` and the ids that find it (https://pokemon.cardcorpus.com/v1/docs/guide.md#pointers); its rows below
list only the fields it adds, copies of the target's and facts of the link.
A vocabulary value's label and tell are in vocabulary.json, and a picture id
becomes a url through index.json's `pictures`.

## CardCorpus static API manifest

`/v1/schema/index.schema.json`

Where everything is: the files, how a pointer finds what it names, how a picture id becomes a url, and how many of each thing there are.

| Field | Type | Description |
| --- | --- | --- |
| `name` | `"CardCorpus"` | The dataset's name. |
| `description` | string | What the dataset is, in a sentence. |
| `version` | string | The version of the shape these files follow (semver). |
| `pictures` | object | How a picture id becomes a url: fill `{id}` and `{width}`, one of `widths`, into `sized` (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures). |
| `pictures.sized` | string | The url of a copy at one of `widths`, with `{id}` and `{width}`. |
| `pictures.widths` | list of integer | The widths a sized copy comes in, in pixels. |
| `prices` | object | Where prices are (https://pokemon.cardcorpus.com/v1/docs/guide.md#prices). |
| `prices.latest` | string | A set's latest daily prices, with `{set}`. |
| `prices.sets` | string | Up to 64 sets' latest prices at once, with `{sets}`: their ids joined by commas. |
| `prices.history` | string | A card's price history, with `{set}` and `{card}`. |
| `prices.rates` | string | The day's exchange rates, to show prices in another currency. |
| `prices.schemas` | object | The JSON Schema of what each url above answers, by its key here: a file in this tree. |
| `prices.schemas.latest` | string | The schema of a set's prices. |
| `prices.schemas.sets` | string | The schema of many sets' prices. |
| `prices.schemas.history` | string | The schema of a card's price history. |
| `prices.schemas.rates` | string | The schema of the exchange rates. |
| `docs`? | object | Where the documentation is, as Markdown: each a path in this tree, beside index.json. |
| `docs.guide` | string | The guide: the ideas the files share (pointers, copies, links, pictures, prices), which the schemas' descriptions cite by section. |
| `docs.reference` | string | The field reference: every field of every file, generated from the schemas. |
| `marketplaces` | object | Where a card opens in a marketplace: fill a product id from marketplaces/ into `tcgplayer` or `cardmarket` (https://pokemon.cardcorpus.com/v1/docs/guide.md#marketplace-ids). |
| `marketplaces.tcgplayer` | string | A TCGplayer product's url, with `{product}`. |
| `marketplaces.cardmarket` | string | A Cardmarket product's url, with `{product}`. |
| `catalogs` | list of object | The catalogs, in the order lists use. A catalog is one language's product line, with its own sets and numbering: `english`, `japanese`, `zh-hans` (Simplified Chinese), `zh-hant` (Traditional Chinese), `thai`, `indonesian`, `korean`, `french`, `german`, `italian`, `spanish`, `portuguese`, `latin-american-spanish` (Latin American Spanish), `dutch`, `russian` and `polish`. Its `language` is that language; a box such as the Pikachu World Collection holds cards in others, each with its own `language`. Each has its list of sets (sets/{catalog}.json) and of cards (cards/{catalog}.json). |
| `catalogs[].id` | vocabulary `catalog` | The catalog's id, the first part of its sets' ids: `english`. |
| `catalogs[].name` | string | Its name: "International". |
| `catalogs[].language` | vocabulary `language` | The language most of its cards are printed in: `en`. |
| `catalogs[].languages` | list of vocabulary `language` | Every language a card of it is printed in, the one with the most cards first: `en` and then the Pikachu World Collection's other languages for the english catalog. |
| `catalogs[].setCount` | integer | How many sets; always present, 0 included. |
| `catalogs[].cardCount` | integer | How many cards; always present, 0 included. |
| `catalogs[].variantCount` | integer | How many variants; always present, 0 included. |
| `counts` | map of integer | How many of each thing the files hold, each named for its unit. |
| `files` | list of object | Every file, as a pattern whose `{…}` a pointer's ids fill, with its JSON Schema. |
| `files[].file` | string | The file's path under /v1, with `{…}` for the ids that pick one. |
| `files[].schema` | string | Its JSON Schema's path under /v1. |
| `pointers` | map of object | How to find what a pointer points at, by its `$kind` (https://pokemon.cardcorpus.com/v1/docs/guide.md#pointers): fill `file` from the pointer's ids and open it, then walk `path` in order, at each step picking from `list` the item whose `match` field equals the pointer's `by` field. |
| `pointers.{key}.file` | string | The file to open, with `{…}` filled from the pointer's ids. |
| `pointers.{key}.path` | list of object | The steps to walk, in order. |
| `pointers.{key}.path[].list` | string | The list to pick from. |
| `pointers.{key}.path[].match` | string | The field of each item to compare. |
| `pointers.{key}.path[].by` | string | The pointer's field to compare it with. |

## CardCorpus sets of a catalog

`/v1/schema/sets.schema.json`

Every set of one catalog (`sets/english.json`, `sets/japanese.json` and so on, one a catalog), with what a list of sets needs: its name, era, type, language, release date, card count, set code, logo and symbol. A set is one product's card list, such as Base Set, a promo series or a deck. They come era by era and then by release date. There is no list of every catalog's sets: index.json's `catalogs` names the catalogs, in the order lists use. Each set's cards are in sets/{id}.json (https://pokemon.cardcorpus.com/v1/docs/guide.md#sets).

| Field | Type | Description |
| --- | --- | --- |
| `catalog` | vocabulary `catalog` | The catalog these sets are in. |
| `setCount` | integer | How many sets; always present, 0 included. |
| `sets` | list of object | Every set of the catalog, era by era and then by release date. |
| `sets[].id` | string | The set's id. Treat it as opaque: it never changes once published. |
| `sets[].catalog` | vocabulary `catalog` | The catalog the set belongs to. |
| `sets[].name` | string | The set's name as it prints it, in its language. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `sets[].englishName` | string | The set's name in English, on every set: "Expansion Pack (Base Set)" (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `sets[].era`? | pointer → era | Its era, by pointer, which stays when the era is renamed. |
| `sets[].era.id` | string | The era's id (eras.json). |
| `sets[].era.name` | string | A copy of the target's own `name`. |
| `sets[].era.englishName`? | string | A copy of the target's own `englishName`. |
| `sets[].type` | vocabulary `setType` | What the set is, by how its cards reached people. |
| `sets[].language` | vocabulary `language` | The language its cards are printed in: its catalog's, apart from a box like the Pikachu World Collection, which holds cards in several (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). |
| `sets[].position`? | integer | Its place among its catalog's sets of one release date, where the data orders them. |
| `sets[].line`? | pointer → line | The line the set is one part of, where several of our sets came out as one product or range of products (https://pokemon.cardcorpus.com/v1/docs/guide.md#lines). |
| `sets[].line.id` | string | The line's id (lines.json). |
| `sets[].line.name` | string | A copy of the target's own `name`. |
| `sets[].line.part` | string | The words that tell this set from the line's others: "Blaziken Tech" for "2004 World Championships Deck: Blaziken Tech", "Latias" for "EX Trainer Kit Latias". |
| `sets[].printedTotal`? | integer | The total its card numbers print (the 102 of 4/102), where they print one. |
| `sets[].actualTotal`? | integer | How many cards the set has by its own count, secret cards included, where known. |
| `sets[].cardCount` | integer | How many cards the set has in its `cards`; always present, 0 included. |
| `sets[].released`? | object | When the set came out, as precisely as is known (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `sets[].released.date`? | date | The date: 1999, 1999-06 or 1999-06-16. |
| `sets[].released.inferred`? | `true` | `true` where the date is an estimate: no page dates the set's print in its language, so its year is borrowed from another language's print (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `sets[].code`? | string | The set code it prints. |
| `sets[].printedCode`? | string or null | The set code and language mark its cards print where they name their set, as printed (in the set code's letter case): "PBL FR", "M6a F", "CSV3C", "SV9a". Null where the cards print no code of their own (Base Set); absent where nobody has checked yet (https://pokemon.cardcorpus.com/v1/docs/guide.md#printed-codes). |
| `sets[].logo`? | picture id | Its logo. |
| `sets[].symbol`? | picture id | The set symbol, the small mark its cards print (an icon a source gives for a set that prints none). |
| `sets[].linkCount` | integer | How many links the set has to other sets; always present, 0 included. |

## CardCorpus set

`/v1/schema/set.schema.json`

One set: its description, tell, print runs, versions and links, and every card with its variants.

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The set's id. Treat it as opaque: it never changes once published. |
| `catalog` | vocabulary `catalog` | The catalog the set belongs to. |
| `name` | string | The set's name as it prints it, in its language. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `englishName` | string | The set's name in English, on every set: "Expansion Pack (Base Set)" (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `era`? | pointer → era | Its era, by pointer, which stays when the era is renamed. |
| `era.id` | string | The era's id (eras.json). |
| `era.name` | string | A copy of the target's own `name`. |
| `era.englishName`? | string | A copy of the target's own `englishName`. |
| `type` | vocabulary `setType` | What the set is, by how its cards reached people. |
| `language` | vocabulary `language` | The language its cards are printed in: its catalog's, apart from a box like the Pikachu World Collection, which holds cards in several (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). |
| `position`? | integer | Its place among its catalog's sets of one release date, where the data orders them. |
| `line`? | pointer → line | The line the set is one part of, where several of our sets came out as one product or range of products (https://pokemon.cardcorpus.com/v1/docs/guide.md#lines). |
| `line.id` | string | The line's id (lines.json). |
| `line.name` | string | A copy of the target's own `name`. |
| `line.part` | string | The words that tell this set from the line's others: "Blaziken Tech" for "2004 World Championships Deck: Blaziken Tech", "Latias" for "EX Trainer Kit Latias". |
| `printedTotal`? | integer | The total its card numbers print (the 102 of 4/102), where they print one. |
| `actualTotal`? | integer | How many cards the set has by its own count, secret cards included, where known. |
| `cardCount` | integer | How many cards the set has in its `cards`; always present, 0 included. |
| `released`? | object | When the set came out, as precisely as is known (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `released.date`? | date | The date: 1999, 1999-06 or 1999-06-16. |
| `released.inferred`? | `true` | `true` where the date is an estimate: no page dates the set's print in its language, so its year is borrowed from another language's print (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `code`? | string | The set code it prints. |
| `printedCode`? | string or null | The set code and language mark its cards print where they name their set, as printed (in the set code's letter case): "PBL FR", "M6a F", "CSV3C", "SV9a". Null where the cards print no code of their own (Base Set); absent where nobody has checked yet (https://pokemon.cardcorpus.com/v1/docs/guide.md#printed-codes). |
| `logo`? | picture id | Its logo. |
| `symbol`? | picture id | The set symbol, the small mark its cards print (an icon a source gives for a set that prints none). |
| `linkCount`? | integer | How many links the set has to other sets; always present, 0 included. |
| `description`? | object | What we wrote about the set, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `description.text` | string | The text; paragraphs are split by a blank line. |
| `tell`? | object | How to tell this set's print of a card from other sets' prints of the same painting, in a few words, naming only what every card of the set prints (its symbol, its numbering); a card's own `tell` stands in for it (https://pokemon.cardcorpus.com/v1/docs/guide.md#set-tells). |
| `tell.text` | string | The text; paragraphs are split by a blank line. |
| `printRuns`? | list of object | The set's print runs whose place is known, earliest first, by edition (`unlimited` also stands for variants with no edition), each with what we say of it (https://pokemon.cardcorpus.com/v1/docs/guide.md#print-runs). |
| `printRuns[].edition` | vocabulary `edition` | The run's edition. |
| `printRuns[].released`? | object | When the run first came out, as precisely as is known (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `printRuns[].released.date`? | date | The date: 1999, 1999-06 or 1999-06-16. |
| `printRuns[].tell`? | object | How to spot it, in a few words. |
| `printRuns[].tell.text` | string | The tell. |
| `printRuns[].standard`? | `true` | Present where the run's variants are the set's standard ones and that isn't Unlimited (a Japanese booster set's 1st Edition). |
| `versions`? | list of object | The versions the set's product was sold in, earliest first, where the version decides some of the cards in it (pack designs of a set sold in waves, binders of two colours); a card found in only some names them in its own `onlyIn` (https://pokemon.cardcorpus.com/v1/docs/guide.md#versions). |
| `versions[].id` | string | The set version's id, unique in its set's `versions`. |
| `versions[].name` | string | The version's name. |
| `versions[].released`? | object | When the version came out, as precisely as is known (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `versions[].released.date`? | date | The date: 1999, 1999-06 or 1999-06-16. |
| `versions[].image`? | picture id | A picture of it. |
| `distributions`? | list of pointer → distribution | How every variant of the set was handed out, where all came out one way (a Prize Pack series, a Burger King collection), named once here and not on each variant (https://pokemon.cardcorpus.com/v1/docs/guide.md#distributions). |
| `distributions[].id` | string | The distribution's id (distributions/{id}.json). |
| `distributions[].name` | string | A copy of the target's own `name`. |
| `distributions[].category` | vocabulary `distribution` | A copy of the target's own `category`. |
| `distributions[].as`? | vocabulary `distribution` | How the variant came out of a distribution that also handed out others: as a `prize` or to `staff`. |
| `distributions[].region`? | string | Where it was handed out, where only in some places. |
| `backImage`? | picture id | The back every card of the set shares, where that is unlike the usual one (a World Championships Deck's). |
| `links`? | list of pointer → set | The set's links to other sets, in the order to show them: by `relation` (`equivalent`, `merged_into`, `subset_of`, `companion`), outgoing before incoming, by catalog in the vocabulary's order, then the most cards in common first. |
| `links[].id` | string | The set's id (sets/{catalog}.json). |
| `links[].catalog` | vocabulary `catalog` | The catalog the other set is in: whether it is in another language, and which, without reading its id. |
| `links[].relation` | vocabulary `setLink` | What the other set is to this one. |
| `links[].direction` | `incoming`, `outgoing` | Which way the link reads: `outgoing` from this set to the other, or `incoming` from the other to this one. |
| `links[].sameCardCount` | integer | How many of the two sets' cards are linked as the same card; always present, 0 included. |
| `cards`? | list of object | Every card, in number order. |
| `cards[].$kind` | `"card"` | What the pointer points at: `card`. index.json's `pointers` say where to find it (https://pokemon.cardcorpus.com/v1/docs/guide.md#pointers). |
| `cards[].set` | string | The set's id (sets/{catalog}.json). |
| `cards[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `cards[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `cards[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `cards[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `cards[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `cards[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `cards[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `cards[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `cards[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `cards[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `cards[].artists[].id` | string | The artist's id (artists.json). |
| `cards[].artists[].name` | string | The artist's name as this card prints it. |
| `cards[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `cards[].species[].id` | integer | The species' National Pokédex number. |
| `cards[].species[].name` | string | A copy of the target's own `name`. |
| `cards[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `cards[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `cards[].species[].forms[].name` | string | The form's name as this card gives it. |
| `cards[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `cards[].picture.front.image` | picture id | The picture's id. |
| `cards[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `cards[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `cards[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `cards[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `cards[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `cards[].picture.back.image` | picture id | The picture's id. |
| `cards[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |
| `cards[].painting`? | pointer → painting | The painting it prints, where another card prints it too. Its file (paintings/{id}.json) holds the card's links: the same card in other languages, the card it reprints and its reprints, and its parallels, with every card they name (https://pokemon.cardcorpus.com/v1/docs/guide.md#links). |
| `cards[].painting.id` | string | The painting's own id, which never changes: its first print's English name and a short code, such as `charizard-k3f9`. Where it was first printed is `firstPrint`'s to say, corrected there when research finds an earlier print. |
| `cards[].description`? | object | What we wrote about the card, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `cards[].description.text` | string | The text; paragraphs are split by a blank line. |
| `cards[].tell`? | object | How to tell this card's print where it differs from its set's `tell`. |
| `cards[].tell.text` | string or null | The tell; null where the card doesn't carry the set's tell (a basic Energy with no set mark). |
| `cards[].eReaderLayout`? | `e_card`, `ex` | Where it prints its e-Reader strip, as its pictures show: `e_card` (along the bottom, under the number, and down the left edge, as Expedition to Skyridge do) or `ex` (one strip along the bottom, under the weakness row, as the EX sets do). Absent where it prints none or nobody has looked (https://pokemon.cardcorpus.com/v1/docs/guide.md#e-reader-strip). |
| `cards[].characters`? | list of pointer → character | The people the card names, in the card's order: its subject (a Supporter's trainer), or the owner of its Pokémon (Misty's Psyduck). |
| `cards[].characters[].id` | string | The character's id (characters.json). |
| `cards[].characters[].name` | string | A copy of the target's own `name`. |
| `cards[].characters[].role` | `subject`, `owner` | What they are on the card: its `subject`, or the `owner` of its Pokémon. |
| `cards[].differsInSet`? | object | Present where its set prints the same painting on other cards too (its parallels, and any other card of the set around that painting): what tells this card from them, as the axes of its default variant that not all of them share, such as `{"finish": "pokeball_pattern"}`. On a card with parallels it is always there, `{}` where nothing tells it from them, so a list of the set's cards labels every card with parallels by it, with no links to follow (https://pokemon.cardcorpus.com/v1/docs/guide.md#telling-variants-apart). |
| `cards[].differsInSet.edition`? | vocabulary `edition` | Its edition. |
| `cards[].differsInSet.finish`? | vocabulary `finish` | Its finish. |
| `cards[].differsInSet.material`? | vocabulary `material` | What it is printed on, where that isn't ordinary card. |
| `cards[].differsInSet.stamp`? | vocabulary `stamp` | The stamp it carries. |
| `cards[].differsInSet.errorState`? | vocabulary `errorState` | The printing error it shows. |
| `cards[].differsInSet.serialCode`? | string | Its serial code. |
| `cards[].printVersion`? | integer | Which of a pair it is, 1 or 2, on the rare pairs of different cards in one set with the same name that the numbering doesn't tell apart. The Celadon City Gym deck's two Erika's Oddish, both numbered No. 043, are 1 and 2; so are Pikachu World Collection's two numberless Pikachu, the blue binder's and the green binder's. It counts within the pair and is not a pointer into the set's `versions` (that is `onlyInSetVersions`). |
| `cards[].onlyInSetVersions`? | list of pointer → set-version | Present where its set is sold in versions (the set's `versions`) and this card is in only some of them: those versions. A card without it is in every version (https://pokemon.cardcorpus.com/v1/docs/guide.md#versions). |
| `cards[].onlyInSetVersions[].id` | string | The set version's id, unique in its set's `versions`. |
| `cards[].pieces`? | object | Present where the card is one piece of a card printed in pieces, a LEGEND's half or a V-UNION's quarter: the whole card's pieces (https://pokemon.cardcorpus.com/v1/docs/guide.md#pieces). |
| `cards[].pieces.format` | vocabulary `piece` | What kind of card in pieces it is: `legend` or `v_union`. |
| `cards[].pieces.position` | integer | This piece's position, counted from 1 in vocabulary.json's `positions` for the format. |
| `cards[].pieces.cards` | list of pointer → card | Every piece of the whole card, this one included, in reading order. |
| `cards[].pieces.cards[].id` | string | The card's id, unique in its set. |
| `cards[].variants` | list of object | Its variants, in role order; the standard one, or the first on a test-only card, supplies the card picture. |
| `cards[].variants[].id` | string | The variant's id, unique within its card: its physical axes as a slug, or "plain". Treat it as opaque: it never changes once published. |
| `cards[].variants[].name` | string | What the card's variant strip calls it (https://pokemon.cardcorpus.com/v1/docs/guide.md#telling-variants-apart). |
| `cards[].variants[].role` | vocabulary `role` | What this variant is on its card; variants are listed in role order (https://pokemon.cardcorpus.com/v1/docs/guide.md#version-roles). |
| `cards[].variants[].finish`? | vocabulary `finish` | Its finish. |
| `cards[].variants[].material`? | vocabulary `material` | What it is printed on, where that isn't ordinary card. |
| `cards[].variants[].edition`? | vocabulary `edition` | Its edition. |
| `cards[].variants[].serialCode`? | string | Its serial code; always present on a `serial_code` copy. |
| `cards[].variants[].serialCodeVariantOf`? | pointer → variant | Another code of the same physical version, by its pointer; always present on a `serial_code` copy. |
| `cards[].variants[].serialCodeVariantOf.id` | string | The variant's id, unique among its card's variants. |
| `cards[].variants[].stamp`? | vocabulary `stamp` | The stamp it carries. |
| `cards[].variants[].errorState`? | vocabulary `errorState` | The printing error it shows. |
| `cards[].variants[].eReaderStrip`? | `dot_code`, `holo_bars`, `blank` | What it prints where its card prints the e-Reader strip (the card's `eReaderLayout`), as its own picture shows: the dot code, holo bars in its place (a reverse holo, a holo rare), or nothing (`blank`: an EX card without a strip). Absent where nobody has looked (https://pokemon.cardcorpus.com/v1/docs/guide.md#e-reader-strip). |
| `cards[].variants[].editionMark`? | `first_edition` | The edition mark it prints: `first_edition`, the 1st Edition symbol. Its edition's mark (vocabulary `edition`, `mark`) unless the variant says otherwise, as Base Set Machamp's stamped ©1999-2000 4th print does. Absent where it prints none (https://pokemon.cardcorpus.com/v1/docs/guide.md#edition-mark). |
| `cards[].variants[].printRun`? | vocabulary `edition` | The edition of the set's print run (its `printRuns`) the variant came out in, where the set lists that run: look it up there. Equal to `edition` wherever both are present; `unlimited` on a variant with no `edition` whose set has runs; absent where the variant's edition has no known place among the set's runs (https://pokemon.cardcorpus.com/v1/docs/guide.md#telling-variants-apart). |
| `cards[].variants[].differsFromStandard`? | object | Its axes that the card's standard variant doesn't share (all of them on a card with no standard variant); none on the standard variant. |
| `cards[].variants[].differsFromStandard.edition`? | vocabulary `edition` | Its edition. |
| `cards[].variants[].differsFromStandard.finish`? | vocabulary `finish` | Its finish. |
| `cards[].variants[].differsFromStandard.material`? | vocabulary `material` | What it is printed on, where that isn't ordinary card. |
| `cards[].variants[].differsFromStandard.stamp`? | vocabulary `stamp` | The stamp it carries. |
| `cards[].variants[].differsFromStandard.errorState`? | vocabulary `errorState` | The printing error it shows. |
| `cards[].variants[].differsFromStandard.serialCode`? | string | Its serial code. |
| `cards[].variants[].tell`? | object | How to spot it on the card, in a sentence or two. |
| `cards[].variants[].tell.text` | string | The tell. |
| `cards[].variants[].picture`? | object | The variant's picture (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): its own, or, with `front.of`, another variant's standing in for one it lacks (the standard variant's; on the standard variant, the first other variant's); and its back, where that is unlike the usual one. |
| `cards[].variants[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `cards[].variants[].picture.front.image` | picture id | The picture's id. |
| `cards[].variants[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `cards[].variants[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `cards[].variants[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `cards[].variants[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `cards[].variants[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `cards[].variants[].picture.back.image` | picture id | The picture's id. |
| `cards[].variants[].description`? | object | What we wrote about the variant, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `cards[].variants[].description.text` | string | The text; paragraphs are split by a blank line. |
| `cards[].variants[].distributions`? | list of pointer → distribution | How the variant was handed out beyond its set's own `distributions`: each distribution by its pointer, with its name and category, and `as` and `region` where they apply. |
| `cards[].variants[].distributions[].id` | string | The distribution's id (distributions/{id}.json). |
| `cards[].variants[].distributions[].name` | string | A copy of the target's own `name`. |
| `cards[].variants[].distributions[].category` | vocabulary `distribution` | A copy of the target's own `category`. |
| `cards[].variants[].distributions[].as`? | vocabulary `distribution` | How the variant came out of a distribution that also handed out others: as a `prize` or to `staff`. |
| `cards[].variants[].distributions[].region`? | string | Where it was handed out, where only in some places. |
| `cards[].printRuns`? | list of pointer → variant | On a card printed in more than one run: its standard variant and print runs, in printing order (https://pokemon.cardcorpus.com/v1/docs/guide.md#telling-variants-apart). |
| `cards[].printRuns[].id` | string | The variant's id, unique among its card's variants. |
| `cards[].play`? | pointer → play | The cards that play the same as it, whatever their picture, where there are others: the same name and text that works the same, so one may stand in for another in a deck. Its file (plays/{id}.json) lists them all. English cards only, for now (https://pokemon.cardcorpus.com/v1/docs/guide.md#plays). |
| `cards[].play.id` | string | The play's own id: its cards' English name and a short code, such as `switch-4175`. It stays the same as cards join the play. |

## CardCorpus eras

`/v1/schema/eras.schema.json`

Every era, each with its sets in order. An era is a run of sets that share a game generation and a card design, the way collectors group them: "Sword & Shield" holds the sets from Sword & Shield (2020) until Scarlet & Violet began, "Neo Series" the sets from Neo Genesis to Neo Destiny. Each catalog has its own eras, since the Japanese sets group differently: the English "Original Series" and the Japanese "Original Era" are two eras. A set names its era in its `era`.

| Field | Type | Description |
| --- | --- | --- |
| `eraCount` | integer | How many eras; always present, 0 included. |
| `eras` | list of object | Every era: the English ones oldest first, then the Japanese, the Simplified Chinese and the French. |
| `eras[].catalog` | vocabulary `catalog` | The catalog whose sets it groups. |
| `eras[].id` | string | Its id: its catalog, then a slug kept when the era is renamed ("english/original-series"). |
| `eras[].name` | string | Its name, as collectors say it, in its catalog's language where its sets name it so: "Sword & Shield", "e-Card Series", "Épée et Bouclier" (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `eras[].englishName`? | string | Its name in English, where `name` is another language's: "Sword & Shield" beside "Épée et Bouclier". |
| `eras[].position` | integer | Its place among its catalog's eras, oldest lowest. Sort by it; the numbers leave gaps (300, 400, 500). |
| `eras[].sets` | list of pointer → set | Every set of the era, in its catalog's order (sets/{catalog}.json). |
| `eras[].sets[].id` | string | The set's id (sets/{catalog}.json). |

## CardCorpus Pokémon

`/v1/schema/pokemon.schema.json`

Every species with its forms, in National Pokédex order; each family in full is pokemon-families/{family}.json (https://pokemon.cardcorpus.com/v1/docs/guide.md#species-and-forms).

| Field | Type | Description |
| --- | --- | --- |
| `speciesCount` | integer | How many species; always present, 0 included. |
| `formCount` | integer | How many forms; always present, 0 included. |
| `familyCount` | integer | How many evolution families; always present, 0 included. |
| `transitionCount` | integer | How many transitions between forms; always present, 0 included. |
| `generations` | list of object | The region each generation's games are set in. |
| `generations[].generation` | integer | The generation. |
| `generations[].region` | string | Its region's name. |
| `regions` | map of object | The regions regional forms come from, by a form's `region`. |
| `regions.{key}.label` | string | The region's name. |
| `regions.{key}.term`? | string | The term for its forms ("Alolan"). |
| `species` | list of object | Every species, in National Pokédex order. |
| `species[].dex` | integer | Its National Pokédex number. |
| `species[].name` | string | Its name. |
| `species[].generation` | integer | The generation the species first appeared in. |
| `species[].category`? | string | Its Pokédex category ("Mouse Pokémon"). |
| `species[].status`? | `baby`, `legendary`, `mythical` | Where it is one: a baby, legendary or mythical Pokémon. |
| `species[].family` | pointer → pokemon-family | Its evolution family, by pointer. |
| `species[].family.id` | string | The evolution family's id (pokemon-families/{id}.json). |
| `species[].names`? | map of string | Its name in other languages, by language code. |
| `species[].types` | list of string | Its default form's types. |
| `species[].tagline`? | string | Its one-line description. |
| `species[].forms` | list of object | Every form but the first, the default, with how many cards show it. |
| `species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `species[].forms[].name` | string | The form's name. |
| `species[].forms[].category`? | `regional`, `mega`, `primal`, `gigantamax`, `costume`, `cosmetic`, `alternate` | What kind of form it is, where it isn't the default. |
| `species[].forms[].generation` | integer | The generation the form first appeared in. |
| `species[].forms[].cardCount`? | integer | How many cards that show the form; always present, 0 included. |
| `species[].cardCount` | integer | How many cards that show it in any form but a regional one; always present, 0 included. |
| `species[].catalogs`? | list of vocabulary `catalog` | The catalogs with cards of it in any form, each with a file of them (pokemon-cards/{catalog}/{dex}.json); none where it has no cards. |
| `species[].totalCardCount` | integer | How many cards that show it in any form, regional ones included, each card once; always present, 0 included. |
| `species[].pictures`? | object | Its picture, a picture id: the official `artwork`. |
| `species[].pictures.artwork`? | picture id | Its official artwork. |

## CardCorpus Pokémon evolution family

`/v1/schema/pokemon-family.schema.json`

One evolution family: its species with every form, and the transitions between forms (evolutions, Mega Evolution, Primal Reversion, Gigantamax, form changes and fusions).

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The evolution family's id (pokemon-families/{id}.json). |
| `species` | list of object | Its species, in National Pokédex order. |
| `species[].dex` | integer | Its National Pokédex number. |
| `species[].name` | string | Its name. |
| `species[].generation` | integer | The generation the species first appeared in. |
| `species[].category`? | string | Its Pokédex category ("Mouse Pokémon"). |
| `species[].status`? | `baby`, `legendary`, `mythical` | Where it is one: a baby, legendary or mythical Pokémon. |
| `species[].family` | pointer → pokemon-family | Its evolution family, by pointer. |
| `species[].family.id` | string | The evolution family's id (pokemon-families/{id}.json). |
| `species[].names`? | map of string | Its name in other languages, by language code. |
| `species[].description`? | object | What we wrote about the species, in three voices. |
| `species[].description.fun` | string | Two to three playful sentences: the one pages show. |
| `species[].description.guide`? | string | A calmer, Pokédex-like voice. |
| `species[].description.tagline`? | string | One line, for lists and tooltips. |
| `species[].branches`? | object | Our note on how the family branches, where it does. |
| `species[].branches.text` | string | The note. |
| `species[].forms` | list of object | Every form, the default first. |
| `species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `species[].forms[].name` | string | The form's name. |
| `species[].forms[].category`? | `regional`, `mega`, `primal`, `gigantamax`, `costume`, `cosmetic`, `alternate` | What kind of form it is, where it isn't the default. |
| `species[].forms[].region`? | `alola`, `galar`, `hisui`, `paldea` | The region a regional form comes from (pokemon.json's `regions`). |
| `species[].forms[].generation` | integer | The generation the form first appeared in. |
| `species[].forms[].types` | list of string | Its types, in game order. |
| `species[].forms[].battleOnly`? | boolean | Present where the form only exists during battle. |
| `species[].forms[].names`? | map of string | Its name in other languages, by language code. |
| `species[].forms[].pictures`? | object | Its picture, a picture id: the official `artwork`. |
| `species[].forms[].pictures.artwork`? | picture id | Its official artwork. |
| `species[].forms[].description`? | object | What we wrote about the form, in three voices. |
| `species[].forms[].description.fun` | string | Two to three playful sentences: the one pages show. |
| `species[].forms[].description.guide`? | string | A calmer, Pokédex-like voice. |
| `species[].forms[].description.tagline`? | string | One line, for lists and tooltips. |
| `species[].forms[].evolvesInto`? | list of pointer → form | Every form it evolves into, at any remove (family files only). |
| `species[].forms[].evolvesInto[].species` | integer | The species' National Pokédex number. |
| `species[].forms[].evolvesInto[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `transitions` | list of object | Every step between its forms. |
| `transitions[].from` | pointer → form | The form before the step. |
| `transitions[].from.species` | integer | The species' National Pokédex number. |
| `transitions[].from.id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `transitions[].to` | pointer → form | The form after the step. |
| `transitions[].to.species` | integer | The species' National Pokédex number. |
| `transitions[].to.id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `transitions[].change` | `evolves`, `mega`, `primal`, `gigantamax`, `form_change`, `fuses` | What kind of step it is. |
| `transitions[].with`? | string | The species it fuses with, on a `fuses` step. |
| `transitions[].how`? | string | How it happens today. |
| `transitions[].otherWays`? | list of string | Other ways older games or other places had. |
| `transitions[].note`? | object | Our note on why the step is there. |
| `transitions[].note.text` | string | The note. |
| `transitions[].note.term`? | string | What the note is about: `baby`, `regional_rule` or `regional_evolution`. |

## CardCorpus vocabulary

`/v1/schema/vocabulary.schema.json`

The words public/v1 uses, each with a label and, where curated, a tell: how to spot it. Roles, reprint differences, a variant's axes and distribution kinds; rarity words and how showy they are; catalogs and languages; set types and links between sets; card types and pieces. A value is never renamed: a rename adds a new value (https://pokemon.cardcorpus.com/v1/docs/guide.md#vocabulary).

| Field | Type | Description |
| --- | --- | --- |
| `role` | map of object | What a variant is on its card (a variant's `role`). Each value by its id. |
| `role.{key}.label` | string | The value's label, to show it by. |
| `role.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `role.{key}.description`? | string | What a group of a role's variants holds. |
| `role.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `differs` | map of object | What sets a reprint apart from its original (a reprint's `differs`). Each value by its id. |
| `differs.{key}.label` | string | The value's label, to show it by. |
| `differs.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `differs.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `finish` | map of object | A variant's finish. Each value by its id. |
| `finish.{key}.label` | string | The value's label, to show it by. |
| `finish.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `finish.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `material` | map of object | What a variant is printed on, where that isn't ordinary card. Each value by its id. |
| `material.{key}.label` | string | The value's label, to show it by. |
| `material.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `material.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `edition` | map of object | A variant's edition, or a print run's. Each value by its id. |
| `edition.{key}.label` | string | The value's label, to show it by. |
| `edition.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `edition.{key}.mark`? | `first_edition` | The edition mark an edition prints. |
| `edition.{key}.order`? | integer | The word's place when its group is listed in order, from 1; for an edition, its place among a card's print runs, earliest first, where known. |
| `edition.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `stamp` | map of object | A stamp a variant carries. Each value by its id. |
| `stamp.{key}.label` | string | The value's label, to show it by. |
| `stamp.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `stamp.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `errorState` | map of object | A printing error a variant shows. Each value by its id. |
| `errorState.{key}.label` | string | The value's label, to show it by. |
| `errorState.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `errorState.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `distribution` | map of object | What kind of distribution something is, and how a variant came out of it (`as`). Each value by its id. |
| `distribution.{key}.label` | string | The value's label, to show it by. |
| `distribution.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `distribution.{key}.order`? | integer | The word's place when its group is listed in order, from 1; for an edition, its place among a card's print runs, earliest first, where known. |
| `distribution.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `rarity` | map of object | A card's rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). Each value by its id. |
| `rarity.{key}.label` | string | The value's label, to show it by. |
| `rarity.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `rarity.{key}.tier`? | vocabulary `rarityTier` | The rarity word's tier. |
| `rarity.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `rarityTier` | map of object | How showy a rarity is: our editorial grouping of rarity words. Each value by its id. |
| `rarityTier.{key}.label` | string | The value's label, to show it by. |
| `rarityTier.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `rarityTier.{key}.rank`? | integer | How showy a rarity tier is, from 0. |
| `rarityTier.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `catalog` | map of object | A catalog: the product line a set belongs to, one a language (english, japanese, zh-hans, zh-hant, thai, indonesian, korean, french, german, italian, spanish, portuguese, latin-american-spanish, dutch, russian, polish). Each value by its id. |
| `catalog.{key}.label` | string | The value's label, to show it by. |
| `catalog.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `catalog.{key}.short`? | string | The catalog's short code: EN, JP, ZH. |
| `catalog.{key}.language`? | vocabulary `language` | The language most of the catalog's cards are printed in. |
| `catalog.{key}.order`? | integer | The word's place when its group is listed in order, from 1; for an edition, its place among a card's print runs, earliest first, where known. |
| `catalog.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `language` | map of object | A language cards are printed in. Each value by its id. |
| `language.{key}.label` | string | The value's label, to show it by. |
| `language.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `language.{key}.catalog`? | vocabulary `catalog` | The catalog printed in the language, where one is. |
| `language.{key}.codeMark`? | string | The letters cards in the language print after the set code where they name their set: "EN" in "PBL EN", "F" in "M6a F", "C" in "CSV3C". Absent for a language whose cards print the code alone (Japanese, Korean). A set's `printedCode` is its set code with this mark (https://pokemon.cardcorpus.com/v1/docs/guide.md#printed-codes). |
| `language.{key}.codeMarkJoined`? | `true` | `true` where the mark is joined to the code with no space (Simplified Chinese's "C": "CSV3C"); absent where a space comes between ("PBL EN"). |
| `language.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `setType` | map of object | What a set is, by how its cards reached people. Each value by its id. |
| `setType.{key}.label` | string | The value's label, to show it by. |
| `setType.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `setType.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `setLink` | map of object | What one set is to another. Each value by its id. |
| `setLink.{key}.label` | string | The value's label, to show it by. |
| `setLink.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `setLink.{key}.incoming`? | string | The set link's label read from the other set, where it differs. |
| `setLink.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `cardType` | map of object | What kind of card it is. Each value by its id. |
| `cardType.{key}.label` | string | The value's label, to show it by. |
| `cardType.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `cardType.{key}.order`? | integer | The word's place when its group is listed in order, from 1; for an edition, its place among a card's print runs, earliest first, where known. |
| `cardType.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |
| `piece` | map of object | A card printed in pieces (a card's `pieces.format`). Each value by its id. |
| `piece.{key}.label` | string | The value's label, to show it by. |
| `piece.{key}.tell`? | string | How to spot it on the card, in a sentence. |
| `piece.{key}.positions`? | list of string | The positions of a card printed in pieces, in the order a piece's `position` counts them from 1. |
| `piece.{key}.useCount` | integer | How many things use it: cards for a rarity or card type, variants for a variant's axis or role, sets for a set type, links for a set link; always present, 0 included. |

## CardCorpus distributions

`/v1/schema/distributions.schema.json`

Every distribution. A distribution is one way cards reached people other than in booster packs: a boxed product with a promo inside ("151 Collection—Alakazam ex"), an event ("Regional Championships"), a shop or restaurant giveaway ("McDonald's Happy Meal 2019", "Burger King"), a program ("Play! Pokémon Prize Pack"), a campaign, a magazine or book, a test run, or cards given to staff. A variant says which distributions it came out through in its own `distributions`; a set says it once where every card of it came out one way. Each entry here counts those variants and sets, and distributions/{id}.json lists them. `series` then groups the distributions that recur: "McDonald's Happy Meal" holds each year's Happy Meal, "World Championships" each year's event (https://pokemon.cardcorpus.com/v1/docs/guide.md#distributions).

| Field | Type | Description |
| --- | --- | --- |
| `distributionCount` | integer | How many distributions; always present, 0 included. |
| `distributions` | list of object | Every distribution, in id order. |
| `distributions[].id` | string | The distribution's id (distributions/{id}.json). |
| `distributions[].name` | string | Its name: "McDonald's Happy Meal 2019", "Regional Championships". |
| `distributions[].category` | vocabulary `distribution` | What kind of distribution it is: a product, an event, a program, a shop, a campaign, a publication, a test run or staff. |
| `distributions[].series`? | pointer → distribution-series | The series it belongs to, where it recurs: "McDonald's Happy Meal" for "McDonald's Happy Meal 2011". |
| `distributions[].series.id` | string | The distribution series' id (distribution-series/{id}.json). |
| `distributions[].series.name`? | string | A copy of the target's own `name`. |
| `distributions[].variantCount` | integer | How many variants that came out through it: every variant of the sets that name it, and the variants that name it themselves; always present, 0 included. |
| `distributions[].setCount` | integer | How many sets whose every card came out through it (Burger King has two); 0 where only single variants name it; always present, 0 included. |
| `distributions[].catalogs` | list of vocabulary `catalog` | The catalogs its variants are in. |
| `series` | list of object | Every distribution series: distributions that recur under one name, such as a yearly event or promotion ("World Championships", "Collector's Tins"). |
| `series[].id` | string | The distribution series' id (distribution-series/{id}.json). |
| `series[].name` | string | Its name, without the year or season: "McDonald's Happy Meal". |
| `series[].category` | vocabulary `distribution` | What kind of distribution it is: a product, an event, a program, a shop, a campaign, a publication, a test run or staff. |
| `series[].distributions` | list of pointer → distribution | Its distributions, in order (by the year and season each name gives). |
| `series[].distributions[].id` | string | The distribution's id (distributions/{id}.json). |
| `series[].variantCount` | integer | How many variants that came out through its distributions; always present, 0 included. |
| `series[].catalogs` | list of vocabulary `catalog` | The catalogs its variants are in. |

## CardCorpus moved addresses

`/v1/schema/moved.schema.json`

Every address that was published and no longer resolves, and what became of it: merged into another, moved to another, split into several (every successor, none picked), or removed. An id never changes once published and a retired address is never given to anything else, so a consumer only needs this file when a stored pointer stops resolving. Each successor is the current address, any chain already followed (https://pokemon.cardcorpus.com/v1/docs/guide.md#identifiers).

| Field | Type | Description |
| --- | --- | --- |
| `moved` | list of object | Every retired address, with what became of it. |
| `moved[].from` | pointer → catalog or set or card or variant or painting | The retired address. |
| `moved[].from.id` | string | Its own id: the catalog's (index.json's `catalogs`; every set, card and variant id under an old catalog's is read under the new one's), the set's (sets/{catalog}.json), the card's in its set, the variant's among its card's, or the painting's (a painting address with a `set` is an old one, named for its first print's card, from before paintings had ids of their own). |
| `moved[].merged`? | pointer → catalog or set or card or variant or painting | The address it was merged into. |
| `moved[].merged.id` | string | Its own id: the catalog's (index.json's `catalogs`; every set, card and variant id under an old catalog's is read under the new one's), the set's (sets/{catalog}.json), the card's in its set, the variant's among its card's, or the painting's (a painting address with a `set` is an old one, named for its first print's card, from before paintings had ids of their own). |
| `moved[].moved`? | pointer → catalog or set or card or variant or painting | The address it moved to. |
| `moved[].moved.id` | string | Its own id: the catalog's (index.json's `catalogs`; every set, card and variant id under an old catalog's is read under the new one's), the set's (sets/{catalog}.json), the card's in its set, the variant's among its card's, or the painting's (a painting address with a `set` is an old one, named for its first print's card, from before paintings had ids of their own). |
| `moved[].split`? | list of pointer → catalog or set or card or variant or painting | Every address it was split into; none is picked. |
| `moved[].split[].id` | string | Its own id: the catalog's (index.json's `catalogs`; every set, card and variant id under an old catalog's is read under the new one's), the set's (sets/{catalog}.json), the card's in its set, the variant's among its card's, or the painting's (a painting address with a `set` is an old one, named for its first print's card, from before paintings had ids of their own). |
| `moved[].removed`? | `true` | Present where it was removed with no successor. |

## CardCorpus distribution series

`/v1/schema/distribution-series.schema.json`

One distribution series in full: distributions that recur under one name, such as "World Championships", with one distribution for each year's event. It has what we wrote about the series where we have written it up, and its distributions in order.

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The distribution series' id (distribution-series/{id}.json). |
| `name` | string | Its name. |
| `category` | vocabulary `distribution` | What kind of distribution it is: a product, an event, a program, a shop, a campaign, a publication, a test run or staff. |
| `description`? | object | What we wrote about the series, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `description.text` | string | The text; paragraphs are split by a blank line. |
| `distributions` | list of pointer → distribution | Its distributions, in order. |
| `distributions[].id` | string | The distribution's id (distributions/{id}.json). |
| `distributions[].name` | string | A copy of the target's own `name`. |
| `distributions[].short` | string | Its name in the series: what it adds to the series' name ("2005"), else its name. |
| `distributions[].variantCount` | integer | How many variants that came out through it; always present, 0 included. |
| `distributions[].setCount` | integer | How many sets that name it for every variant of them; always present, 0 included. |
| `distributions[].catalogs` | list of vocabulary `catalog` | The catalogs its variants are in. |
| `distributions[].image`? | picture id | Its first set's or first variant's picture. |

## CardCorpus distribution

`/v1/schema/distribution.schema.json`

One distribution in full: one way cards reached people other than in booster packs, such as "McDonald's Happy Meal 2019" or "Regional Championships". It has what we wrote about it where we have written it up, the sets whose every card came out through it, earliest first, and then each single variant that came out through it, in set order.

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The distribution's id (distributions/{id}.json). |
| `name` | string | Its name. |
| `short`? | string | Its name in its series, where it is in one (see distribution-series). |
| `category` | vocabulary `distribution` | What kind of distribution it is: a product, an event, a program, a shop, a campaign, a publication, a test run or staff. |
| `series`? | pointer → distribution-series | The series it belongs to, where it is in one. |
| `series.id` | string | The distribution series' id (distribution-series/{id}.json). |
| `series.name` | string | A copy of the target's own `name`. |
| `description`? | object | What we wrote about the distribution, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `description.text` | string | The text; paragraphs are split by a blank line. |
| `sets`? | list of pointer → set | The sets that name it, earliest first. |
| `sets[].id` | string | The set's id (sets/{catalog}.json). |
| `sets[].name` | string | A copy of the target's own `name`. |
| `sets[].catalog` | vocabulary `catalog` | A copy of the target's own `catalog`. |
| `sets[].released`? | object | When the set first came out, as precisely as is known (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `sets[].released.date`? | date | The date: 1999, 1999-06 or 1999-06-16. |
| `sets[].cardCount` | integer | How many cards in the set; always present, 0 included. |
| `sets[].variantCount` | integer | How many variants in the set; always present, 0 included. |
| `sets[].image`? | picture id | Its first variant's picture. |
| `sets[].as`? | vocabulary `distribution` | How the variant came out of a distribution that also handed out others: as a `prize` or to `staff`. |
| `sets[].region`? | string | Where it was handed out, where only in some places. |
| `variants` | list of pointer → card | Each variant it handed out one by one, in set order: its card, with the variant under `variant`. |
| `variants[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `variants[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `variants[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `variants[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `variants[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `variants[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `variants[].number.printed`? | string | The number as printed, where the card prints one. |
| `variants[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `variants[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `variants[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `variants[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `variants[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `variants[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `variants[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `variants[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `variants[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `variants[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `variants[].artists[].id` | string | The artist's id (artists.json). |
| `variants[].artists[].name` | string | The artist's name as this card prints it. |
| `variants[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `variants[].species[].id` | integer | The species' National Pokédex number. |
| `variants[].species[].name` | string | A copy of the target's own `name`. |
| `variants[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `variants[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `variants[].species[].forms[].name` | string | The form's name as this card gives it. |
| `variants[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `variants[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `variants[].picture.front.image` | picture id | The picture's id. |
| `variants[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `variants[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `variants[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `variants[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `variants[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `variants[].picture.back.image` | picture id | The picture's id. |
| `variants[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `variants[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |
| `variants[].as`? | vocabulary `distribution` | How the variant came out of a distribution that also handed out others: as a `prize` or to `staff`. |
| `variants[].region`? | string | Where it was handed out, where only in some places. |

## CardCorpus curated collections

`/v1/schema/collections.schema.json`

Our curated collections: hand-picked lists of card variants that belong together across sets and languages, which no single set file gives you. "All 25th Stamped Cards" gathers every card with a 25th-anniversary stamp, from Celebrations to Happy Meals; "Banned Cards" the cards banned from play. Each collection is split into titled sections, and each section lists its variants.

| Field | Type | Description |
| --- | --- | --- |
| `collections` | list of object | Every collection, each complete in this file. |
| `collections[].id` | string | The collection's id in collections.json. |
| `collections[].title` | string | Its title: "All 30th Stamped Cards". |
| `collections[].updated` | string (date) | When we last checked it, a full date. |
| `collections[].description` | string | What it collects, in a sentence or two for people to read. |
| `collections[].coverage` | string | How complete it is and what it leaves out, in a sentence (which languages it covers, for one). |
| `collections[].sections` | list of object | Its sections, in order. |
| `collections[].sections[].id` | string | The section's id, unique in its collection. |
| `collections[].sections[].title` | string | Its title. |
| `collections[].sections[].description` | object | What we wrote about the section, for people to read (https://pokemon.cardcorpus.com/v1/docs/guide.md#descriptions). |
| `collections[].sections[].description.text` | string | The text; paragraphs are split by a blank line. |
| `collections[].sections[].see`? | list of pointer → set or distribution | Our set and distribution pages its cards come from. |
| `collections[].sections[].see[].id` | string | The set's id (sets/{catalog}.json) or the distribution's (distributions/{id}.json). |
| `collections[].sections[].see[].name` | string | A copy of the target's own `name`. |
| `collections[].sections[].see[].category`? | vocabulary `distribution` | A copy of the target's own `category`. Present on a `distribution`. |
| `collections[].sections[].see[].languages` | list of vocabulary `language` | The languages of its cards: a set's, or those of the sets holding a distribution's cards, English first, then Japanese, then Chinese. A section lists its pages in that order too. |
| `collections[].sections[].groups`? | list of object | Small groups of its variants that came the same way (one box, one deck), in order; each variant names its `group`. |
| `collections[].sections[].groups[].id` | string | The group's id, unique in its section. |
| `collections[].sections[].groups[].title` | string | Its label. |
| `collections[].sections[].variants` | list of pointer → card or variant | Each variant by its pointer (the card's, where the collection names no variant). |
| `collections[].sections[].variants[].id` | string | Its own id: the card's in its set, or the variant's among its card's variants. |
| `collections[].sections[].variants[].language` | vocabulary `language` | A copy of the target's own `language`. |
| `collections[].sections[].variants[].note`? | string | Our note on why it is here. |
| `collections[].sections[].variants[].group`? | string | The id of the section's group it is in, when the section has groups. |
| `collections[].sections[].variants[].via`? | `v_union`, `legend` | Set when the variant is here because it is a piece of a V-UNION or LEGEND card whose other piece qualifies, not on its own stamp. |

## CardCorpus artists

`/v1/schema/artists.schema.json`

Every artist a card's illustrator credit names, such as Mitsuhiro Arita or Ken Sugimori, each once however cards spell them, with how many cards credit them. Each has a file of their cards per catalog, artists/{catalog}/{id}.json.

| Field | Type | Description |
| --- | --- | --- |
| `artistCount` | integer | How many artists; always present, 0 included. |
| `artists` | list of object | Every artist, in id order. |
| `artists[].id` | string | The artist's id (artists.json). |
| `artists[].name` | string | The name to show them under. |
| `artists[].cardCount` | integer | How many cards whose credit names them; always present, 0 included. |
| `artists[].catalogs`? | list of vocabulary `catalog` | The catalogs they have cards in, each with a file of them (artists/{catalog}/{id}.json); none where they have no cards. |
| `artists[].credits`? | list of string | The other spellings cards credit them under, where there are any: "Kei Hoshiba" for K. Hoshiba. |

## CardCorpus artist

`/v1/schema/artist.schema.json`

One artist's cards in one catalog: every card of it whose credit names them (their id among the card's `artists`), oldest first (https://pokemon.cardcorpus.com/v1/docs/guide.md#card-lists). A catalog they have no cards in has no file.

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The artist's id (artists.json). |
| `name` | string | The name to show them under. |
| `catalog` | vocabulary `catalog` | The catalog whose cards these are. |
| `cards` | list of pointer → card | Every card of the catalog whose credit names them, oldest first. |
| `cards[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `cards[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `cards[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `cards[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `cards[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `cards[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `cards[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `cards[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `cards[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `cards[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `cards[].artists[].id` | string | The artist's id (artists.json). |
| `cards[].artists[].name` | string | The artist's name as this card prints it. |
| `cards[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `cards[].species[].id` | integer | The species' National Pokédex number. |
| `cards[].species[].name` | string | A copy of the target's own `name`. |
| `cards[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `cards[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `cards[].species[].forms[].name` | string | The form's name as this card gives it. |
| `cards[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `cards[].picture.front.image` | picture id | The picture's id. |
| `cards[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `cards[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `cards[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `cards[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `cards[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `cards[].picture.back.image` | picture id | The picture's id. |
| `cards[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |

## CardCorpus characters

`/v1/schema/characters.schema.json`

Every character on the cards: a named person or group from the Pokémon world, not a Pokémon. A character is on a card as its subject (the Supporter card "Misty", or "Here Comes Team Rocket!") or as the owner of its Pokémon ("Misty's Psyduck"). Misty, Cynthia, N, Lillie and Team Rocket are characters. Each has a file of their cards per catalog, characters/{catalog}/{id}.json.

| Field | Type | Description |
| --- | --- | --- |
| `characterCount` | integer | How many characters; always present, 0 included. |
| `characters` | list of object | Every character, in id order. |
| `characters[].id` | string | The character's id (characters.json). |
| `characters[].name` | string | Their name in English: "Misty", "Team Rocket". |
| `characters[].cardCount` | integer | How many cards they're the subject of or own a Pokémon on, in every catalog; always present, 0 included. |
| `characters[].catalogs`? | list of vocabulary `catalog` | The catalogs they have cards in, each with a file of them (characters/{catalog}/{id}.json); none where they have no cards. |

## CardCorpus Pokémon cards

`/v1/schema/pokemon-cards.schema.json`

Every card of one species in one catalog, oldest first (https://pokemon.cardcorpus.com/v1/docs/guide.md#card-lists): `cards`, every card that shows it in any form but a regional one, and under `forms` each other form's own, a form with none left out. A catalog with no cards of it has no file.

| Field | Type | Description |
| --- | --- | --- |
| `dex` | integer | The species' National Pokédex number. |
| `name` | string | The species' name. |
| `catalog` | vocabulary `catalog` | The catalog whose cards these are. |
| `cards`? | list of pointer → card | Every card that shows it in any form but a regional one, oldest first. |
| `cards[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `cards[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `cards[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `cards[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `cards[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `cards[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `cards[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `cards[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `cards[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `cards[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `cards[].artists[].id` | string | The artist's id (artists.json). |
| `cards[].artists[].name` | string | The artist's name as this card prints it. |
| `cards[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `cards[].species[].id` | integer | The species' National Pokédex number. |
| `cards[].species[].name` | string | A copy of the target's own `name`. |
| `cards[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `cards[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `cards[].species[].forms[].name` | string | The form's name as this card gives it. |
| `cards[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `cards[].picture.front.image` | picture id | The picture's id. |
| `cards[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `cards[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `cards[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `cards[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `cards[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `cards[].picture.back.image` | picture id | The picture's id. |
| `cards[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |
| `forms`? | list of object | Each other form's cards, a form with none left out. |
| `forms[].form` | pointer → form | The form, with its name. |
| `forms[].form.species` | integer | The species' National Pokédex number. |
| `forms[].form.id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). |
| `forms[].form.name` | string | A copy of the target's own `name`. |
| `forms[].cards` | list of pointer → card | Every card that shows the form, oldest first. |
| `forms[].cards[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `forms[].cards[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `forms[].cards[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `forms[].cards[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `forms[].cards[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `forms[].cards[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `forms[].cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `forms[].cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `forms[].cards[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `forms[].cards[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `forms[].cards[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `forms[].cards[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `forms[].cards[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `forms[].cards[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `forms[].cards[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `forms[].cards[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `forms[].cards[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `forms[].cards[].artists[].id` | string | The artist's id (artists.json). |
| `forms[].cards[].artists[].name` | string | The artist's name as this card prints it. |
| `forms[].cards[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `forms[].cards[].species[].id` | integer | The species' National Pokédex number. |
| `forms[].cards[].species[].name` | string | A copy of the target's own `name`. |
| `forms[].cards[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `forms[].cards[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `forms[].cards[].species[].forms[].name` | string | The form's name as this card gives it. |
| `forms[].cards[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `forms[].cards[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `forms[].cards[].picture.front.image` | picture id | The picture's id. |
| `forms[].cards[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `forms[].cards[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `forms[].cards[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `forms[].cards[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `forms[].cards[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `forms[].cards[].picture.back.image` | picture id | The picture's id. |
| `forms[].cards[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `forms[].cards[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |

## CardCorpus character

`/v1/schema/character.schema.json`

One person's cards in one catalog: every card of it they're on, oldest first, each once, with their `roles` on it: `subject` (a Supporter, "Misty"), `owner` (their Pokémon, "Misty's Psyduck") or both (https://pokemon.cardcorpus.com/v1/docs/guide.md#card-lists). A catalog they have no cards in has no file.

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The character's id (characters.json). |
| `name` | string | Their name. |
| `catalog` | vocabulary `catalog` | The catalog whose cards these are. |
| `cards` | list of pointer → card | Every card of the catalog they're on, oldest first, each once. |
| `cards[].id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `cards[].name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `cards[].subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `cards[].cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `cards[].language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `cards[].released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `cards[].released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `cards[].released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `cards[].released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `cards[].credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `cards[].artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `cards[].artists[].id` | string | The artist's id (artists.json). |
| `cards[].artists[].name` | string | The artist's name as this card prints it. |
| `cards[].species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `cards[].species[].id` | integer | The species' National Pokédex number. |
| `cards[].species[].name` | string | A copy of the target's own `name`. |
| `cards[].species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `cards[].species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `cards[].species[].forms[].name` | string | The form's name as this card gives it. |
| `cards[].picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].picture.front`? | object | The front: a picture id, and whose picture it is. |
| `cards[].picture.front.image` | picture id | The picture's id. |
| `cards[].picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `cards[].picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `cards[].picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `cards[].picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `cards[].picture.back`? | object | Its back, where that is unlike the usual one. |
| `cards[].picture.back.image` | picture id | The picture's id. |
| `cards[].picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `cards[].defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |
| `cards[].roles` | list of `subject`, `owner` | What they are on the card: its `subject`, the `owner` of its Pokémon, or both. |

## CardCorpus sets with marketplace ids

`/v1/schema/marketplace-sets.schema.json`

The ids of the sets that have a file in marketplaces/, in order (https://pokemon.cardcorpus.com/v1/docs/guide.md#marketplace-ids).

| Field | Type | Description |
| --- | --- | --- |
| `sets` | list of string | The set ids, in the sets' order (sets/{catalog}.json, catalog by catalog). |
| `hashes` | map of string | Each set's marketplace file's hash: the first 16 hex digits of its text's SHA-256, so a reader reloads only the files that changed. |

## CardCorpus marketplace ids of a set

`/v1/schema/marketplaces.schema.json`

The TCGplayer and Cardmarket products that sell one set's cards: each card by its id with every product its records name, and each of its variants by its id with the one product that prices it, TCGplayer's product and subtype, Cardmarket's product and, with reverseHolo, the price guide's -holo fields (https://pokemon.cardcorpus.com/v1/docs/guide.md#marketplace-ids).

| Field | Type | Description |
| --- | --- | --- |
| `set` | string | The set's id (sets/{catalog}.json). |
| `cards` | map of object | Each card, by its id. |
| `cards.{key}.tcgplayer`? | list of integer | Every TCGplayer product its records name. |
| `cards.{key}.cardmarket`? | list of integer | Every Cardmarket product its records name. |
| `cards.{key}.variants`? | map of object | Each of its variants, by its id, with the one product that prices it. |
| `cards.{key}.variants.{key}.tcgplayer`? | object | TCGplayer's product and subtype. |
| `cards.{key}.variants.{key}.tcgplayer.product` | integer | The TCGplayer product id. |
| `cards.{key}.variants.{key}.tcgplayer.subtype` | string | The price subtype ("Holofoil", "Reverse Holofoil"). |
| `cards.{key}.variants.{key}.cardmarket`? | object | Cardmarket's product. |
| `cards.{key}.variants.{key}.cardmarket.product` | integer | The Cardmarket product id. |
| `cards.{key}.variants.{key}.cardmarket.reverseHolo`? | `true` | Present where the variant is priced by the price guide's -holo fields. |

## CardCorpus card list

`/v1/schema/cards.schema.json`

Every card of one catalog in one slim list (`cards/english.json`, `cards/japanese.json` and so on, one a catalog), so a card can be found, filtered and sorted by name, number, rarity, type or date without opening every set file: its pointer, with copies of its name, number, rarity, card type, language, release date and front picture, in its sets' order (sets/{catalog}.json) and then its set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#card-lists). A set's name is in sets/{catalog}.json, by the pointer's `set`.

| Field | Type | Description |
| --- | --- | --- |
| `catalog` | vocabulary `catalog` | The catalog whose cards these are. |
| `cardCount` | integer | How many cards; always present, 0 included. |
| `cards` | list of pointer → card | Every card of the catalog, in its sets' order (sets/{catalog}.json) and then its set's. |
| `cards[].id` | string | The card's id, unique in its set. |
| `cards[].name` | string | A copy of the target's own `name`. |
| `cards[].englishName`? | string | A copy of the target's own `englishName`. |
| `cards[].subtitle`? | string | A copy of the target's own `subtitle`. The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | A copy of the target's own `englishSubtitle`. The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number`? | object | A copy of the target's own `number`. Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].rarity`? | vocabulary `rarity` | A copy of the target's own `rarity`. Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `cards[].cardType`? | vocabulary `cardType` | A copy of the target's own `cardType`. Pokémon, Trainer or Energy. |
| `cards[].language` | vocabulary `language` | A copy of the target's own `language`. |
| `cards[].released`? | object | A copy of the card's `released`, holding only its `date`: when it first came out, as precisely as is known. Absent where the date isn't known. |
| `cards[].released.date` | date | The date: 1999, 1999-06 or 1999-06-16. |
| `cards[].picture`? | object | A copy of the card's front picture id. |
| `cards[].picture.front` | object | The front. |
| `cards[].picture.front.image` | picture id | The picture's id. |

## CardCorpus lines

`/v1/schema/lines.schema.json`

Every line. A line is one product, or one range of products, that we list as several sets because each part has its own card list and numbering. The 2004 World Championships Decks are a line of four sets, one per deck ("Blaziken Tech", "Magma Spirit", "Rocky Beach", "Team Rushdown"); the EX Trainer Kit is a line of two, its Latias half deck and its Latios half deck. Use a line to show its sets together under one heading: each set carries the line's `name` and its own `part`, the words that tell it from the others. A set names its line in its `line`. Lines come catalog by catalog, in the order their first set comes in sets/{catalog}.json (https://pokemon.cardcorpus.com/v1/docs/guide.md#lines).

| Field | Type | Description |
| --- | --- | --- |
| `lineCount` | integer | How many lines; always present, 0 included. |
| `lines` | list of object | Every line, catalog by catalog, in the order its first set comes in sets/{catalog}.json. |
| `lines[].id` | string | The line's id (lines.json). |
| `lines[].catalog` | vocabulary `catalog` | The catalog its sets are in. |
| `lines[].name` | string | The name its sets share: "2004 World Championships Decks", "EX Trainer Kit". |
| `lines[].sets` | list of pointer → set | The sets that make it up, two or more, in their catalog's order (sets/{catalog}.json). |
| `lines[].sets[].id` | string | The set's id (sets/{catalog}.json). |
| `lines[].sets[].name` | string | A copy of the target's own `name`. |
| `lines[].sets[].englishName`? | string | A copy of the target's own `englishName`. |
| `lines[].sets[].part` | string | The words that tell this set from the line's others: "Blaziken Tech" for "2004 World Championships Deck: Blaziken Tech", "Latias" for "EX Trainer Kit Latias". |

## CardCorpus paintings

`/v1/schema/painting-index.schema.json`

Every painting printed on more than one card. A painting is one piece of artwork: Base Set Charizard and its reprints in every language print the same one. Each entry is a pointer to the painting with its `printCount`, how many cards print it; follow it to paintings/{id}.json for the cards. They come by the set of their first print in the sets' order (sets/{catalog}.json, catalog by catalog), then in the order of those cards in their set's file (https://pokemon.cardcorpus.com/v1/docs/guide.md#card-lists).

| Field | Type | Description |
| --- | --- | --- |
| `paintingCount` | integer | How many paintings printed on more than one card; always present, 0 included. |
| `paintings` | list of pointer → painting | Every such painting. |
| `paintings[].id` | string | The painting's own id, which never changes: its first print's English name and a short code, such as `charizard-k3f9`. Where it was first printed is `firstPrint`'s to say, corrected there when research finds an earlier print. |
| `paintings[].printCount` | integer | How many cards print it. |

## CardCorpus painting

`/v1/schema/painting.schema.json`

One painting, with every print of it in every language and how each links to the others. A painting is one piece of card art, with an id of its own that never changes, its file paintings/{id}.json; its `firstPrint` is the card that first printed it, such as Expansion Pack's No. 006 for the Charizard painting. Its `prints` are keyed by set and then card id, so a card finds its own print as `prints[card.set][card.id]`, and each print's `links` say which print is the same card in each other catalog, what it reprints and its reprints, and its parallels. Every card a link names is in `prints` too, found the same way. `cards` lists the prints card by card: one list for each different card that uses the painting, which is one card with its reprints, its parallels and the same card in other languages, all of which play the same. That Charizard painting has two, the original Base Set Charizard with every reprint of it, and the Evolutions Charizard, which reuses the picture on a card with other HP and attacks. A card's `painting` names this file (https://pokemon.cardcorpus.com/v1/docs/guide.md#links).

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The painting's own id, which never changes: its first print's English name and a short code, such as `charizard-k3f9`. Where it was first printed is `firstPrint`'s to say, corrected there when research finds an earlier print. |
| `firstPrint` | pointer → card | The card that first printed it: its earliest print that reprints nothing, the Japanese one where prints came out the same day. Corrected when research finds an earlier print; the painting's `id` stays the same. |
| `firstPrint.id` | string | The card's id, unique in its set. |
| `printCount` | integer | How many prints carry it, in every language. |
| `cardCount` | integer | How many different cards those prints are: the length of `cards`. |
| `cards` | list of list of pointer → card | Its prints card by card: the card that holds the painting's first print first, then in the order each card first came out. A card is one card with its reprints, its parallels and the same card in other languages, which all play the same; a card that reuses the painting with other game text is another. Most paintings have one. For "other cards with this picture", take the cards that do not hold yours. |
| `cards[][].id` | string | The card's id, unique in its set. |
| `prints` | map of map of pointer → card | Every print in `cards`, by its set's id and then its card id, each with how it links to other cards; and the prints of other paintings those links name, the same card redrawn for another language, which say their own `painting` and are in no list of `cards`. |
| `prints.{key}.{key}.id` | string | The card's id, unique in its set. Treat it as opaque: it never changes once published. |
| `prints.{key}.{key}.name` | string | The card's name as it prints it, in its language: "リザードン" on a Japanese Charizard. Where no source gives the printed name yet, its English name (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `prints.{key}.{key}.englishName`? | string | The card's name in English, on every card, the same as `name` on an English card: "Charizard". What to search and sort by across languages (https://pokemon.cardcorpus.com/v1/docs/guide.md#names). |
| `prints.{key}.{key}.subtitle`? | string | The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `prints.{key}.{key}.englishSubtitle`? | string | The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `prints.{key}.{key}.number`? | object | Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `prints.{key}.{key}.number.printed`? | string | The number as printed, where the card prints one. |
| `prints.{key}.{key}.number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `prints.{key}.{key}.rarity`? | vocabulary `rarity` | Its rarity, as its mark reads (https://pokemon.cardcorpus.com/v1/docs/guide.md#rarity). |
| `prints.{key}.{key}.cardType`? | vocabulary `cardType` | Pokémon, Trainer or Energy. |
| `prints.{key}.{key}.language` | vocabulary `language` | The language its text is printed in: its set's, or another where its product held a foreign print (https://pokemon.cardcorpus.com/v1/docs/guide.md#foreign-prints). Every card has one. |
| `prints.{key}.{key}.released` | object | When the card first came out, in any of its variants: its own date, or, in a set whose cards came out together, the set's (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). |
| `prints.{key}.{key}.released.date`? | date | The date, as precisely as is known: 2001, 2001-09 or 2001-09-22; absent where neither the card's nor the set's is known. |
| `prints.{key}.{key}.released.sort` | string | Sorts cards oldest first, every list alike: compare as plain strings. An ordering convention, not a date; every card has one. |
| `prints.{key}.{key}.released.inferred`? | `true` | `true` where the card's date is an estimate: no page dates the card itself, so its year is borrowed from another language's print or worked out from its neighbours, its copyright line or its product, or it takes its set's date, which is one (https://pokemon.cardcorpus.com/v1/docs/guide.md#release-dates). Absent otherwise. |
| `prints.{key}.{key}.credit`? | string | The credit as printed ("CR CG gangs & K. Hoshiba"). |
| `prints.{key}.{key}.artists`? | list of pointer → artist | The people the credit names, in credit order, each by pointer with the name as this card prints it; a director's credit names no one. |
| `prints.{key}.{key}.artists[].id` | string | The artist's id (artists.json). |
| `prints.{key}.{key}.artists[].name` | string | The artist's name as this card prints it. |
| `prints.{key}.{key}.species`? | list of pointer → species | The species it shows, each once, in the order the card shows them, each with the forms of it the card shows where that is more than its ordinary form. |
| `prints.{key}.{key}.species[].id` | integer | The species' National Pokédex number. |
| `prints.{key}.{key}.species[].name` | string | A copy of the target's own `name`. |
| `prints.{key}.{key}.species[].forms`? | list of object | The forms of the species the card shows, in the card's order, where it shows something other than the species' ordinary form alone (a Gigantamax, a regional form, two forms at once): every form it shows then, the ordinary one included. Usually absent: the card shows the ordinary form alone. One entry on a card of a single other form (a Mega, a Gigantamax, a regional form). Two or more where the card shows several forms of the species, the ordinary one listed with the others where it is among them (Tatsugiri's three: `978`, `978:droopy`, `978:stretchy`). |
| `prints.{key}.{key}.species[].forms[].id` | string | The form's id: the dex number, with ":<key>" for a form other than the default ("52", "52:galar"). With the entry's own `id` as `species` it is a form pointer. |
| `prints.{key}.{key}.species[].forms[].name` | string | The form's name as this card gives it. |
| `prints.{key}.{key}.picture`? | object | The picture to show for the card (https://pokemon.cardcorpus.com/v1/docs/guide.md#pictures): `front.image`, and `front.of`, the variant it is a picture of (its default variant, another variant standing in, or, for a reprint with no picture of any variant, its original's); `back`, where its back is unlike the usual one; `oversized` where the card only came as a jumbo or its picture is a jumbo variant's. |
| `prints.{key}.{key}.picture.front`? | object | The front: a picture id, and whose picture it is. |
| `prints.{key}.{key}.picture.front.image` | picture id | The picture's id. |
| `prints.{key}.{key}.picture.front.of`? | pointer → variant | The variant it is a picture of, where that isn't the variant it is on (on a card, always). |
| `prints.{key}.{key}.picture.front.of.id` | string | The variant's id, unique among its card's variants. |
| `prints.{key}.{key}.picture.front.of.relation`? | `original` | Whose card it is, where not the card's own: `original`, the card a reprint with no picture of its own reprints. Label the picture by it ("the original's picture") without following the card's links. |
| `prints.{key}.{key}.picture.front.watermark`? | string | Whose watermark the picture carries, where it carries one. |
| `prints.{key}.{key}.picture.back`? | object | Its back, where that is unlike the usual one. |
| `prints.{key}.{key}.picture.back.image` | picture id | The picture's id. |
| `prints.{key}.{key}.picture.oversized`? | `true` | Present where the card only came as a jumbo or its picture is a jumbo variant's. |
| `prints.{key}.{key}.defaultVariant` | string | The variant that stands for the card where one must (prices, a tile): by id in `variants`. Whose photo the card's picture is can be another variant's: read `picture.front.of` for that. |
| `prints.{key}.{key}.links`? | object | How it links to other cards: each card a link names is in this file's `prints`. |
| `prints.{key}.{key}.links.counterparts`? | map of pointer → card | The card it is the same card as in each other catalog, by the catalog's id (index.json's `catalogs`), one a catalog, in the catalogs' order: `counterparts.french` is its French print. Each names a print in the same painting file (https://pokemon.cardcorpus.com/v1/docs/guide.md#counterparts). |
| `prints.{key}.{key}.links.counterparts.{key}.id` | string | The card's id, unique in its set. |
| `prints.{key}.{key}.links.counterparts.{key}.printCount` | integer | The card's prints in its language: the card it reprints (or itself), their parallels, and every reprint of those and theirs. |
| `prints.{key}.{key}.links.counterparts.{key}.via`? | `original`, `reprint` | Where the link is borrowed: `original`, from the card this one reprints; `reprint`, from its first print in its set. |
| `prints.{key}.{key}.links.reprintOf`? | pointer → card | The card it reprints: the first print in its language (https://pokemon.cardcorpus.com/v1/docs/guide.md#reprints). |
| `prints.{key}.{key}.links.reprintOf.id` | string | The card's id, unique in its set. |
| `prints.{key}.{key}.links.reprintOf.differs` | list of vocabulary `differs` | What sets the reprint apart from the original; empty when nothing we compare differs, which is itself a fact. |
| `prints.{key}.{key}.links.reprints`? | list of pointer → card | The later prints of it in other sets of its language, oldest first. |
| `prints.{key}.{key}.links.reprints[].id` | string | The card's id, unique in its set. |
| `prints.{key}.{key}.links.reprints[].differs` | list of vocabulary `differs` | What sets the reprint apart from the original; empty when nothing we compare differs, which is itself a fact. |
| `prints.{key}.{key}.links.parallels`? | list of pointer → card | Its other prints in its own set, in number order (https://pokemon.cardcorpus.com/v1/docs/guide.md#parallels). |
| `prints.{key}.{key}.links.parallels[].id` | string | The card's id, unique in its set. |

## CardCorpus play

`/v1/schema/play.schema.json`

The cards that play the same, whatever their picture: one name and one text that works the same way, so an older print may stand in for a newer one in a deck. Base Set's Switch and Scarlet & Violet's are one play though their wording differs; a card that shuffles 3 cards and one that shuffles up to 3 are two. Official errata count: every Potion heals 30, so every Potion is one play. A card's `play` names this file, plays/{id}.json; only plays of more than one card have one. English cards only, for now: a translation sometimes words a card so it plays differently (https://pokemon.cardcorpus.com/v1/docs/guide.md#plays).

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | The play's own id: its cards' English name and a short code, such as `switch-4175`. It stays the same as cards join the play. |
| `name` | string | The name its cards share. |
| `cardCount` | integer | How many cards are in it: the length of `cards`. |
| `errata`? | list of object | The official errata its cards play by, where any do: an erratum to the name rewrites every card of it (every Potion heals 30), one to a printing corrects that printing only. Its cards whose `basis` is `errata` play by them. |
| `errata[].id` | string | The erratum's id, such as `potion`. |
| `errata[].scope` | `name`, `printing`, `rule` | What it corrects: `name`, every card of that name; `printing`, the printing named; `rule`, a kind of card. |
| `errata[].name` | string | The card name it corrects. |
| `errata[].text`? | list of string | The corrected text, one string a paragraph, for an erratum to a name. |
| `errata[].parts`? | object | For an erratum to a printing, the parts it corrects, as the card's printed text names them (`attack1 text`, `ability1 text`, `attack2 cost`, `retreat`, `effect`), with their corrected value. |
| `errata[].change` | string | What it changed, in Pokémon's words. |
| `errata[].listed` | string | When Pokémon's errata page that lists it was last updated, YYYY-MM-DD. |
| `cards` | list of pointer → card | Every card in it, oldest first. |
| `cards[].id` | string | The card's id, unique in its set. |
| `cards[].name` | string | A copy of the target's own `name`. |
| `cards[].englishName`? | string | A copy of the target's own `englishName`. |
| `cards[].subtitle`? | string | A copy of the target's own `subtitle`. The smaller name the card prints beside its own, as it prints it, in its language, where it prints one: "Professeur Chen" on a French Professor's Research, "オーキド博士" on a Japanese one. Where we have no printed subtitle, the English one on a card whose `name` is its English one (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). |
| `cards[].englishSubtitle`? | string | A copy of the target's own `englishSubtitle`. The smaller name the card prints beside its own, in English, where it prints one: the professor on a Professor's Research ("Professor Oak"), the boss on a Boss's Orders ("Ghetsis"). It says which of the cards sharing a name this is, but isn't part of the name: a deck counts them all as one card. Always one of the card's `characters` (https://pokemon.cardcorpus.com/v1/docs/guide.md#subtitles). Our own pages show it joined to the name: "Professor's Research (Professor Oak)". |
| `cards[].number` | object | A copy of the target's own `number`. Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].picture`? | object | A copy of the card's front picture id. |
| `cards[].picture.front` | object | Its front. |
| `cards[].picture.front.image` | picture id | The picture's id. |
| `cards[].basis`? | `identical`, `wording`, `errata`, `unread` | Why it is in the play, from its printed text, read off its picture: `identical`, it prints the play's wording (the one most of its cards print); `wording`, it words it differently in a way that plays the same; `errata`, an official erratum (`errata`) makes it play the same; `unread`, its own text isn't read yet, so it is here by being a reprint or by its sources' text. |

## CardCorpus printed text of a set

`/v1/schema/text.schema.json`

What each card of a set prints, word for word: names, levels, attacks, abilities, trainer and energy text, rule boxes and rule lines, weakness and resistance as printed, and flavor text. English cards are read off their pictures; other languages' text comes from official and other card lists and is not yet checked against the cards (`checked`: `source`). It is the print, not how the card plays now: official errata are in the play files (plays/{id}.json). A card we have no text for is not listed. text/{set}.json, by the set's id (https://pokemon.cardcorpus.com/v1/docs/guide.md#printed-text).

| Field | Type | Description |
| --- | --- | --- |
| `set` | pointer → set | The set. |
| `set.id` | string | The set's id (sets/{catalog}.json). |
| `cardCount` | integer | How many cards are listed: the length of `cards`. |
| `cards` | list of pointer → card | Its cards whose text is read, in the set file's order. |
| `cards[].id` | string | The card's id, unique in its set. |
| `cards[].name` | string | A copy of the target's own `name`. |
| `cards[].number`? | object | A copy of the target's own `number`. Its number as printed ("4/102", "SWSH001"), and that number without its set total. |
| `cards[].number.printed`? | string | The number as printed, where the card prints one. |
| `cards[].number.short` | string | The number without its set total: "4" of "4/102", and the whole number where it prints no total ("SWSH001"). What a tile shows and a sort reads. |
| `cards[].text` | object | What the card prints, as printed (case and punctuation kept, italics dropped). Energy symbols are type names: in costs and `types` as `Lightning`, `Colorless`; inside text in braces, `{Darkness}`. |
| `cards[].text.name`? | string | The name as printed, with its suffix (`ex`, `EX`, `GX`, `V`, `LV.X`, `δ`); empty for a basic Energy that prints only ENERGY and its symbol. |
| `cards[].text.level`? | integer or null | A printed level, such as 42 for LV.42; null where none is printed. |
| `cards[].text.kind`? | string | `pokemon`, `trainer` or `energy`. |
| `cards[].text.subtype`? | string or null | The category of a trainer or energy, in English in every language: `Item`, `Supporter`, `Stadium`, `Pokémon Tool`, `Trainer`, `Basic Energy`, `Special Energy`, … |
| `cards[].text.stage`? | string or null | A Pokémon's stage, in English in every language: `Basic`, `Stage 1`, `Stage 2`, `BREAK`, `VMAX`, … |
| `cards[].text.evolvesFrom`? | string or null | The printed Evolves from name. |
| `cards[].text.hp`? | integer or null | Printed HP. |
| `cards[].text.types`? | list of string | The type symbols printed beside the HP. |
| `cards[].text.markers`? | list of string | Printed labels that are neither text nor stage: `Future`, `Ancient`, `Tera`, `Rapid Strike`, `Prism Star`, `ACE SPEC`, `Team Plasma`, … |
| `cards[].text.abilities`? | list of object | Abilities, Poké-Powers, Poké-Bodies, Pokémon Powers, Ancient Traits and the like, in printed order. |
| `cards[].text.abilities[].label`? | string or null | Its printed label: `Ability`, `Poké-Power`, `Poké-Body`, `Pokémon Power`, `Ancient Trait`, `VSTAR Power`, … |
| `cards[].text.abilities[].name`? | string | Its name. |
| `cards[].text.abilities[].text`? | string | Its text. |
| `cards[].text.attacks`? | list of object | Attacks, in printed order. |
| `cards[].text.attacks[].label`? | string or null | `GX` or `VSTAR` for a GX attack or VSTAR Power attack, else null; the name leaves the logo out. |
| `cards[].text.attacks[].cost`? | list of string | Its Energy cost, one type a symbol; empty for a free attack. |
| `cards[].text.attacks[].name`? | string | Its name. |
| `cards[].text.attacks[].damage`? | string | Damage as printed (`30`, `30+`, `20×`), empty where none. |
| `cards[].text.attacks[].text`? | string | Its text. |
| `cards[].text.effect`? | list of string | A trainer's or energy's text, one string a printed paragraph. |
| `cards[].text.rules`? | list of object | Rule boxes and rule lines as printed (the Pokémon ex rule, You may play only 1 Supporter card during your turn., …). |
| `cards[].text.rules[].label`? | string or null | Its printed label, where it has one. |
| `cards[].text.rules[].text`? | string | Its text. |
| `cards[].text.weakness`? | list of object | Weakness as printed. |
| `cards[].text.weakness[].type`? | string | The type. |
| `cards[].text.weakness[].value`? | string | The value as printed (`×2`, `+20`); empty where the card prints only the type. |
| `cards[].text.resistance`? | list of object | Resistance as printed. |
| `cards[].text.resistance[].type`? | string | The type. |
| `cards[].text.resistance[].value`? | string | The value as printed (`-30`); empty where none is printed. |
| `cards[].text.retreat`? | integer | How many retreat symbols are printed. |
| `cards[].text.flavor`? | string or null | The Pokédex text, where printed. |
| `cards[].checked` | `agreed`, `judged`, `picture`, `source` | How the text is known: `agreed`, a blind read of the picture matches the text other transcriptions give on every part; `judged`, they differed and each difference was settled by a closer look at the picture; `picture`, only the picture read gives it; `source`, a card list gives it and it is not yet checked against the card (languages other than English). |
| `cards[].unsure`? | list of string | Parts a closer look could not settle; `text` keeps the read. |

## CardCorpus prices of a set

`/v1/schema/prices.schema.json`

One set's prices on each marketplace's newest day, by card id and then copy id, the ids of the set's file in sets/. Served by index.json's `prices.latest`, not in this tree; 404 for a set no marketplace sells (https://pokemon.cardcorpus.com/v1/docs/guide.md#prices).

| Field | Type | Description |
| --- | --- | --- |
| `set` | string | The set's id (sets/{catalog}.json). |
| `day` | string | The newest day priced: TCGplayer's, else Cardmarket's. |
| `tcgplayer` | object or null | TCGplayer's newest day here, or null before it has one. |
| `tcgplayer.day` | string | The day its prices are for. |
| `tcgplayer.updated` | string | When TCGplayer says it made them. |
| `tcgplayer.currency` | `"USD"` | The currency of its prices. |
| `cardmarket` | object or null | Cardmarket's newest day here, or null before it has one. |
| `cardmarket.day` | string | The day its prices are for. |
| `cardmarket.createdAt` | string | When Cardmarket says it made them. |
| `cardmarket.currency` | `"EUR"` | The currency of its prices. |
| `cards` | map of map of object | Each card that has a price that day, by its id. |
| `cards.{key}.{key}.tcgplayer`? | object | TCGplayer's prices, in US dollars. |
| `cards.{key}.{key}.tcgplayer.price`? | number | The number to show: the market price. Left out when the product has no recent sales, and then `low`, `mid` and `high` are only what sellers ask. |
| `cards.{key}.{key}.tcgplayer.market`? | number | The market price: what copies recently sold for. Left out with no recent sales. |
| `cards.{key}.{key}.tcgplayer.low`? | number | The lowest listing. |
| `cards.{key}.{key}.tcgplayer.mid`? | number | The middle listing. |
| `cards.{key}.{key}.tcgplayer.high`? | number | The highest listing, which can be anything a seller asks. |
| `cards.{key}.{key}.tcgplayer.directLow`? | number | The lowest TCGplayer Direct listing, when there is one. |
| `cards.{key}.{key}.cardmarket`? | object | Cardmarket's prices, in euros. |
| `cards.{key}.{key}.cardmarket.price`? | number | The number to show: the trend, else the 30-, 7- or 1-day average, else the average of what is listed. |
| `cards.{key}.{key}.cardmarket.trend`? | number | The trend price. |
| `cards.{key}.{key}.cardmarket.avg`? | number | The average of the articles listed. |
| `cards.{key}.{key}.cardmarket.low`? | number | The lowest listing. |
| `cards.{key}.{key}.cardmarket.avg1`? | number | The average sale over 1 day. |
| `cards.{key}.{key}.cardmarket.avg7`? | number | The average sale over 7 days. |
| `cards.{key}.{key}.cardmarket.avg30`? | number | The average sale over 30 days. |
| `summary` | map of map of object | Each card, by its id, with what a page shows without asking for the history. |
| `summary.{key}.{key}.last`? | object | The last `price` the copy had on each marketplace, for a copy with none today. |
| `summary.{key}.{key}.last.tcgplayer`? | object | The last price TCGplayer had for the copy. |
| `summary.{key}.{key}.last.tcgplayer.price` | number | The price, in US dollars. |
| `summary.{key}.{key}.last.tcgplayer.day` | string | The day it was seen. |
| `summary.{key}.{key}.last.cardmarket`? | object | The last price Cardmarket had for the copy. |
| `summary.{key}.{key}.last.cardmarket.price` | number | The price, in euros. |
| `summary.{key}.{key}.last.cardmarket.day` | string | The day it was seen. |
| `summary.{key}.{key}.change30d`? | object | How the copy's price moved over 30 days on each marketplace, as a fraction: 0.045 is up 4.5%, 0 is less than 2% either way. A marketplace is left out when there is too little to compare. |
| `summary.{key}.{key}.change30d.tcgplayer`? | number | The change on TCGplayer. |
| `summary.{key}.{key}.change30d.cardmarket`? | number | The change on Cardmarket. |

## CardCorpus prices of many sets

`/v1/schema/prices-sets.schema.json`

The newest `price` and 30-day change of every copy in 1 to 64 sets, for sorting a list or valuing a collection in few requests. Served by index.json's `prices.sets`, not in this tree; 400 for no set or more than 64 (https://pokemon.cardcorpus.com/v1/docs/guide.md#prices).

| Field | Type | Description |
| --- | --- | --- |
| `tcgplayer` | object or null | TCGplayer's newest day here, or null before it has one. |
| `tcgplayer.day` | string | The day its prices are for. |
| `tcgplayer.updated` | string | When TCGplayer says it made them. |
| `tcgplayer.currency` | `"USD"` | The currency of its prices. |
| `cardmarket` | object or null | Cardmarket's newest day here, or null before it has one. |
| `cardmarket.day` | string | The day its prices are for. |
| `cardmarket.createdAt` | string | When Cardmarket says it made them. |
| `cardmarket.currency` | `"EUR"` | The currency of its prices. |
| `sets` | map of map of map of object | Every set asked for, by its id; empty when nothing in it is priced. |
| `sets.{key}.{key}.{key}.tcgplayer`? | object | TCGplayer's price. |
| `sets.{key}.{key}.{key}.tcgplayer.price` | number | The market price, in US dollars. |
| `sets.{key}.{key}.{key}.cardmarket`? | object | Cardmarket's price. |
| `sets.{key}.{key}.{key}.cardmarket.price` | number | The trend or an average, in euros. |
| `sets.{key}.{key}.{key}.change30d`? | object | How the copy's price moved over 30 days on each marketplace, as a fraction: 0.045 is up 4.5%, 0 is less than 2% either way. A marketplace is left out when there is too little to compare. |
| `sets.{key}.{key}.{key}.change30d.tcgplayer`? | number | The change on TCGplayer. |
| `sets.{key}.{key}.{key}.change30d.cardmarket`? | number | The change on Cardmarket. |

## CardCorpus price history of a card

`/v1/schema/price-history.schema.json`

One card's daily `price` for each of its copies, from its first priced day to the newest. Served by index.json's `prices.history`, not in this tree; 404 for a card with none (https://pokemon.cardcorpus.com/v1/docs/guide.md#prices).

| Field | Type | Description |
| --- | --- | --- |
| `set` | string | The set's id (sets/{catalog}.json). |
| `card` | string | The card's id in its set. |
| `through` | string | The newest day either marketplace has: where the history ends. |
| `currency` | object | The currency of each marketplace's series. |
| `currency.tcgplayer` | `"USD"` | US dollars. |
| `currency.cardmarket` | `"EUR"` | Euros. |
| `days` | list of string | Every day from the card's first priced day through `through`, in order. |
| `copies` | map of object | Each copy that ever had a price, by copy id. |
| `copies.{key}.tcgplayer`? | list of number or null | The copy's `price` on TCGplayer, one entry per day of `days`. |
| `copies.{key}.cardmarket`? | list of number or null | The copy's `price` on Cardmarket, one entry per day of `days`. |
| `change30d` | map of object | How each copy's price moved over the last 30 days, by copy id. |
| `change30d.{key}.tcgplayer`? | number | The change on TCGplayer. |
| `change30d.{key}.cardmarket`? | number | The change on Cardmarket. |

## CardCorpus exchange rates

`/v1/schema/exchange-rates.schema.json`

The newest exchange rates, per euro: a price in currency A is `amount / rates[A] * rates[B]` in currency B. Served by index.json's `prices.rates`, not in this tree (https://pokemon.cardcorpus.com/v1/docs/guide.md#prices).

| Field | Type | Description |
| --- | --- | --- |
| `day` | string | The day the rates are for. |
| `source` | string | Who publishes them. |
| `base` | `"EUR"` | The currency every rate is per one of. |
| `rates` | map of number | Each currency, by its ISO 4217 code, with how many of it one euro buys. |
