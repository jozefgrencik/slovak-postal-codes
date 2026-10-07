# slovak-postal-codes

Poštové smerovacie čísla (PSČ) Slovenska s obcami, ulicami, časťami obcí a súradnicami stredu, vo formátoch CSV, JSON a JSONL. Dáta sa denne aktualizujú z registra adries Ministerstva vnútra SR a sú vo verejnej doméne (CC0).

Slovak postal codes (PSČ) by municipality, street and municipality part, with GPS centres. Built from the Slovak address register (Ministry of Interior), refreshed daily, released into the public domain (CC0 1.0).

This repository is generated automatically; do not edit files by hand.

## Contents

All files are in `data/`, each in three formats: `.csv`, `.json`, `.jsonl`.

Common rules:

- UTF-8 without BOM, LF line endings, `,` as CSV separator, English header.
- Sorted deterministically by key (bytewise, not locale-aware), identically in all three formats.
- JSON is an array with exactly one object per line (no pretty-printing), so diffs stay readable. JSONL has one object per line without brackets.
- In JSON/JSONL, codes (`postal_code`, `*_code`) are strings (leading zeros), counts and coordinates are numbers.
- Coordinates are WGS84 with 5 decimal places.
- `*_ascii` columns hold the same name without diacritics (`Žilina` -> `Zilina`), letter case is preserved.
- `district_*` is the district (LAU1, e.g. `SK031B`), `region_*` the region (NUTS3, e.g. `SK031`).

| File | Key | Columns |
|---|---|---|
| `postal_codes` | `postal_code` + `municipality_code` | `postal_code`, `postal_code_formatted`, `municipality_code`, `municipality_name`, `municipality_name_ascii`, `city_name`, `district_code`, `district_name`, `region_code`, `region_name`, `latitude`, `longitude`, `address_points`, `weight`, `applies_to` |
| `streets` | `postal_code` + `municipality_code` + `street_code` | `postal_code`, `municipality_code`, `municipality_name`, `street_code`, `street_name`, `street_name_ascii`, `latitude`, `longitude`, `address_points`, `weight`, `applies_to` |
| `municipality_parts` | `postal_code` + `municipality_code` + `part_code` | `postal_code`, `municipality_code`, `municipality_name`, `part_code`, `part_name`, `part_name_ascii`, `latitude`, `longitude`, `address_points`, `weight` |
| `municipalities` | `municipality_code` | `municipality_code`, `municipality_name`, `municipality_name_ascii`, `municipality_status`, `city_name`, `district_code`, `district_name`, `region_code`, `region_name`, `latitude`, `longitude`, `address_points`, `weight`, `postal_codes` |
| `postal_code_areas` | `postal_code` | `postal_code`, `postal_code_formatted`, `latitude`, `longitude`, `bbox_min_lat`, `bbox_min_lon`, `bbox_max_lat`, `bbox_max_lon`, `municipalities_count`, `address_points`, `weight` |

Notes on columns:

- `postal_code` is five digits (`01001`), `postal_code_formatted` is the display form (`010 01`).
- `municipality_code` is the LAU 2 code from the register (`lau2_id`), e.g. `SK031B517402`: the district code (`SK031B`, LAU 1) followed by the six-digit municipality code of the Statistical Office (`517402`). The six-digit code alone is unique across Slovakia, and OpenStreetMap uses it in the `ref` tag of municipality boundaries, so it can join the two. In Bratislava and Košice it identifies a city district (`Bratislava-Nové Mesto`, `Košice-Džungľa`).
- `city_name` is `Bratislava` for districts SK0101-SK0105 and `Košice` for SK0422-SK0425, otherwise empty. It groups the city districts of the two cities.
- `municipality_status` (only in `municipalities`) is one of `city`, `municipality`, `city_district`, `military_district`, or empty if the municipality is missing from the register snapshot.
- `postal_codes` in `municipalities` is a `|`-separated list in CSV (`01001|01003`) and an array in JSON/JSONL.
- A street with several postal codes has one row per postal code.
- `streets` contains only addresses with a street (municipalities without streets have none). `street_code` is the register's street id, so two different streets with the same name in one municipality stay separate.
- `municipality_parts` contains only parts whose name differs from the municipality name.
- `applies_to` says which house numbers or parts of the municipality the postal code of the row applies to. Rows it is not used for are empty.
  - In `streets` it is filled for streets with more than one postal code and uses the orientation number (`orientačné číslo`).
  - In `postal_codes` it is filled only for municipalities without streets that have more than one postal code and uses the registration number (`súpisné číslo`); for municipalities with streets see `streets`. Where the same registration number has different postal codes in different parts of the municipality, each part is listed by name: `Sása` for the whole part, `Masníkovo (1-36, 38-49)` for numbers in it.
  - Comma-separated ranges and numbers: `9-33 odd` and `2-14 even` cover the odd or even numbers in the range, `5-11` all numbers in it, `12` and `12A` single numbers.
  - A number covers its variants with a letter (`5` covers `5A`) unless a variant is listed with another postal code.
  - An orientation number used by buildings with different postal codes is written with the registration number, like the post office does: `1543/55`.
  - `other` means all numbers not listed in the other rows of the same street or municipality.

### Sample rows

`postal_codes`

| postal_code | postal_code_formatted | municipality_code | municipality_name | municipality_name_ascii | city_name | district_code | district_name | region_code | region_name | latitude | longitude | address_points | weight | applies_to |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 01001 | 010 01 | SK031B517402 | Žilina | Zilina |  | SK031B | Žilina | SK031 | Žilinský | 49.21630 | 18.74316 | 6383 | 12766 |  |
| 81101 | 811 01 | SK0101528595 | Bratislava-Staré Mesto | Bratislava-Stare Mesto | Bratislava | SK0101 | Bratislava I | SK010 | Bratislavský | 48.14459 | 17.10187 | 534 | 1068 |  |
| 07205 | 072 05 | SK0427522295 | Bánovce nad Ondavou | Banovce nad Ondavou |  | SK0427 | Michalovce | SK042 | Košický | 48.67970 | 21.81834 | 14 | 14 | 21, 78, 109, 152, 173, 189, 236, 241, 243, 248, 249, 273, 275, 283 |

`streets`

| postal_code | municipality_code | municipality_name | street_code | street_name | street_name_ascii | latitude | longitude | address_points | weight | applies_to |
|---|---|---|---|---|---|---|---|---|---|---|
| 01001 | SK031B517402 | Žilina | 53783 | Mariánske námestie | Marianske namestie | 49.22385 | 18.73907 | 29 | 58 |  |
| 81105 | SK0101528595 | Bratislava-Staré Mesto | 39122 | Štefánikova | Stefanikova | 48.15196 | 17.10651 | 22 | 44 | 2-14 even, 9-33 odd |
| 81106 | SK0101528595 | Bratislava-Staré Mesto | 39122 | Štefánikova | Stefanikova | 48.14924 | 17.10679 | 4 | 8 | 1-7 odd |

`municipality_parts`

| postal_code | municipality_code | municipality_name | part_code | part_name | part_name_ascii | latitude | longitude | address_points | weight |
|---|---|---|---|---|---|---|---|---|---|
| 01001 | SK031B517402 | Žilina | 401810 | Bytčica | Bytcica | 49.19334 | 18.72710 | 63 | 126 |
| 01001 | SK031B517402 | Žilina | 406284 | Mojšova Lúčka | Mojsova Lucka | 49.19448 | 18.81053 | 210 | 420 |
| 01001 | SK031B517402 | Žilina | 409133 | Strážov | Strazov | 49.23338 | 18.70723 | 268 | 536 |

`municipalities`

| municipality_code | municipality_name | municipality_name_ascii | municipality_status | city_name | district_code | district_name | region_code | region_name | latitude | longitude | address_points | weight | postal_codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SK031B517402 | Žilina | Zilina | city |  | SK031B | Žilina | SK031 | Žilinský | 49.21720 | 18.74134 | 13260 | 26520 | 01001\|01003\|01004\|01007\|01008\|01009\|01014\|01015\|02401 |
| SK0101528595 | Bratislava-Staré Mesto | Bratislava-Stare Mesto | city_district | Bratislava | SK0101 | Bratislava I | SK010 | Bratislavský | 48.15144 | 17.09946 | 7599 | 15198 | 81101\|81102\|81103\|81104\|81105\|81106\|81107\|81108\|81109\|82108\|82109\|84107 |
| SK032C517348 | Veľké Pole | Velke Pole | municipality |  | SK032C | Žarnovica | SK032 | Banskobystrický | 48.54498 | 18.56057 | 311 | 311 | 96674\|96681 |

`postal_code_areas`

| postal_code | postal_code_formatted | latitude | longitude | bbox_min_lat | bbox_min_lon | bbox_max_lat | bbox_max_lon | municipalities_count | address_points | weight |
|---|---|---|---|---|---|---|---|---|---|---|
| 01001 | 010 01 | 49.21047 | 18.74981 | 49.17007 | 18.67364 | 49.25519 | 18.83514 | 2 | 6912 | 13295 |
| 81101 | 811 01 | 48.14459 | 17.10187 | 48.14114 | 17.08777 | 48.14641 | 17.11269 | 1 | 534 | 1068 |
| 96681 | 966 81 | 48.48498 | 18.70813 | 48.43924 | 18.57702 | 48.56802 | 18.77407 | 7 | 2715 | 4907 |

## How the values are computed

- **Centre** (`latitude`, `longitude`): a weighted median of the address points of the group, then the mean of points within 2.5x the median distance (at least 300 m), dropping distant points only while they carry less than 20 % of the weight. All addresses have the same weight. The result is snapped to the nearest address point of the group, so the pin never lands in a field or river between two settlements. Addresses without coordinates are ignored; a group with no coordinates inherits the centre of its parent (street or part -> postal code x municipality -> municipality). Coordinates stay unchanged between releases unless the centre moved by 10 m or more.
- **`address_points`**: number of address points of the group. An address point is a building with a house number, not a flat.
- **`weight`**: an estimate for sorting results (e.g. autocomplete), not a population count. `weight = address_points x K`, where K is 2 for cities and city districts (`municipality_status` = `city` or `city_district`) and 1 otherwise; K is currently 2. It is rounded to a whole number. Cities have more inhabitants per address than villages, which is what K approximates.
- **`postal_code_areas`**: a summary per postal code; the bounding box covers the address points that passed the outlier filter.

## Data quality notes

- Small pairs of postal code x municipality (1-8 addresses) are real: marginal streets or houses served by a neighbouring post office. Nothing is filtered out; such pairs have a low `weight`.
- Bratislava and Košice are split into city districts; use `city_name` to group them.
- About 460 address points have no postal code and are skipped; about 2 % have no coordinates (counted in `address_points`, not used for centres and bounding boxes).
- Only territorial postal codes are included: no P.O. boxes and no company codes.
- Military districts (`military_district`, e.g. Záhorie, Lešť) appear with the few addresses they have.

## Installation

Composer (the files end up in `vendor/jozefgrencik/slovak-postal-codes/data/`):

```
composer require jozefgrencik/slovak-postal-codes
```

Git:

```
git clone https://github.com/jozefgrencik/slovak-postal-codes.git
```

jsDelivr (version range `@0.1` follows all releases of the current format):

```
https://cdn.jsdelivr.net/gh/jozefgrencik/slovak-postal-codes@0.1/data/postal_codes.csv
```

## Updates and versions

The data is rebuilt every day. A new release is published only when the output changed, so there are no releases on days without changes in the source.

Versions have the form `MAJOR.MINOR.YYYYMMDD`, for example `0.1.20261003`:

- `MAJOR` changes when a column is renamed or removed, or the format changes.
- `MINOR` changes when a column or file is added, or a calculation is corrected (including a change of the `weight` constant K).
- `YYYYMMDD` is the date of the data (Europe/Bratislava). There is at most one release per day for a given `MAJOR.MINOR`.

## Notifications about new versions

- PHP projects: enable Dependabot or Renovate for Composer. Because releases are frequent, a weekly schedule is recommended (`schedule: weekly` in Dependabot, `schedule` in Renovate).
- Everyone else: subscribe to the RSS feed of tags, `https://github.com/jozefgrencik/slovak-postal-codes/tags.atom`.

## Sources

- Register adries (address register), Ministry of Interior of the Slovak Republic, CC0
- Register obcí (municipality register), Ministry of Interior of the Slovak Republic, CC0

## License

[CC0 1.0 Universal](LICENSE). The data comes only from CC0 sources.
