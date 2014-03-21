# MusicGraph API Documentation

Inspired by the three-column, single-page design made popular by [Stripe's](https://stripe.com/docs/api) API documentation. Everything is written in Github flavored Markdown — even the code samples! As you scroll, the URL path is updated to the nearest anchor tag, so it's easy to link to a particular point in the documentation. Support for syntax highlighting is provided by [Rogue](http://rouge.jayferd.us/demo).

### Prerequisites

You're going to need:

- **Ruby, version 1.9.3 or newer**
- **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

### Local Deploy

1. `cd api-docs`
2. Install all dependencies: `bundle install`
3. Start the test server: `bundle exec middleman server`

You can now see the docs at <http://localhost:4567>. As you edit `source/index.md`, the server will automatically update!

### Server Deploy

Publishing the MusicGraph API documentation couldn't be more simple. Middleman will build your website to the `build` directory of your project, and you can copy those static HTML files to the server of your choice.

1. `cd api-docs`
2. Compile to HTML: `rake build`
3. Copy the static HTML files in `build` to the server

The MusicGraph API documentation is static HTML, Javascript, and CSS, so it's pretty trivial to host. 

## Markdown Syntax

### Headers

For headers:

    # Level 1 Header
    ## Level 2 Header
    ### Level 3 Header

Note that only level 1 and 2 headers will appear in the table of contents.

### Paragraph Text

For normal text, just type your paragraph on a single line.

    This is some paragraph text. Exciting, no?

Make sure the lines above below your paragraph are empty.

### Code Samples

For code samples:

```
  ```ruby
  # This is some Ruby code!
  ```

  ```python
  // This is some Python code!
  ```
```

Code samples will appear in the dark area to the left of the main text. We recommend positioning code samples right under headers in your markdown file.

For the full list of supported languages, see [rouge](http://rouge.jayferd.us/demo).

### Code Annotations

For code annotations:

    > This is a code annotation. It will appear in the area to the right, next to the code samples.

Code annotations are essentially the same thing as paragraphs, but they'll appear in the area to the right along with your code samples.

### Tables

Slate uses PHP Markdown Extra style tables:

```markdown
Table Header 1 | Table Header 2 | Table Header 3
-------------- | -------------- | --------------
Row 1 col 1 | Row 1 col 2 | Row 1 col 3
Row 2 col 1 | Row 2 col 2 | Row 2 col 3
```

Note that the pipes do not need to line up with each other on each line.

If you don't like that syntax, feel free to use normal html `<table>`s directly in your markdown.

### Formatted Text

    This text is **bold**, this is *italic*, this is an `inline code block`.

You can use those formatting rules in code annotations, tables, paragraphs, lists, wherever.

### Lists

    1. This
    2. Is
    3. An
    4. Ordered
    5. List

    * This
    * Is
    * A
    * Bullet
    * List

### Links

    This is an [internal link](#error-code-definitions), this is an [external link](http://google.com).

### Notes and Warnings

You can add little highlighted warnings and notes with just a little HTML embedded in your markdown document:

    <aside class="notice">
    You must replace `meowmeowmeow` with your personal API key.
    </aside>

Use `class="notice"` for blue notes, `class="warning"` for red warnings, and `class="success"` for green notes.
