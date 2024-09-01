---
date: 2024-09-01
draft: false
params:
  author: Bananafish
title: 让 Blowfish 主题支持 $\LaTeX$
tags:
  - Blog
---
## $\LaTeX$ 支持问题
1. `Blowfish` 官方使用 `KaTex` 渲染公式
2. 没有暴露 `Ketax` 的配置方法，只单纯使用了一个短代码 `{{ ketax }}` 来启用公式渲染

## 更改 `Ketax` 的默认终止符
`KaTex` 默认的公式终止符为 `\\(` 与 `\\)`，这在很多 `markdown` 写作软件中不兼容，很麻烦
1. 找到 `themes/blowfish/layouts/partials/vendor.html`
2. 将和 `ketax` 有关的部分从：
```html
{{/* Katex */}}
{{ if .Page.HasShortcode "katex" }}
{{ $katexCSS := resources.Get "lib/katex/katex.min.css" }}
{{ $katexCSS := $katexCSS | resources.Fingerprint "sha512" }}
<link type="text/css" rel="stylesheet" href="{{ $katexCSS.RelPermalink }}" integrity="{{ $katexCSS.Data.Integrity }}" />
{{ $katexJS := resources.Get "lib/katex/katex.min.js" }}
{{ $katexJS := $katexJS | resources.Fingerprint "sha512" }}
<script defer src="{{ $katexJS.RelPermalink }}" integrity="{{ $katexJS.Data.Integrity }}"></script>
{{ $katexRenderJS := resources.Get "lib/katex/auto-render.min.js" }}
{{ $katexRenderJS := $katexRenderJS | resources.Fingerprint "sha512" }}
<script defer src="{{ $katexRenderJS.RelPermalink }}" integrity="{{ $katexRenderJS.Data.Integrity }}"
  onload="renderMathInElement(document.body);"></script>
{{ $katexFonts := resources.Match "lib/katex/fonts/*" }}
{{ range $katexFonts }}
<!-- {{ .RelPermalink }} -->
{{ end }}
{{ end }}

{{/* TypeIt */}}
{{ if .Page.HasShortcode "typeit" }}
{{ $typeitLib := resources.Get "lib/typeit/typeit.umd.js" }}
<script defer src="{{ $typeitLib.RelPermalink }}" integrity="{{ $typeitLib.Data.Integrity }}"></script>
{{ end }}
```
   更改为：
```html
{{/* Katex */}}

// 如果你想默认启用公式渲染，把下面这行删了
{{ if .Page.HasShortcode "katex" }}

{{ $katexCSS := resources.Get "lib/katex/katex.min.css" }}
{{ $katexCSS := $katexCSS | resources.Fingerprint "sha512" }}
<link type="text/css" rel="stylesheet" href="{{ $katexCSS.RelPermalink }}" integrity="{{ $katexCSS.Data.Integrity }}" />
{{ $katexJS := resources.Get "lib/katex/katex.min.js" }}
{{ $katexJS := $katexJS | resources.Fingerprint "sha512" }}
<script defer src="{{ $katexJS.RelPermalink }}" integrity="{{ $katexJS.Data.Integrity }}"></script>
{{ $katexRenderJS := resources.Get "lib/katex/auto-render.min.js" }}
{{ $katexRenderJS := $katexRenderJS | resources.Fingerprint "sha512" }}
<script defer src="{{ $katexRenderJS.RelPermalink }}" integrity="{{ $katexRenderJS.Data.Integrity }}"></script>
<script>
document.addEventListener("DOMContentLoaded", function() {
renderMathInElement(document.body, {
// 在这里增加你需要的自定义终止符
delimiters: [
{left: '$$', right: '$$', display: true},
{left: '$', right: '$', display: false},
{left: '\\(', right: '\\)', display: false},
{left: '\\[', right: '\\]', display: true}
],
throwOnError : false
});
});
</script>
{{ $katexFonts := resources.Match "lib/katex/fonts/*" }}
{{ range $katexFonts }}
<!-- {{ .RelPermalink }} -->
{{ end }}
```

## 参考
1. [`Blowfish` issue](https://github.com/nunocoracao/blowfish/issues/551)
2. [`KeTax`文档](https://katex.org/docs/autorender)