<img width="500" src="https://raw.githubusercontent.com/tfeldmann/simplematch/main/docs/simplematch.svg" alt="logo">

# simplematch

> Minimal, super readable string pattern matching for python.

[![PyPI Version][pypi-image]][pypi-url]
![PyPI - License](https://img.shields.io/pypi/l/simplematch)
[![tests](https://github.com/tfeldmann/simplematch/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/tfeldmann/simplematch/actions/workflows/tests.yml)

```python
import simplematch

simplematch.match("He* {planet}!", "Hello World!")
>>> {"planet": "World"}

simplematch.match("It* {temp:float}°C *", "It's -10.2°C outside!")
>>> {"temp": -10.2}
```

## Installation

`pip install simplematch`

(Or just drop the `simplematch.py` file in your project.)

## Syntax

`simplematch` has only two syntax elements:

- wildcard `*`
- capture group `{name}`

Capture groups can be named (`{name}`), unnamed (`{}`) and typed (`{name:float}`).

The following types are available:

- `int`
- `float`
- `email`
- `url`
- `ipv4`
- `ipv6`
- `bitcoin`
- `ssn` (social security number)
- `ccard` (matches Visa, MasterCard, American Express, Diners Club, Discover, JCB)

For now, only named capture groups can be typed.

Then use one of these functions:

```python
import simplematch

simplematch.match(pattern, string) # -> returns a `dict` on match, `None` otherwise.
simplematch.test(pattern, string)  # -> returns `True` on match, `False` otherwise.
```

Or use a `Matcher` object:

```python
import simplematch as sm

matcher = sm.Matcher(pattern)

matcher.match(string) # -> returns a dict or None
matcher.test(string)  # -> returns True / False
matcher.regex         # -> shows the generated regex
```

## Basic usage

```python
import simplematch as sm

# extracting data
sm.match(
    pattern="Invoice_*_{year}_{month}_{day}.pdf",
    string="Invoice_RE2321_2021_01_15.pdf")
>>> {"year": "2021", "month": "01", "day": "15"}

# test match only
sm.test("ABC-{value:int}", "ABC-13")
>>> True
```

## Typed matches

```python
import simplematch as sm

matcher = sm.Matcher("{year:int}-{month:int}: {value:float}")

# extracting data
matcher.match("2021-01: -12.786")
>>> {"year": 2021, "month": 1, "value": -12.786}

# month is no integer -> no match and return `None`.
matcher.match("2021-AB: Hello")
>>> None

# no extraction, only test for match
matcher.test("1234-01: 123.123")
>>> True

# show generated regular expression
matcher.regex
>>> '^(?P<year>[+-]?[0-9]+)\\-(?P<month>[+-]?[0-9]+):\\ (?P<value>[+-]?(?:[0-9]*[.])?[0-9]+)$'

# show registered converters
matcher.converters
>>> {'year': <class 'int'>, 'month': <class 'int'>, 'value': <class 'float'>}
```

## Register your own types

You can register your own types to be available for the `{name:type}` matching syntax
with the `register_type` function.

`simplematch.register_type(name, regex, converter=str)`

- `name` is the name to use in the matching syntax
- `regex` is a regular expression to match your type
- `converter` is a callable to convert a match (`str` by default)

### Example

Register a `smiley` type to detect smileys (`:)`, `:(`, `:/`) and getting their moods:

```python
import simplematch as sm

def mood_convert(smiley):
    moods = {
        ":)": "good",
        ":(": "bad",
        ":/": "sceptic",
    }
    return moods.get(smiley, "unknown")

sm.register_type("smiley", r":[\)\(\/]", mood_convert)

sm.match("I'm feeling {mood:smiley} *", "I'm feeling :) today!")
>>> {"mood": "good"}
```

## CLI Command

You can also install `simplematch` for use as a CLI command e.g. using `pipx`.

```sh
pipx install simplematch
```

### Usage

```sh
usage: simplematch [-h] [--regex] pattern [strings ...]

positional arguments:
  pattern     A matching pattern
  strings     The string to match

options:
  -h, --help  show this help message and exit
  --regex     Show the generated regular expression
```

### Example

Extract a date from a specific file name:

```sh
simplematch "Invoice_*_{year}_{month}_{day}.pdf" "Invoice_RE2321_2021_01_15.pdf"
>>> {"year": "2021", "month": "01", "day": "15"}
```

## Background

`simplematch` aims to fill a gap between parsing with `str.split()` and regular
expressions. It should be as simple as possible, fast and stable.

The `simplematch` syntax is transpiled to regular expressions under the hood, so
matching performance should be just as good.

I hope you get some good use out of this!

## Contributions

Contributions are welcome! Just submit a PR and maybe get in touch with me via email
before big changes.

## License

[MIT](https://choosealicense.com/licenses/mit/)

<!-- Badges -->

[pypi-image]: https://img.shields.io/pypi/v/simplematch
[pypi-url]: https://pypi.org/project/simplematch/
