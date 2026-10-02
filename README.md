# TV Show Records Summary

This program summarizes TVMaze show records by genre and calculates
average ratings by language. These summaries help compare the genres
and language groups represented in the downloaded data.

## Data source

https://api.tvmaze.com/shows?page=0

Each record represents one TV show. The successful initial run returned
240 records. The program downloads only page 0, not the complete catalogue.

## Setup

Create a virtual environment:

```powershell
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

On macOS or Linux, use:

```bash
source .venv/bin/activate
```

Install dependencies:

```text
python -m pip install -r requirements.txt
```

## Run

From the project folder, run:

```text
python records.py
```

The program creates or replaces summary.json in the current directory.

## Example output

An excerpt from the initial successful run:

```json
{
  "source_url": "https://api.tvmaze.com/shows?page=0",
  "records_processed": 240,
  "shows_per_genre": {
    "counts": {
      "Action": 55,
      "Adventure": 23,
      "Anime": 4,
      "Comedy": 66,
      "Crime": 57,
      "Drama": 154
    }
  }
}
```

This excerpt shows that 154 downloaded shows have the Drama genre.
A show can belong to several genres, so genre counts can add up to
more than the number of shows.

The full output also contains average ratings and the number of rated
shows included for each language.

## Data quirks

- Shows can have multiple genres. Each show is counted in every valid
  genre it belongs to.
- Empty or malformed genre values are excluded and the number of shows
  without valid genres is reported.
- Duplicate genre labels within a show are removed using a set.
- Missing or invalid ratings are excluded from averages rather than
  treated as zero. Their count is reported.
- Missing or blank languages are excluded from language averages and
  their count is reported.
- A show missing both language and rating appears in both missing-value
  counters. These counters should not be added to calculate excluded shows.
- Some shows have no network. Network information is not required for
  either aggregation.

## Design choices

- Lists store the downloaded records and ratings for each language.
- Dictionaries group genre counts and ratings by language.
- Sets keep each show's valid genre labels unique.
- Comprehensions build the genre sets and language-average dictionary.
- Separate functions handle downloading, each aggregation, combining
  results, and writing the output.
- Requests uses a timeout and raise_for_status. Download failures produce
  a clear message instead of a traceback.
- pathlib handles the output path, and JSON is written with UTF-8 encoding
  and indent=2.

## Known limitations

- Only the first API page is downloaded, so results do not represent
  the entire TVMaze catalogue.
- API records and ratings may change, so future results may differ.
- Languages with no valid ratings are absent from the averages.
- Genre and language spellings are not standardized beyond removing
  surrounding whitespace.
- The program requires internet access and does not retry failed downloads.