# Convert markdown to ebook (epub and mobi)

OSX only.

## Install dependencies

You will need Pandoc and KindleGen applications.

```
brew update && brew upgrade
brew install pandoc homebrew/binary/kindlegen
```

## Convertion

### Get the source

Example project: https://github.com/vasanthk/css-refresher-notes

```
git clone https://github.com/vasanthk/css-refresher-notes.git
```

### Handle converting errors

Pandoc generation fails in case of incorrectly placed html tag inside the text flow (like a &lt;div&gt;). Every time check the result!

A useful tool to debug: [EPUBReader](https://addons.mozilla.org/en-US/firefox/addon/epubreader/). This app always marks the place (line number) of the error.

I had to make the following replacements in the downloaded markdown file:

|      from      |          to          |
|:--------------:|:--------------------:|
| &lt;html&gt; element | &amp;lt;html&amp;gt; element |
| &lt;meta&gt; viewport element | &amp;lt;meta&amp;gt; viewport element |
| &lt;img&gt; element  | &amp;lt;img&amp;gt; element  |
| &lt;picture&gt; element | &amp;lt;picture&amp;gt; element |
| &lt;video&gt; and &lt;audio&gt; elements | &amp;lt;video&amp;gt; and &amp;lt;audio&amp;gt; elements |
| &lt;img&gt; tag | &amp;lt;img&amp;gt; tag |

HTML tags inside code blocks are allowed!

### Metadata

By default the output file will start with "UNTITLED".

To setup the document title you have to create a `metadata.xml` file.

```
echo "<dc:title>CSS Refresher Notes</dc:title>" > metadata.xml
```

You can set title, creator, date, etc. See `--epub-metadata=FILE` section in [Pandoc Documentation](http://pandoc.org/README.html#options-affecting-specific-writers).

### Convertion

Now we can convert the markdown file to epub.

```
pandoc -f markdown_github -t epub -o css-refresher-notes.epub --toc --toc-depth=2 --epub-metadata=metadata.xml css-refresher-notes/README.md
```

Using KindleGen we can convert epub to mobi.

```
kindlegen css-refresher-notes.epub
```

### Further enhancements

You can specify book cover with `--epub-cover-image`.

You can customize the book lookout with custom CSS using `--epub-stylesheet`.

## Sources

- https://puppetlabs.com/blog/automated-ebook-generation-convert-markdown-epub-mobi-pandoc-kindlegen
- http://pandoc.org/README.html
