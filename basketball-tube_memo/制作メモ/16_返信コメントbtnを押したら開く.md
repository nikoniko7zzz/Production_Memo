<!-- omit in toc -->
# 返信ボタンを押したら、返信コメントボックスが開く
返信コメントの入力フォームと返信コメント一覧が出てくる

## Bootstrapのアコーディオンで実装
連動している動きに、comment.pkをつけて、テンプレート上で1:1でボタンの中身が連動する
```html
<div >
  <div id="panelsStayOpen-heading_{{ comment.pk }}">
    <button type="button" data-bs-toggle="collapse" data-bs-target="#panelsStayOpen-collapse_{{ comment.pk }}" aria-expanded="false" aria-controls="panelsStayOpen-collapse_{{ comment.pk }}">
    送信ボタン
    </button>
  </div>

  <div id="panelsStayOpen-collapse_{{ comment.pk }}" class="collapse" aria-labelledby="panelsStayOpen-heading_{{ comment.pk }}">
    <div>
    開閉ゾーン(返信コメントの一覧)
    </div>
  </div>
</div>
```