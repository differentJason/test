# test
A headless Hugo site
To create a Hugo site that meets your requirements, we'll need to set up a few layout files and configurations. Below is a structured guide that includes the necessary layout files and a brief explanation of each component.

### Directory Structure

Here's how your directory structure should look:

```
your-hugo-site/
├── archetypes/
├── content/
│   ├── _topics/
│   │   └── topic1/
│   │       └── index.md
│   └── page1/
│       └── index.md
├── layouts/
│   ├── _default/
│   │   ├── baseof.html
│   │   ├── single.html
│   │   └── list.html
│   ├── partials/
│   │   ├── headless-bundle.html
│   │   └── toc.md
├── config.toml
└── mdPackages/
```

### Configuration (`config.toml`)

Make sure to configure your `config.toml` to enable markdown rendering and set up any other necessary parameters:

```toml
baseURL = "http://example.com/"
languageCode = "en-us"
title = "My Hugo Site"
theme = "your-theme"

[markup]
  [markup.goldmark]
    [markup.goldmark.extensions]
      [markup.goldmark.extensions.table]
      [markup.goldmark.extensions.footnote]
```

### Layout Files

#### 1. Base Layout (`layouts/_default/baseof.html`)

This file serves as the base layout for all pages.

```html
<!DOCTYPE html>
<html lang="{{ .Lang }}">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ block "title" . }}{{ .Title }}{{ end }}</title>
    {{ partial "head.html" . }}
</head>
<body>
    {{ block "main" . }}{{ end }}
</body>
</html>
```

#### 2. Single Layout (`layouts/_default/single.html`)

This layout is used for individual pages.

```html
{{ define "main" }}
<article>
    <h1>{{ .Title }}</h1>
    {{ .Content }}
    {{ if .Params.topics }}
    <h2>Topics</h2>
    <ul>
        {{ range .Params.topics }}
        <li>{{ partial "headless-bundle.html" . }}</li>
        {{ end }}
    </ul>
    {{ end }}
</article>
{{ end }}
```

#### 3. List Layout (`layouts/_default/list.html`)

This layout is used for lists of items.

```html
{{ define "main" }}
<h1>{{ .Title }}</h1>
<ul>
    {{ range .Pages }}
    <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
    {{ end }}
</ul>
{{ end }}
```

#### 4. Headless Bundle Partial (`layouts/partials/headless-bundle.html`)

This partial renders the headless bundle titles.

```html
{{ $bundle := . }}
<h3><a href="{{ $bundle.RelPermalink }}">{{ $bundle.Title }}</a></h3>
```

#### 5. TOC Markdown Generation (`layouts/partials/toc.md`)

This file generates the markdown TOC for each page.

```markdown
# Table of Contents

- [{{ .Title }}]({{ .RelPermalink }})
{{ range .Params.topics }}
  - [{{ .Title }}]({{ .RelPermalink }})
{{ end }}
```

### Markdown Publishing Logic

To publish Markdown versions of your pages and generate the `toc.md` files, you can create a custom script or use Hugo's capabilities in combination with a build process. Here’s a simple example of how you might approach it:

1. **Custom Script**: Write a script that runs after the Hugo build process to copy the generated Markdown files to the `mdPackages` directory and create the `toc.md` files.

2. **Hugo Command**: Use the following command to build your site:
   ```bash
   hugo --destination=public
   ```

3. **Markdown Copying**: You can use a simple shell script or a Node.js script to copy the `.md` files and generate the `toc.md` files.

### Example Shell Script

Here’s a simple example of a Bash script that could be run after the Hugo build to copy files:

```bash
#!/bin/bash

# Create mdPackages directory if it doesn't exist
mkdir -p mdPackages

# Copy all Markdown files to mdPackages
find public/ -name '*.md' -exec cp {} mdPackages/ \;

# Generate TOC files
for file in mdPackages/*.md; do
    toc_file="${file%.*}_toc.md"
    echo "# Table of Contents" > "$toc_file"
    echo "- [$(basename "$file" .md)]($file)" >> "$toc_file"
    # Add headless bundles
    # You would need to parse the front matter to get topics here
done
```

### Final Thoughts

This setup provides a solid foundation for your Hugo site with headless bundles and Markdown publishing. Make sure to adapt paths and logic as necessary based on your specific requirements and environment. If you have any further questions or need additional customization, feel free to ask!
