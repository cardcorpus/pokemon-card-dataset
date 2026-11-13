# CardCorpus dataset

An open dataset of Pokémon Trading Card Game cards in English, Japanese and
Simplified Chinese: every set, every card, its printings and variants, and
the links between them (the same card in another language, reprints,
shared paintings, Pokémon and characters, artists, distributions).

This snapshot was published on **2026-11-12**. It holds:

- 916 sets
- 67,243 cards
- 106,101 variants (holo, reverse holo, stamps and the like)
- 44,702 links between the same card in different languages
- 19,390 paintings shared across cards
- 1,604 promo distributions
- 1,025 Pokémon species
- 428 characters
- 494 artists

## Getting it

- **Browse** the files here, under [`v1/`](v1/).
- **Download everything** at once from the
  [latest release](../../releases/latest): `cardcorpus-dataset-<date>.zip`
  (37 MB, 460 MB unpacked) with a `SHA256SUMS` file.
- **Query the live copy** at
  [cardcorpus.com/v1/index.json](https://cardcorpus.com/v1/index.json).
  It has the same layout and is updated more often than this repository.

## Layout

Start at [`v1/index.json`](v1/index.json). Its `files` list names every
file with its JSON Schema (in [`v1/schema/`](v1/schema/)), and `pointers`
explains how a reference to a set, card or copy finds its file.

| file | what it holds |
|---|---|
| `v1/sets.json`, `v1/sets/<catalog>/<set>.json` | every set, then each set's cards, printings and variants |
| `v1/cards.json` | a compact list of every card |
| `v1/pokemon.json`, `v1/pokemon-cards/`, `v1/pokemon-families/` | Pokémon, their forms and evolutions, and their cards |
| `v1/characters.json`, `v1/characters/` | trainers and other characters, and their cards |
| `v1/artists.json`, `v1/artists/` | artists and their cards |
| `v1/paintings.json`, `v1/paintings/` | cards that share a painting |
| `v1/distributions.json`, `v1/distributions/`, `v1/distribution-series/` | how promos and stamped cards were given out |
| `v1/collections.json` | curated collections |
| `v1/marketplaces.json`, `v1/marketplaces/` | TCGplayer and Cardmarket product ids |
| `v1/moved.json` | every earlier address that no longer resolves, and where it went |

Pictures are not included. Each picture is an id, and `v1/index.json`'s
`pictures` turns it into an image url.

## Reporting a mistake

If something looks wrong, such as a missing card, a wrong date or a link
between two cards that aren't the same, please
[open an issue](../../issues/new/choose) and link a page or picture that
shows it. Reports are read in batches. A fix is researched, made upstream
and shows up here in the next snapshot.

The files here are generated, so pull requests that change them can't be
merged; an issue is the way to suggest a change.

## Related

[cardcorpus/pokemon-card-scanner](https://github.com/cardcorpus/pokemon-card-scanner)
recognizes cards in photos, down to the exact print, and names them with this
dataset's ids.

## Licence

The data is licensed under [CC BY 4.0](LICENSE). Credit "CardCorpus" with a
link to this repository or cardcorpus.com. See [NOTICE](NOTICE) for the
trademarks and copyrights of others that the licence doesn't cover.
