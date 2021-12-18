---
title: Embedding asciinema cast in your Hugo site
date: 2019-06-02
published: true
comments: true
asciinema: true
tags:
  - hugo
  - asciinema
---

Every now and then I want to show step by step a process simply recording an output of my terminal, so commonly I use [asciinema](https://github.com/asciinema/asciinema) to do that.

It is an excellent tool very useful to dynamic content which give you a good explanation of your process. It will create a compact animation along with their output and formatting with far less overhead, if you compare it with others alternative like a gif or mp4 files.

You could use asciinema in your Hugo posts and pages by embedding a snippet like this directly as markdown:

```html
<script src="https://asciinema.org/a/186686.js" id="asciicast-186686" async></script>
```

Although, this method works quietly, you have another method to use asciinema in your blog site without upload the cast file on public, it is embedding the cast file

## Setup 

I will show you how to setup you hugo to use asciinema. Follow these steps to accomplish it.

* Download the latest [asciinema-player release](https://github.com/asciinema/asciinema-player/releases).

Using wget:

```bash
BASE_URL=https://github.com/asciinema/asciinema-player/releases/download
wget $BASE_URL/v2.6.1/asciinema-player.css
wget $BASE_URL/v2.6.1/asciinema-player.js
```

Put these files into `static/css` and `static/js` folders. Like this:

```console
$ ls -l static/css/asciinema-player.css 
-rw-rw-r-- 1 jenciso jenciso 50722 Fev 21  2018 static/css/asciinema-player.css
$ ls -l static/js/asciinema-player.js 
-rw-rw-r-- 1 jenciso jenciso 582376 Fev 21  2018 static/js/asciinema-player.js
```

* You need to configure your hugo template files to use css and js files previously downloaded. 

Insert this block in your `head` template section. In my case it's here: `layouts/partials/_shared/head.html`

```html
{{ if .Params.asciinema }}
    <link rel="stylesheet" type="text/css" href="{{ .Site.BaseURL }}css/asciinema-player.css" />
{{ end }}
```
And before the `</body>` tag in your template’s baseof. Ex.: `layouts/_default/baseof.html`

```html
{{ if .Params.asciinema }}
    <script src="{{ .Site.BaseURL }}js/asciinema-player.js"></script>
{{ end }}
```

* Create a shortcode

Create a file `layouts/shortcodes/asciinema.html` with the this content:

```python
<p>
    <asciinema-player
        src="/casts/{{ with .Get "key" }}{{ . }}{{ end }}.cast"
        cols="{{ if .Get "cols" }}{{ .Get "cols" }}{{ else }}640{{ end }}"
        rows="{{ if .Get "rows" }}{{ .Get "rows" }}{{ else }}10{{ end }}"
        {{ if .Get "autoplay" }}autoplay="{{ .Get "autoplay" }}"{{ end }}
        {{ if .Get "preload" }}preload="{{ .Get "preload" }}"{{ end }}
        {{ if .Get "loop" }}loop="{{ .Get "loop" }}"{{ end }}
        start-at="{{ if .Get "start-at" }}{{ .Get "start-at" }}{{ else }}0{{ end }}"
        speed="{{ if .Get "speed" }}{{ .Get "speed" }}{{ else }}1{{ end }}"
        {{ if .Get "idle-time-limit" }}idle-time-limit="{{ .Get "idle-time-limit" }}"{{ end }}
        {{ if .Get "poster" }}poster="{{ .Get "poster" }}"{{ end }}
        {{ if .Get "font-size" }}font-size="{{ .Get "font-size" }}"{{ end }}
        {{ if .Get "theme" }}theme="{{ .Get "theme" }}"{{ end }}
        {{ if .Get "title" }}title="{{ .Get "title" }}"{{ end }}
        {{ if .Get "author" }}author="{{ .Get "author" }}"{{ end }}
        {{ if .Get "author-url" }}author-url="{{ .Get "author-url" }}"{{ end }}
        {{ if .Get "author-img-url" }}author-img-url="{{ .Get "author-img-url" }}"{{ end }}
    ></asciinema-player>
</p>
```

> The fields map directly to those documented [here](https://github.com/asciinema/asciinema-player#asciinema-player-element-attributes). All fields are supported, but only the key is required.

### Testing

Create a new cast ans save it here `static/casts/<key>.cast`.

* You can now embed asciinema casts in your Hugo posts and pages with the following shortcode:

```sh
{{</* asciinema key="demo-cast" rows="10" preload="1" */>}}
```

* You also need to include `asciinema = true` in the frontmatter for you post too, to make sure the javascript and css assets are included. Ex:

```yaml
---
title: Kubernetes Backup - ARK 
description: Kubernetes backup process using ark
asciinema: true
tags:
  - kubernetes
  - backup
---
```

### Result

Finally, the result should show something like this:

{{< asciinema key="249623" cols="158" rows="40" preload="1" speed="1" >}}

#### References 

https://www.tonylykke.com/posts/custom-fonts-in-asciinema/
