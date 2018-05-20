<h3> Data blocks </h3>
<blockquote>
  Note: please note
</blockquote>

Main syntax principle:
- nesting (and parsing) by indentation, similar to all Python blocks;
- start of the suite: "///" -- triple slash;
- first token after "///" defines the type of structure, e.g. "/// t" for tuples;
- whitespace separated elements;
- expressions are put in parentheses

```python
for _ in {1}:
  print ("foo")
```
