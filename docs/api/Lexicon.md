# Lexicon

## Exported
---

#### doctest(modname::Module)
Run code blocks in the docstrings of the specified module `modname` and return
a `Summary` of the results.

Code blocks may be skipped by adding an extra newline at the end of the block.

**Example:**

```julia
doctest(Lexicon)

```


**source:**
[Lexicon/src/doctest.jl:101](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/doctest.jl#L101)

---

#### query(f::Function, sig::(Any..., ))
Search loaded documentation for all methods of a generic function `f` that match
the provided signature `sig`. Optionally, provide an index (1-based) to view an
individual entry if several different ones are found.


**source:**
[Lexicon/src/query.jl:184](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L184)

---

#### query(f::Function, sig::(Any..., ), index)
Search loaded documentation for all methods of a generic function `f` that match
the provided signature `sig`. Optionally, provide an index (1-based) to view an
individual entry if several different ones are found.


**source:**
[Lexicon/src/query.jl:184](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L184)

---

#### save(file::AbstractString, modulename::Module)
Write the documentation stored in `modulename` to the specified `file`
in the format guessed from the file's extension.

If MathJax support is required then the optional keyword argument
`mathjax::Bool` may be given. MathJax uses `\(...\)` for in-line maths
and `\[...\]` or `$$...$$` for display equations.

Currently supported formats: `HTML`, and `markdown`.

**MkDocs**

Beginning with Lexicon 0.1 you can save documentation as pre-formatted
markdown files which can then be post-processed using 3rd-party programs
such as the static site generator [MkDocs](http://www.mkdocs.org).

For details on how to build documentation using MkDocs please consult their
detailed guides and the Docile and Lexicon packages. A more customized build
process can be found in the Sims.jl package.

**Example:**

The documentation for this package was created in the following manner.
All commands are run from the top-level folder in the package.

```julia
save("docs/api/lexicon.md", Lexicon)
run(`mkdocs build`)

```

From the command line, or using `run`, push the `doc/site` directory
to the `gh-pages` branch on the package repository after pushing the
changes to the `master` branch.

```
git add .
git commit -m "documentation changes"
git push origin master
git subtree push --prefix docs/build origin gh-pages

```

If this is the first push to the branch then the site may take some time
to become available. Subsequent updates should appear immediately. Only
the contents of the `doc/site` folder will be pushed to the branch.

The documentation will be available from
`https://USER_NAME.github.io/PACKAGE_NAME/FILE_PATH.html`.



**source:**
[Lexicon/src/render.jl:58](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/render.jl#L58)

---

#### Lexicon.EachEntry
Iterator type for Metadata Entries with sorting options

**Constructors**

```julia
EachEntry(docs::Metadata; order = [:category, :name, :source])

```

**Arguments**

* `docs` : main input

**Optional keyword arguments**

* `order` : indicators of sorting order, given in priority, options include:
  * `:category` - category of Entries
  * `:exported` - whether the item is exported or unexported
  * `:name` - name of Entries
  * `:source` - source location of Entries uses both the file path and
    line number

In addition to symbols, items in `order` can be functions of the form
`(x,y) = ...` where `x` is the documented item, and `y` is the
Entry. The function should return a quantity to be compared when
sorting.

**Main methods**

An iterable, supports `start`, `next`, and `done`. `next` returns a
`(key, value)` pair where the `key` is the ObjectId key, and `value`
is the Entry.

**Example**

```julia
using Lexicon, Docile, Docile.Interface
d = metadata(Docile);

# Collect the source location of each Entry sorted by the default
# (:category then :name then :source).
res = [v.data[:source][2] for (k,v) in EachEntry(d)]
```



**source:**
[Lexicon/src/filtering.jl:131](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/filtering.jl#L131)

---

#### @query(args...)
Create a `Query` object from the provided `args`. The resulting query can then be
`run` to retrieve matching results from currently loaded documentation.

This is a low-level interface. For everyday usage in the REPL rather use the
built-in `?` mode, which Lexicon hooks into automatically.

The first argument must be the expression to search for. Supported expressions
include method calls, macros, constants, types, functions, and strings. An
optional integer argument may be used to only show documentation from one of
several results.

**Example:**

```julia
q = @query Lexicon.@query
run(q)
```

```julia
q = @query Lexicon.save 2
run(q)
```

**Note:** When searching documentation for an operator (`+`, `-`, etc.) it should
be enclosed in parentheses:

```julia
q = @query (+) 4
run(q)
```


**signature:**
query(args...)

**source:**
[Lexicon/src/query.jl:116](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L116)

## Internal
---

#### calculate_score(query, text, object)
Basic text importance scoring.

**source:**
[Lexicon/src/query.jl:207](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L207)

---

#### filter(docs::Docile.Metadata)
Filter Metadata based on categories or file source

**Arguments**

* `docs` : main input

**Optional keyword arguments**

* `categories` : categories to include in the result; can include any
  of [:module, :function, :method, :type, :macro, :global, :comment];
  defaults to all but :comment

* `files` : vector of file names where entries originated to include
  in the result; full pathnames are searched; can include partial
  paths; defaults to all

**Returns**

* `::Metadata` : the filtered result

**Example**

```julia
using Lexicon, Docile, Docile.Interface
d = metadata(Docile);

# Filter entries with categories of :macro and :type
entries( filter(d, categories = [:macro, :type]) )

# Filter entries from the file types.jl
entries( filter(d, files = ["types.jl"]) )
```



**source:**
[Lexicon/src/filtering.jl:39](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/filtering.jl#L39)

---

#### filter(f::Function, docs::Docile.Metadata)
Filter Metadata based on a function

**Arguments**

* `f` : a function that filters Entries and returns a Bool; the
  function signature should be `f(x::Entry)`.
* `docs` : main input

**Returns**

* `::Metadata` : the filtered result

**Example**

```julia
using Lexicon, Docile, Docile.Interface
d = metadata(Docile);

# Filter entries with all categories except :type
res = filter(d) do e
    category(e) != :type
end
```



**source:**
[Lexicon/src/filtering.jl:78](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/filtering.jl#L78)

---

#### Lexicon.Match
An entry and the set of all objects that are linked to it.

**source:**
[Lexicon/src/query.jl:43](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L43)

---

#### Lexicon.Query
Holds the parsed user query.

**Fields:**

* `objects`: The objects that will be searched for in metadata.
* `mods`:    Modules to be searched through for documentation.
* `index`:   1-based, for picking an individual entry to display out of a list.



**source:**
[Lexicon/src/query.jl:23](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L23)

---

#### Lexicon.QueryResults
Stores the matching entries resulting from running a query.

**source:**
[Lexicon/src/query.jl:53](https://github.com/MichaelHatherly/Lexicon.jl/tree/33e504ea950e1fae5889c80264f92d7965e8923d/src/query.jl#L53)


