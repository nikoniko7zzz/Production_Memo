<!-- omit in toc -->
# formのあれこれ(エンターキーで送信無効化・キャンセルボタンでinputを空白にする)
- エンターキーを押したらformの内容が送信されるのを無効化する
- コメントを書いたとき、キャンセルボタンを押したらフォームの中身を削除する

- [templete](#templete)
  - [templates/base.html](#templatesbasehtml)
    - [static/js/script.js](#staticjsscriptjs)
  - [templates/pages/item_detail.html](#templatespagesitem_detailhtml)
- [static](#static)
  - [static/js/script.js](#staticjsscriptjs-1)

<br><br>

## templete
static<br>
├── css<br>
│   ├── reset.css<br>
│   ├── sanitize.css<br>
│   └── style.css<br>
└── js　　追加<br>
     └── script.js　　追加<br>

### templates/base.html
```html
    <head>
      ...
+     <script src="{% static 'js/script.js' %}"></script>
    </head>
```

#### static/js/script.js
jsファイルが読み込まれているか確認
```js
console.log('hello')
```

### templates/pages/item_detail.html
```html
      <form method="POST">
        {% csrf_token %}
        {{ form.comment_text }}
        <div class="text-right my-2">
+         <button type="text" class="btn btn-info btn-sm" onclick="clearText()">キャンセル</button>
          <button type="submit" class="btn btn-info btn-sm active">コメント</button>
        </div>
      </form>
```

いま現在の状態↓<br>
コメントのinputに`id="id_comment_text"` が、できているので、
```html
<form method="POST">
  <input type="hidden" name="csrfmiddlewaretoken" value="GcWwCjf9cYzCDPAncB6XB7VPbgFF0dgKZzEhGFJfw9LH2L7ywnPH5arhCYQOaBkL">
  <input
    type="text"
    name="comment_text"
    placeholder="コメントする"
    class="form-control"
    maxlength="1000"
    required=""
    id="id_comment_text">
  <div class="text-right my-2">
    <button type="text" class="btn btn-info btn-sm" onclick="clearText()">キャンセル</button>
    <button type="submit" class="btn btn-info btn-sm active">コメント</button>
  </div>
</form>
```

##
## static
### static/js/script.js
```js
// エンターキーの無効化 エンターキーでformを送信するのを防ぐ
document.onkeypress = function(e) {
  // エンターキーだったら無効にする
  if (e.key === 'Enter') {
    return false;
  }
}

// キャンセルボタンを押した時、inputの中身を空白にする
function clearText() {
	var textForm = document.getElementById("id_comment_text");
  textForm.value = '';
}
```

__ばっちりです OK!__

