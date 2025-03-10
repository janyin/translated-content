---
title: ':fullscreen'
slug: Web/CSS/:fullscreen
translation_of: Web/CSS/:fullscreen
---
{{CSSRef}}

A pseudo-classe **`:fullscreen`** do CSS representa os elementos que estão atualmente no modo tela-cheia. Se vários elementos estiverem em tela-cheia, isto seleciona todos.

## Sintaxe

{{csssyntax}}

## Usage notes

The `:fullscreen` pseudo-class lets you configure your stylesheets to automatically adjust the size, style, or layout of content when elements switch back and forth between full-screen and traditional presentations.

## Examples

In this example, the color of a button is changed depending on whether or not the document is in full-screen mode. This is done without needing to specifically apply style changes using JavaScript.

### HTML

The page's HTML looks like this:

```html
<h1>MDN Web Docs Demo: :fullscreen pseudo-class</h1>

<p>This demo uses the <code>:fullscreen</code> pseudo-class to automatically
  change the style of a button used to toggle full-screen mode on and off,
  entirely using CSS.</p>

<button id="fs-toggle">Toggle Fullscreen</button>
```

The {{HTMLElement("button")}} with the ID `"fs-toggle"` will change between pale red and pale green depending on whether or not the document is in full-screen mode.

### CSS

The magic happens in the CSS. There are two rules here. The first establishes the background color of the "Toggle Full-screen Mode" button when the element is not in a full-screen state. The key is the use of the `:not(:fullscreen)`, which looks for the element to not have the `:fullscreen` pseudo-class applied to it.

```css
#fs-toggle:not(:fullscreen) {
  background-color: #afa;
}
```

When the document _is_ in full-screen mode, the following CSS applies instead, setting the background color to a pale shade of red.

```css
#fs-toggle:fullscreen {
  background-color: #faa;
}
```

## Specifications

| Specification                                                                                | Status                           | Comment             |
| -------------------------------------------------------------------------------------------- | -------------------------------- | ------------------- |
| {{SpecName('Fullscreen', '#:fullscreen-pseudo-class', ':fullscreen')}} | {{Spec2('Fullscreen')}} | Initial definition. |

## Compatibilidade com navegadores

{{Compat("css.selectors.fullscreen")}}

## See also

- [Fullscreen API](/pt-BR/docs/Web/API/Fullscreen_API)
- [Guide to the Fullscreen API](/pt-BR/docs/Web/API/Fullscreen_API/Guide)
- {{cssxref(":not")}}
- {{cssxref("::backdrop")}}
- DOM API: {{ domxref("Element.requestFullscreen()") }}, {{ domxref("Document.exitFullscreen()") }}, {{ domxref("Document.fullscreenElement") }}
- {{HTMLAttrXRef("allowfullscreen", "iframe")}} attribute
