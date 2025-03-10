---
title: '<meta>: メタデータ要素'
slug: Web/HTML/Element/meta
---

{{HTMLRef}}

**`<meta>`** は [HTML](/ja/docs/Web/HTML) の要素で、{{Glossary("Metadata","メタデータ")}}を表します。これは、他のメタ関連要素 ({{HTMLElement("base")}}, {{HTMLElement("link")}}, {{HTMLElement("script")}}, {{HTMLElement("style")}}, {{HTMLElement("title")}}) で表すことができないものを表します。

<table class="properties">
  <tbody>
    <tr>
      <th>
        <a href="/ja/docs/Web/Guide/HTML/Content_categories"
          >コンテンツカテゴリー</a
        >
      </th>
      <td>
        <a href="/ja/docs/Web/Guide/HTML/Content_categories#メタデータコンテンツ"
          >メタデータコンテンツ</a
        >。 <a href="/ja/docs/Web/HTML/Global_attributes/itemprop"><code>itemprop</code></a> 属性がある場合は<a href="/ja/docs/Web/Guide/HTML/Content_categories#フローコンテンツ"
          >フローコンテンツ</a
        >、<a href="/ja/docs/Web/Guide/HTML/Content_categories#記述コンテンツ"
          >記述コンテンツ</a
        >。
      </td>
    </tr>
    <tr>
      <th>許可されている内容</th>
      <td>なし。これは{{Glossary("empty element", "空要素")}}です。</td>
    </tr>
    <tr>
      <th>タグの省略</th>
      <td>
        空要素であるため開始タグは必須であり、終了タグは置いてはいけません。
      </td>
    </tr>
    <tr>
      <th>許可されている親要素</th>
      <td>
        <ul>
          <li>
            <code>&#x3C;meta charset></code>, <code>&#x3C;meta http-equiv></code>: {{HTMLElement("head")}} 要素。{{htmlattrxref("http-equiv", "meta")}} がエンコーディング宣言ではない場合は、 <code>&#x3C;head></code> 要素内にある {{HTMLElement("noscript")}} 要素の内部にも配置できます。
          </li>
          <li>
            <code>&#x3C;meta name></code>: <a
              href="/ja/docs/Web/Guide/HTML/Content_categories#メタデータコンテンツ"
              >メタデータコンテンツ</a
            >を受け入れるすべての要素。
          </li>
          <li>
            <code>&#x3C;meta itemprop></code>: <a
              href="/ja/docs/Web/Guide/HTML/Content_categories#メタデータコンテンツ"
              >メタデータコンテンツ</a
            >または<a href="/ja/docs/Web/Guide/HTML/Content_categories#フローコンテンツ"
              >フローコンテンツ</a
            >を受け入れるすべての要素。
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <th scope="row">許可されている ARIA ロール</th>
      <td>
        <a href="https://www.w3.org/TR/html-aria/#dfn-no-corresponding-role"
          >対応するロールなし</a
        >
      </td>
    </tr>
    <tr>
      <th scope="row">許可されている ARIA ロール</th>
      <td>許可されている <code>role</code> なし</td>
    </tr>
    <tr>
      <th>DOM インターフェイス</th>
      <td>{{domxref("HTMLMetaElement")}}</td>
    </tr>
  </tbody>
</table>

`<meta>` 要素で提供されるメタデータの型は、以下のいずれかになります。

- {{htmlattrxref("name", "meta")}} 属性が設定されている場合、`<meta>` 要素は _文書レベルメタデータ_ を提供し、ページ全体に適用されます。
- {{htmlattrxref("http-equiv", "meta")}} 属性が設定されている場合、 `<meta>` 要素は _プラグ魔ディレクティブ_ であり、同様の名前の HTTP ヘッダーによって与えられるものと同等の情報を提供します。
- {{htmlattrxref("charset", "meta")}} 属性が設定されている場合、 `<meta>` 要素は _文字セット宣言_ であり、文書がどの文字エンコーディングでエンコードされているかを指定します。
- [`itemprop`](/ja/docs/Web/HTML/Global_attributes/itemprop) 属性が設定されている場合、`<meta>` 要素は _ユーザー定義のメタデータ_ を提供します。

## 属性

この要素には[グローバル属性](/ja/docs/Web/HTML/Global_attributes)があります。

> **Note:** {{htmlattrxref("name", "meta")}} 属性は `<meta>` 要素において特別な意味を持ちます。また、 [`itemprop`](/ja/docs/Web/HTML/Global_attributes/itemprop) 属性は、 `<meta>` 要素にすでに {{htmlattrxref("name", "meta")}}, {{htmlattrxref("http-equiv", "meta")}}, {{htmlattrxref("charset", "meta")}} のいずれかがある場合は設定してはいけません。

- {{htmlattrdef("charset")}}
  - : この属性は、文書の文字エンコーディングを宣言します。この属性が存在する場合、その値は大文字小文字の区別なく "`utf-8`" という ASCII 文字列と一致する必要があります。 UTF-8 が HTML5 文書で唯一の有効なエンコーディングだからです。文字エンコーディングを宣言する `<meta>` 要素は、文書の最初の 1024 バイトの中に完全に収まっている必要があります。
- {{htmlattrdef("content")}}
  - : この属性は状況に応じて、{{htmlattrxref("http-equiv", "meta")}} 属性または {{htmlattrxref("name", "meta")}} 属性に関連付けられた値を持ちます。
- {{htmlattrdef("http-equiv")}}

  - : プラグマディレクティブを定義します。属性名が `http-equiv(alent)` なのは、利用できる値のすべてが特定の HTTP ヘッダーの名前だからです。

    - `content-security-policy`

      この値により、ページ作者がページの[コンテンツポリシー](/ja/docs/Web/Security/CSP/CSP_policy_directives)を定義できます。いくつかの例外を除き、ポリシーはサーバーオリジンやスクリプトのエンドポイントの指定に関与します。これは、クロスサイトスクリプティング攻撃の対策になります。

    - `content-type`

      文書の [MIME タイプ](/ja/docs/Web/HTTP/Basics_of_HTTP/MIME_types)を宣言するもので、後に文字エンコーディングの定義が続きます。指定された場合、 `content` 属性には "`text/html; charset=utf-8`" 設定しなければなりません。これは `<meta>` 要素に {{htmlattrxref("charset", "meta")}} 属性を指定した場合と同じであり、文書内の位置の制約も同様になります。 **メモ:** `text/html`で提供される文書でのみ使用することができます - XML MIME型で提供される文書では使用できません。

    - `default-style`

     既定の [CSS スタイルシート](/ja/docs/Web/CSS)セットの名前を設定します。

    - `x-ua-compatible`

      指定された場合、 `content` 属性は "`IE=edge`" という値を持つ必要があります。ユーザーエージェントはこのプラグマを無視することが要求されます。

    - `refresh` これは以下のことを指定する指示です。

      - {{htmlattrxref("content", "meta")}} 属性に正の整数が 1 つだけ含まれている場合は、ページを再読み込みするまでの秒数。
      - {{htmlattrxref("content", "meta")}} 属性に正の整数と、その後に文字列 '`;url=`' と有効な URL がある場合は、別のページにリダイレクトするまでの秒数。

      ##### アクセシビリティの考慮

      `refresh` の値で設定されたページは、時間間隔が短すぎになるという危険性があります。画面の内側から読み上げるような支援技術を利用している人は、自動的にリダイレクトされる前にページの内容を読み、理解することができないかもしれません。また、弱視の人にとっても、突然に予告なくページ内容が更新されると、混乱させる可能性があります。

      - [MDN Understanding WCAG, Guideline 2.1 explanations](/ja/docs/Web/Accessibility/Understanding_WCAG/Operable#guideline_2.2_—_enough_time_provide_users_enough_time_to_read_and_use_content)
      - [MDN Understanding WCAG, Guideline 3.1 explanations](/ja/docs/Web/Accessibility/Understanding_WCAG/Understandable#guideline_3.2_—_predictable_make_web_pages_appear_and_operate_in_predictable_ways)
      - [Understanding Success Criterion 2.2.1 | W3C Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/time-limits-required-behaviors.html)
      - [Understanding Success Criterion 2.2.4 | W3C Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/time-limits-postponed.html)
      - [Understanding Success Criterion 3.2.5 | W3C Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/consistent-behavior-no-extreme-changes-context.html)

- {{htmlattrdef("name")}}

  - : `name` 属性と `content` 属性を一緒に使用すると、文書のメタデータを名前と値のペアで提供することができます。 `name` 属性はメタデータの名前、 `content` 属性がその値を指定します。

    HTML 仕様書で定義されている標準的なメタデータ名のセットについては、[標準メタデータ名](/ja/docs/Web/HTML/Element/meta/name)を参照してください。

## 例

```html
<meta charset="utf-8">

<!-- 3 秒後にページをリダイレクト -->
<meta http-equiv="refresh" content="3;url=https://www.mozilla.org">
```

## 仕様書

{{Specifications}}

## ブラウザーの互換性

{{Compat}}

## 関連情報

- [標準メタデータ名](/ja/docs/Web/HTML/Element/meta/name)
- [学習: `<meta>`](/ja/docs/Learn/HTML/Introduction_to_HTML/The_head_metadata_in_HTML#metadata_the_meta_element)
- [viewport メタタグ](/ja/docs/Web/HTML/Viewport_meta_tag)
