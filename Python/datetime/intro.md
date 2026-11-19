# Datetime Notes

## The Main Types

```python
from datetime import date, datetime, time, timedelta, timezone
```

| Type        | Holds                      | Example                              |
| ----------- | -------------------------- | ------------------------------------ |
| `date`      | year, month, day           | `date(2026, 11, 19)`                 |
| `time`      | hour, minute, second, µs   | `time(14, 30)`                       |
| `datetime`  | date + time (+ tz)         | `datetime(2026, 11, 19, 14, 30)`     |
| `timedelta` | a duration                 | `timedelta(days=7, hours=2)`         |
| `timezone`  | a fixed UTC offset         | `timezone.utc`                       |

> The module and the class share a name. `import datetime` then
> `datetime.datetime.now()`. Or `from datetime import datetime` then
> `datetime.now()`. Mixing the two styles is the most common mistake.

## Getting Today's Date / Now

```python
date.today()                  # date(2026, 11, 19)  — local date
datetime.now()                # local date + time, *naive* (no timezone)
datetime.now(timezone.utc)    # current UTC time, *aware* — prefer this
datetime.now().date()         # just the date part
datetime.now().time()         # just the time part
```

> `datetime.utcnow()` is **deprecated** since Python 3.12 — use
> `datetime.now(timezone.utc)` instead.

## str → date → str (the round trip)

Two methods, easy to mix up:

- **`strptime`** — string **p**arse time: `str` → `datetime`
- **`strftime`** — string **f**ormat time: `datetime` → `str`

```python
# str -> datetime
dt = datetime.strptime("19/11/2026", "%d/%m/%Y")   # datetime(2026, 11, 19, 0, 0)

# datetime -> date (drop the time)
d = dt.date()                                      # date(2026, 11, 19)

# date -> str
d.strftime("%Y-%m-%d")        # '2026-11-19'
d.strftime("%d %B %Y")        # '19 November 2026'
d.strftime("%a, %d %b")       # 'Thu, 19 Nov'

# f-strings accept the same format codes
f"{d:%d.%m.%Y}"               # '19.11.2026'
```

> There is no `date.strptime` — parse with `datetime.strptime(...)` and then
> call `.date()`.

### ISO format — the easy path

If the string is ISO 8601 (`YYYY-MM-DD...`), skip format codes entirely:

```python
date.fromisoformat("2026-11-19")                    # date(2026, 11, 19)
datetime.fromisoformat("2026-11-19T14:30:00")       # datetime(2026, 11, 19, 14, 30)
datetime.fromisoformat("2026-11-19T14:30:00+01:00") # aware datetime (3.11+)

d.isoformat()                                       # '2026-11-19'
datetime(2026, 11, 19, 14, 30).isoformat()          # '2026-11-19T14:30:00'
```

> Use ISO format whenever you control the format (filenames, APIs, CSVs).
> It sorts correctly as plain text and is unambiguous.

### Common Format Codes

| Code | Meaning                 | Example    |
| ---- | ----------------------- | ---------- |
| `%Y` | 4-digit year            | `2026`     |
| `%y` | 2-digit year            | `26`       |
| `%m` | month, zero-padded      | `11`       |
| `%B` | full month name         | `November` |
| `%b` | short month name        | `Nov`      |
| `%d` | day, zero-padded        | `19`       |
| `%A` | full weekday            | `Thursday` |
| `%a` | short weekday           | `Thu`      |
| `%H` | hour 00–23              | `14`       |
| `%I` | hour 01–12              | `02`       |
| `%p` | AM/PM                   | `PM`       |
| `%M` | minute                  | `30`       |
| `%S` | second                  | `05`       |
| `%z` | UTC offset              | `+0100`    |
| `%j` | day of year             | `323`      |

Full list: <https://strftime.org>

## Parsing Dates

```python
datetime.strptime("2026-11-19 14:30", "%Y-%m-%d %H:%M")
datetime.strptime("Nov 19, 2026", "%b %d, %Y")
datetime.strptime("19.11.26", "%d.%m.%y")
datetime.strptime("2:30 PM", "%I:%M %p").time()    # time(14, 30)
```

The format must match the string **exactly**, or you get a `ValueError`:

```python
try:
    datetime.strptime("2026/11/19", "%Y-%m-%d")
except ValueError as e:
    print(e)  # time data '2026/11/19' does not match format '%Y-%m-%d'
```

> **Ambiguity trap:** `"03/04/2026"` is 3 April in Europe but March 4 in the
> US. Always know which convention your source uses — never guess.

For messy or unknown formats, `dateutil` (installed with pandas) guesses:

```python
from dateutil import parser
parser.parse("19th Nov 2026 2:30pm")   # datetime(2026, 11, 19, 14, 30)
parser.parse("03/04/2026", dayfirst=True)  # 3 April
```

## Date Arithmetic

```python
today = date.today()

today + timedelta(days=7)        # one week from today
today - timedelta(weeks=2)       # two weeks ago

delta = date(2026, 12, 25) - today   # date - date -> timedelta
delta.days                            # e.g. 36

timedelta(hours=1, minutes=30).total_seconds()   # 5400.0
```

> `timedelta` has no `months` or `years` — months vary in length.
> Use `dateutil.relativedelta` for that:

```python
from dateutil.relativedelta import relativedelta
date(2026, 1, 31) + relativedelta(months=1)   # date(2026, 2, 28) — clamps
```

### Comparing, Replacing, Components

```python
d1 < d2                          # dates compare naturally
d.year, d.month, d.day
d.weekday()                      # Monday=0 ... Sunday=6
d.isoweekday()                   # Monday=1 ... Sunday=7
d.isocalendar().week             # ISO week number
d.replace(day=1)                 # first of the month
```

## Timezones

```python
from zoneinfo import ZoneInfo     # standard library since 3.9

berlin = datetime(2026, 11, 19, 14, 30, tzinfo=ZoneInfo("Europe/Berlin"))
berlin.astimezone(ZoneInfo("Asia/Kolkata"))   # same instant, other zone
berlin.astimezone(timezone.utc)
```

- **Naive** datetime: no `tzinfo`. Python doesn't know which timezone it is.
- **Aware** datetime: has `tzinfo`.
- You **cannot** compare or subtract naive and aware datetimes — `TypeError`.

> Rule of thumb: store and compute in UTC, convert to local time only for
> display.

## Unix Timestamps

```python
datetime.now(timezone.utc).timestamp()               # float seconds since 1970
datetime.fromtimestamp(1_700_000_000, tz=timezone.utc)
```

---

# Dates in Pandas

Pandas has its own types built on NumPy:

| Python      | Pandas                         | Column dtype        |
| ----------- | ------------------------------ | ------------------- |
| `datetime`  | `pd.Timestamp`                 | `datetime64[us]`    |
| `timedelta` | `pd.Timedelta`                 | `timedelta64[us]`   |
| —           | `pd.Period` (e.g. a month)     | `period[M]`         |

> The unit in brackets (`ns`, `us`, `s`) is the storage resolution. Pandas 2
> always used `ns`; pandas 3 picks a unit from the input (`us` when parsing
> strings). It rarely matters — just don't be surprised by either.

```python
import pandas as pd

pd.Timestamp.now()               # now
pd.Timestamp.today().normalize() # today at midnight
pd.Timestamp("2026-11-19")
```

## Parsing: str column → datetime column

```python
df = pd.DataFrame({"date": ["2026-11-19", "2026-11-20", "2026-11-21"],
                   "sales": [100, 150, 120]})

df["date"].dtype                    # str (pandas 3) / object (pandas 2) — strings!
df["date"] = pd.to_datetime(df["date"])
df["date"].dtype                    # datetime64[us]
```

```python
# Specify the format — faster and avoids wrong guesses
pd.to_datetime(df["date"], format="%d/%m/%Y")

# European day-first strings without a fixed format
pd.to_datetime(df["date"], dayfirst=True)

# Bad values become NaT (Not a Time) instead of raising
pd.to_datetime(df["date"], errors="coerce")

# Unix timestamps
pd.to_datetime(df["ts"], unit="s")
```

### Parse while reading the CSV

```python
df = pd.read_csv("sales.csv", parse_dates=["date"])

# with a non-ISO format
df = pd.read_csv("sales.csv", parse_dates=["date"], date_format="%d/%m/%Y")
```

> If a date column shows `str` or `object` dtype after loading, it's still strings —
> none of the `.dt` methods will work until you `pd.to_datetime` it.

## The `.dt` Accessor

On a datetime column, `.dt` exposes the same components as Python dates:

```python
df["date"].dt.year
df["date"].dt.month
df["date"].dt.day
df["date"].dt.day_name()        # 'Thursday'
df["date"].dt.month_name()      # 'November'
df["date"].dt.weekday           # Monday=0
df["date"].dt.isocalendar().week
df["date"].dt.quarter
df["date"].dt.date              # plain Python date objects
df["date"].dt.normalize()       # drop the time, keep datetime64 dtype
```

## datetime column → str column

```python
df["date"].dt.strftime("%d %b %Y")   # '19 Nov 2026'
df["date"].dt.strftime("%Y-%m")      # '2026-11'
```

> This turns the column back into strings. Do it last, only for display
> or export — you lose sorting, filtering and arithmetic.

## Filtering by Date

```python
df[df["date"] >= "2026-11-20"]                   # strings are converted for you
df[df["date"].between("2026-11-01", "2026-11-30")]
df[df["date"].dt.month == 11]

# With a DatetimeIndex you can slice with partial strings
s = df.set_index("date")
s.loc["2026-11"]                  # whole month
s.loc["2026-11-19":"2026-11-20"]  # range (inclusive both ends)
```

## Date Arithmetic in Pandas

```python
df["date"] + pd.Timedelta(days=7)
df["date"] + pd.DateOffset(months=1)        # calendar-aware months
(df["date"] - df["date"].min()).dt.days      # days since first date

pd.Timestamp.today().normalize() - df["date"]   # age of each row
```

## Generating Date Ranges

```python
pd.date_range("2026-11-01", periods=5, freq="D")        # 5 days
pd.date_range("2026-01-01", "2026-12-31", freq="MS")    # month starts
pd.date_range("2026-01-01", periods=4, freq="W-MON")    # Mondays
pd.bdate_range("2026-11-16", "2026-11-20")               # business days
```

Common frequency aliases: `D` day, `B` business day, `W` week,
`MS`/`ME` month start/end, `QS`/`QE` quarter, `YS`/`YE` year, `h` hour.

## Grouping by Time — `resample`

```python
s = df.set_index("date")

s["sales"].resample("W").sum()     # weekly totals
s["sales"].resample("ME").mean()   # monthly averages

# without setting the index
df.groupby(df["date"].dt.to_period("M"))["sales"].sum()
```

## Timezones in Pandas

```python
df["date"].dt.tz_localize("Europe/Berlin")   # naive -> aware (attach a tz)
df["date"].dt.tz_convert("UTC")              # aware -> another tz
```

> `tz_localize` says "these times *are* in Berlin". `tz_convert` says
> "show this instant in another zone". Use `tz_convert` only on columns
> that already have a timezone.

## Gotchas

- `datetime` module vs `datetime` class — see the top of this note.
- `strptime` parses, `strftime` formats.
- `%m` is month, `%M` is minute.
- Day-first vs month-first strings — always specify `format=` or `dayfirst=`.
- Naive and aware datetimes can't be mixed.
- Store as ISO / UTC, convert for display.
- In pandas, check `df.dtypes` — `str`/`object` means the dates are still strings.
