# THIS LIBRARY IS IN DEVELOPMENT AND NOT USABLE AT THE MOMENT

# easymatch

Minimal, layman-readable string pattern matching for python.

## Example:

```python
import easymatch as em

em.match(
    pattern="Invoice*_{year}_{month}_{day}.pdf",
    string="Invoice_RE2321_2021_01_15.pdf")
```

```python
{"year": "2021", "month": "01", "day": "15"}
```

I use it in some applications to provide pattern matching functionality to endusers.
