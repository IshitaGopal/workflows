# CSV

## Reading a CSV

```python
import csv

with open("data.csv", newline="") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)  # each row is a list of strings
```

> Always pass `newline=""` when opening files for `csv` — it prevents extra
> blank lines being inserted on Windows.

## Reading with headers — DictReader

```python
with open("data.csv", newline="") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], row["age"])  # each row is an OrderedDict/dict
```

```python
reader.fieldnames  # list of column headers (read after iterating starts)
```

## Writing a CSV

```python
with open("out.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "age"])       # single row
    writer.writerows([                     # multiple rows
        ["Alice", 30],
        ["Bob", 25],
    ])
```

## Writing with headers — DictWriter

```python
fieldnames = ["name", "age"]

with open("out.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerow({"name": "Alice", "age": 30})
    writer.writerows([
        {"name": "Bob", "age": 25},
        {"name": "Cara", "age": 28},
    ])
```

## Dialects & delimiters

```python
# Custom delimiter (e.g. tab-separated)
reader = csv.reader(f, delimiter="\t")

# Quoting
writer = csv.writer(f, quoting=csv.QUOTE_MINIMAL)  # default
# other options: QUOTE_ALL, QUOTE_NONNUMERIC, QUOTE_NONE
```

Common `csv.reader`/`csv.writer` parameters:

- `delimiter` — field separator (default `,`)
- `quotechar` — character used to quote fields (default `"`)
- `quoting` — quoting behavior
- `skipinitialspace` — ignore whitespace after the delimiter

## Sniffing an unknown format

```python
with open("data.csv", newline="") as f:
    sample = f.read(2048)
    f.seek(0)
    dialect = csv.Sniffer().sniff(sample)
    reader = csv.reader(f, dialect)
```

## When NOT to use `csv`

For anything beyond simple read/write, reach for **pandas** instead:

- Filtering, grouping, joining, or transforming data
- Handling large files efficiently
- Type inference / parsing dates, numbers, etc. automatically
- Exporting to other formats (Excel, JSON, SQL, etc.)
