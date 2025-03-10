---
title: 501 Not Implemented
slug: Web/HTTP/Status/501
tags:
  - HTTP
  - Server error
  - Status code
translation_of: Web/HTTP/Status/501
---
{{HTTPSidebar}}

HyperText Transfer Protocol (HTTP) の **`501 Not Implemented`** サーバーエラーレスポンスコードは、**サーバーがリクエストを満たすのに必要な機能に対応していないこと**を示します。

このステータスは {{HTTPHeader("Retry-After")}} ヘッダーを送信することもでき、いつまでに機能を機能がサポートされているかどうかを確認するためのチェックバックのタイミングを要求元に伝えることができます。

`501` は、サーバーがリクエストメソッドを理解できず、あるリソースに対して対応することができない場合のレスポンスに適切です。サーバーが対応する必要がある (したがって、 `501` を返す必要がない) メソッドは {{HTTPMethod("GET")}} と {{HTTPMethod("HEAD")}} だけです。

サーバーがそのメソッドを理解して*いて*、意図的に対応していない場合は、適切なレスポンスは {{HTTPStatus(405, "405 Method Not Allowed")}} です。

> **Note:** - 501 エラーは修正できるものではありませんが、アクセスしようとしているウェブサーバーで修正が必要です。
>
> - 501 レスポンスは、その他のヘッダーのキャッシュの指示がない限り、既定でキャッシュ可能です。

## ステータス

```
501 Not Implemented
```

## 仕様書

| 仕様書                                                           | 題名                                                          |
| ---------------------------------------------------------------- | ------------------------------------------------------------- |
| {{RFC("7231", "501 Not Implemented" , "6.6.2")}} | Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content |

## ブラウザーの互換性

以下に示す情報は、 MDN の GitHub から取得したものです。 (<https://github.com/mdn/browser-compat-data>).

{{Compat("http.status.501")}}
