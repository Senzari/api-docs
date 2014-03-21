# MusicGraph API Documentation

Inspired by the three-column, single-page design made popular by [Stripe's](https://stripe.com/docs/api) API documentation. Everything is written in Markdown — even the code samples! As you scroll, the URL path is updated to the nearest anchor tag, so it's easy to link to a particular point in the documentation.

```
Senzari/api-docs
 └ source
    └ fonts
    └ images
    └ javascripts
    └ stylesheets
    └ index.md
```

## Markdown Editing

The documentation is written on a single page, using [Github Flavored Markdown](https://help.github.com/articles/github-flavored-markdown):

`source/index.md`

For **headers**:

    # Level 1 Header
    ## Level 2 Header
    ### Level 3 Header

Only level 1 and level 2 headers appear in the table of contents.

For **normal text**, just type your paragraph on a single line:

    This text is **bold**, this is *italic*, this is an `inline code block`. This is an [internal link](#error-code-definitions), this is an [external link](http://google.com).

Make sure the lines above and below the paragraph are empty.

For **code samples**:

```
    ```ruby
    # This is some Ruby code!
    ```

    ```python
    // This is some Python code!
    ```
```

Position code samples right under headers in the markdown file. Here's a [list of supported languages](http://rouge.jayferd.us/demo).

For **code annotations**:

    > This is a code annotation.

Code annotations are essentially the same thing as paragraphs, but they'll appear in the area to the right along with your code samples.

For **lists**:

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

For **tables**:

```markdown
Table Header 1 | Table Header 2 | Table Header 3
-------------- | -------------- | --------------
Row 1 col 1 | Row 1 col 2 | Row 1 col 3
Row 2 col 1 | Row 2 col 2 | Row 2 col 3
```

Note that the pipes do not need to line up with each other on each line.

For **highlighted notes**:

    <aside class="notice">
    You must replace `meowmeowmeow` with your personal API key.
    </aside>

Use `class="notice"` for blue notes, `class="warning"` for red warnings, and `class="success"` for green notes.

## Installation Instructions

You're going to need:

- **Ruby, version 1.9.3 or newer** - Type `ruby -v` to see what version you have.
- **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

### Local Deploy

1. Go to the directory: `cd api-docs`
2. Install all dependencies: `bundle install`
3. Start the dev server: `bundle exec middleman server`

You can now see the docs at <http://localhost:4567>. As you edit `source/index.md`, the server will automatically update!

### Remote Deploy

Publishing the MusicGraph API documentation couldn't be more simple. Middleman will build your website to the `build` directory of your project, and you can copy those static HTML files to the server of your choice.

1. `cd api-docs`
2. Compile to HTML: `rake build`
3. Copy the static HTML files in `build` to the server

The MusicGraph API documentation is static HTML, Javascript, and CSS, so it's pretty trivial to host. See for yourself, try deploying this `README.md` file!
