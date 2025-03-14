---
title: '<small>: 附随コメント要素'
slug: Web/HTML/Element/small
---
{{HTMLRef}}

**HTML の `<small>` 要素**は、表示上のスタイルとは関係なく、著作権表示や法的表記のような、注釈や小さく表示される文を表します。既定では、 `small` から `x-small` のように、一段階小さいフォントでテキストが表示されます。

{{EmbedInteractiveExample("pages/tabbed/small.html", "tabbed-shorter")}}

<table class="properties">
  <tbody>
    <tr>
      <th scope="row">コンテンツカテゴリ</th>
      <td>
        <a href="/ja/docs/Web/HTML/Content_categories#フローコンテンツ"
          >フローコンテンツ</a
        >,
        <a href="/ja/docs/Web/HTML/Content_categories#記述コンテンツ"
          >記述コンテンツ</a
        >
      </td>
    </tr>
    <tr>
      <th scope="row">許可されている内容</th>
      <td>
        <a href="/ja/docs/Web/HTML/Content_categories#記述コンテンツ"
          >記述コンテンツ</a
        >
      </td>
    </tr>
    <tr>
      <th scope="row">タグの省略</th>
      <td>不可。開始タグと終了タグの両方が必要です。</td>
    </tr>
    <tr>
      <th scope="row">許可されている親要素</th>
      <td>
        <a href="/ja/docs/Web/HTML/Content_categories#記述コンテンツ"
          >記述コンテンツ</a
        >を受け入れるすべての要素、または<a
          href="/ja/docs/Web/HTML/Content_categories#フローコンテンツ"
          >フローコンテンツ</a
        >を受け入れるすべての要素。
      </td>
    </tr>
    <tr>
      <th scope="row">暗黙の ARIA ロール</th>
      <td>
        <a href="https://www.w3.org/TR/html-aria/#dfn-no-corresponding-role"
          >対応するロールなし</a
        >
      </td>
    </tr>
    <tr>
      <th scope="row">許可されている ARIA ロール</th>
      <td>すべて</td>
    </tr>
    <tr>
      <th scope="row">DOM インターフェイス</th>
      <td>{{domxref("HTMLElement")}}</td>
    </tr>
  </tbody>
</table>

## 属性

この要素には[グローバル属性](/ja/docs/Web/HTML/Global_attributes)のみがあります。

## 例

### 基本的な使用

```html
<p>これは最初の文です。
 <small>この文は小さい文字で表記されています。</small>
</p>
```

{{EmbedLiveSample("Basic_usage")}}

### CSS による代替

```html
<p>これは最初の文です。
  <span style="font-size:0.8em">この文は小さい文字で表記されています。
    </span>
</p>
```

{{EmbedLiveSample("CSS_alternative")}}

## 仕様書

| 仕様書                                                                                                               | 状態                             | 備考 |
| -------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ---- |
| {{SpecName('HTML WHATWG', 'semantics.html#the-small-element', '&lt;small&gt;')}}         | {{Spec2('HTML WHATWG')}} |      |
| {{SpecName('HTML5 W3C', 'textlevel-semantics.html#the-small-element', '&lt;small&gt;')}} | {{Spec2('HTML5 W3C')}}     |      |
| {{SpecName('HTML4.01', 'present/graphics.html#edef-SMALL', '&lt;small&gt;')}}             | {{Spec2('HTML4.01')}}     |      |

## 注

`<small>` 要素は {{htmlelement("b")}} 要素や {{htmlelement("i")}} 要素と同様に、構造と表現の分離の原則に反しますが、これら 3 つの要素は HTML5 で有効です。作者は `<small>` を使用するか CSS を使用するかを決める際に最良の判断を行うよう求められます。

## ブラウザーの互換性

{{Compat("html.elements.small")}}

## 関連情報

- {{HTMLElement("b")}}
- {{HTMLElement("sub")}} と {{HTMLElement("sup")}}
- {{HTMLElement("font")}}
- {{HTMLElement("style")}}
- HTML 4.01 仕様書: [Font Styles](https://www.w3.org/TR/html4/present/graphics.html#h-15.2)
