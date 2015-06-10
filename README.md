# Epub3 *fly-by*

The ***Epub3.0 specification*** defines both what can go into a Epub document and how it should be rendered. It utilizes standard web technologies like `CSS3` and `JavaScript` but uses the `XML`-version of `HTML5`. Work on the standard began in 2010.

It can be used for every type of publication and **can be read non-linearly**. An Epub **can contain several diverent versions/publications**.

Since it is based on web standards it allows for ***reflowable content*** as opposed to e.g. `PDF`s fixed format. The standard requires `MP3` and `MathML` support and allows for both `WebM` and `H.264`.

The specification is comprised of four parts:

1. **EPUB Publications 3.0**
  - Required content types.
  - The `XML schema` that defines how and what meta information about the document can be provided.
  - When and how to provide fallbacks.
- **EPUB Content Documents 3.0**
  - A whitelist of `XHTML5`, `SVG1.1`, `CSS3` features that can be used ("***profiles***").
  - How the document navigation file must be structured.
- **EPUB Media Overlays 3.0**
  - How to synchronize the display area with audio narration.
- **EPUB Open Container Format (OCF) 3.0**
  - How to bundle all files together.

## Creating an Epub3.0 document

### The bare minimum

1. Exactly one **OCF container definition file** (`META-INF/container.xml`)
  - Needs to point the package-document.
- Exactly one ***Package Document***  
  - Needs to lists all referenced resources
  - Needs to hold a certain set of document metadata.
  - Needs to define the recommended reading order (***spine***).
- At least one ***Epub Content Document***
- Exactly one ***Epub Navigation Document***
- Exactly one **mimetype file** (named `mimetype`)  
  - Needs to containing the string: `application/epub+zip`
- **Zip the package**  
  - Needs to include all resources except audio and video files wich can be loaded externally.

### container.xml

```xml
<?xml version='1.0' encoding='utf-8'?>
<container xmlns="urn:oasis:names:tc:opendocument:xmlns:container" version="1.0">
  <rootfiles>
    <rootfile media-type="application/oebps-package+xml"
      full-path="<!-- package-document -->" />
    <!-- ... -->
    <!-- You could have more than one rootfile
      since you can have multiple publications
      per Epub package -->
  </rootfiles>
</container>
```

### Package-document

The order of elements is of significance in the package document:

1. `<metadata>`
2. `<manifest>`
3. `<spine>`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<package xmlns="http://www.idpf.org/2007/opf" version="3.0"
  unique-identifier="pub-id">
  <metadata xmlns:dc="http://purl.org/dc/elements/1.1/"
    xmlns:dcterms="http://purl.org/dc/terms/"
    <!-- ... -->
  </metadata>
  <manifest>
    <!-- ... -->
  </manifest>
  <spine>
    <!-- ... -->
  </spine>
</package>
```

#### `<metadata>`

```xml
<dc:identifier id="id">
  <!-- unique identifier freely chosen by you,
    you could use your ISBN -->
</dc:identifier>
<dc:title id="titel">
  <!-- ... -->
</dc:title>
<dc:language>
  <!-- IETF language tag -->
</dc:language>
<meta property="dcterms:modified">
   <!-- timestamp of last modification,
    helps reader track changes
    eg. 2012-06-08T14:44:00Z -->
</meta>
```


#### `<manifest>`

```xml
<manifest>
  <item media-type="application/xhtml+xml"
    id="<!-- ... -->"
    href="<!-- ... -->" />
  <!-- ... -->
</manifest>
```

##### Fallback

```xml
<item href="<!-- ... -->"
  id="image" fallback="NO-image"
  media-type="image/png" />
<item href="<!-- ... -->"
  id="NO-image"
  media-type="application/xhtml+xml"/>
```

In the `fallback` property you can reference the `id` of another item which will be used in case the original item can not.

#### `<spine>`

```xml
<spine>
  <itemref idref="<!-- ... -->" />
  <!-- ... -->
</spine>
```

The property `idref` references elements from the `<manifest>` due to their `id` property.

The ordering of the `itemref` elements represents the recommended reading order.

### Content documents

Since Epub3 uses XHTML5 file should end with `.xhtml` and have the following opening. The whole set of HTML5 elements can be used.

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns=http://www.w3.org/1999/xhtml>
<!-- ... -->
```

#### Epub XHTML5 extensions

The extensions are defined in the [EPUB3 Structural Semantics Vocabulary](http://www.idpf.org/epub/vocab/structure/). If the extensions will be used the `<html>` element needs to extended with the `xmlns:epub` property:

```html
<html xmlns="http://www.w3.org/1999/xhtml"
  xmlns:epub="http://www.idpf.org/2007/ops">
```

##### Available properties (among others)

- `epub:type="chapter"`
- `epub:type="title`
- `epub:type="subtitle"`
- `epub:type="contributors"`
- `epub:type="foreword"`
