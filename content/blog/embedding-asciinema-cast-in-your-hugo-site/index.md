---
title: Embedding asciinema cast in your Hugo site
date: 2019-06-02
published: true
comments: true
tags:
  - hugo
  - asciinema
asciinema: true
---


Often, I use [asciinema](https://github.com/asciinema/asciinema) as a tool to show a specific command result or to explain step by step some linux setup, commonly runned in my terminal. 


Asciinema is an excellent tool when you need to show how to do something else, and very useful in technical blogs, documentation and landing pages alike. It allows you to create compact animations of commands along with their output and formatting with far less overhead than the alternative of creating a gif or mp4.

You can embed casts hosted on asciinema into your Hugo posts and pages by embedding a snippet like this directly into the markdown:

```html
<script src="https://asciinema.org/a/186686.js" id="asciicast-186686" async></script>
```

Although, this method works quietly, you also have another option to use that in your blog and with a better behavior, without upload the cast file created on the asciinema public site. Be careful, you need to think sometimes in security issues when you externalize some information and take care with some internal processes.

## Embedding in Hugo with shortcodes

I will show you how you should setup you hugo template to use asciinema. Follow these steps to accomplish it.


### Download the latest [asciinema-player release](https://github.com/asciinema/asciinema-player/releases).

```
wget https://github.com/asciinema/asciinema-player/releases/download/v2.6.1/asciinema-player.css
wget https://github.com/asciinema/asciinema-player/releases/download/v2.6.1/asciinema-player.js
```
Now, you have 2 files. you need to put both into the `static/` folder

```
$ ls -l static/css/asciinema-player.css 
-rw-rw-r-- 1 jenciso jenciso 50722 Fev 21  2018 static/css/asciinema-player.css
$ ls -l static/js/asciinema-player.js 
-rw-rw-r-- 1 jenciso jenciso 582376 Fev 21  2018 static/js/asciinema-player.js
```

Then, you need to configure your template to use this files. So you have to add this block of code in your `head` template section. In my case it was here: `layouts/partials/_shared/head.html`

```html
{{ if .Params.asciinema }}
    <link rel="stylesheet" type="text/css" href="{{ .Site.BaseURL }}css/asciinema-player.css" />
{{ end }}
```
And the last one, before the `</body>` tag in your template’s baseof, in my case it was here: `layouts/_default/baseof.html`

```html
{{ if .Params.asciinema }}
    <script src="{{ .Site.BaseURL }}js/asciinema-player.js"></script>
{{ end }}
```

#### Create a shortcode

Create a file `layouts/shortcodes/asciinema.html` with the following contents:

```html
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

Follow the instructions to create new casts. Save them to `static/casts/<key>.cast`.

1. You can now embed asciinema casts in your Hugo posts and pages with the following shortcode:

```sh
{{</* asciinema key="demo-cast" rows="10" preload="1" */>}}
```
2. You also need to include `asciinema = true` in the frontmatter for you post too, to make sure the javascript and css assets are included.


Finally, you should end up with something like this:

```shell
{{< asciinema key="249623" cols="158" rows="40" preload="1" speed="1" >}}
```

#### References 

https://www.tonylykke.com/posts/custom-fonts-in-asciinema/
