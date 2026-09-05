# Calibre save-to-disk sorter

A [Calibre](https://calibre-ebook.com/) [save template](https://manual.calibre-ebook.com/template_lang.html) that exports books into a genre-first folder tree. Authors with a single standalone book are stored flat under the genre. Everyone else gets an author folder, and series get their own subfolder.

The template is in [`save.template`](save.template).

## What it does

Calibre’s default save layout is `Author/Title`. This template instead:

1. Groups everything by a custom **Genre** column (`#genre`).
2. Skips the author folder when that author has **exactly one book** in the library and **no series**.
3. Otherwise uses `author_sort` as a folder, and adds a series folder when the book is in a series.
4. Names the file `{title} - {authors}`.

| Situation | Path |
| --- | --- |
| Author has one standalone book | `{genre}/{title} - {authors}` |
| Author has several books, or any series | `{genre}/{author_sort}/{series}/{title} - {authors}` |
| Same as above, but this book has no series | `{genre}/{author_sort}/{title} - {authors}` |

Example after **Save to disk**:

```text
Science Fiction/
  The Martian - Andy Weir.epub
  Asimov, Isaac/
    Foundation/
      Foundation - Isaac Asimov.epub
      Foundation and Empire - Isaac Asimov.epub
    I, Robot - Isaac Asimov.epub
Fantasy/
  Tolkien, J.R.R./
    The Lord of the Rings/
      The Fellowship of the Ring - J.R.R. Tolkien.epub
```

Author folders use Calibre’s **author sort** name (`Asimov, Isaac`), not the display name. The file name still uses the display authors.

The “one book, no series” check looks at the **first author** only, across the whole library (virtual libraries are ignored).

## Requirements

- Calibre’s GUI. The template calls [`book_count()`](https://manual.calibre-ebook.com/generated/en/template_ref.html#book-count), which only works in the GUI (not `calibredb` / command-line save).
- A custom column whose lookup name is `#genre`. Hover a column header in the book list to see its lookup name. Create one under **Preferences → Add your own columns** if you do not already have it.

If `#genre` is empty for a book, that folder level is omitted.

Use the normal **Save to disk** action (not **Save to disk in a single folder**), or the extra folders will be flattened.

## Add it to Calibre

1. Open [`save.template`](save.template) and copy its entire contents.
2. In Calibre: **Preferences → Saving books to disk**.
3. Find **Save template** and open the **Template editor**.
4. Paste the template, then apply / OK out of Preferences.

The same screen lives under **Preferences → Import/export → Saving books to disk**.

After that, select books and use **Save to disk**. Calibre will build the folders from this template.

You can test a book in the Template editor before saving: pick a book, and the preview shows the path it would produce.

## Customizing

- To sort by a different custom column, replace `{#genre}` in both `template(...)` lines with that column’s lookup name (always starts with `#`).
- Series folders come from `{series:||/}`: if series is set, Calibre inserts `SeriesName/`; if not, that segment is left out. Slashes in the prefix/suffix are how Calibre creates extra folders; slashes inside field values are stripped so they cannot create folders on their own.
- The file name does not include series index. Two books in the same series are distinguished by title. If the generated path collides, Calibre appends ` (1)`, ` (2)`, and so on.

## Language

The file is written in Calibre’s [General Program Mode](https://manual.calibre-ebook.com/template_lang.html#general-program-mode) (`program:`). See the [template language manual](https://manual.calibre-ebook.com/template_lang.html) for the rest of the syntax.
