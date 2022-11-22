<!-- omit in toc -->
# 20_コメント編集機能-2
<!-- omit in toc -->
# 入力フォームで編集後、コメントを上書きする
コメントを上書きさせるので、どのコメントかidを取得してコメントと更新時間を上書きする<br>
DetailViewで複数のフォームを使う方法(新規登録と更新)

- [forms](#forms)
  - [base/forms/item_comment_forms.py](#baseformsitem_comment_formspy)
- [templates](#templates)
  - [templates/snippets/comment_form_update.html](#templatessnippetscomment_form_updatehtml)
- [views](#views)
  - [base/views/item_views.py](#baseviewsitem_viewspy)
    - [要約](#要約)
    - [全文](#全文)
- [git add](#git-add)

<br><br>

# forms
## base/forms/item_comment_forms.py
forms.ModelFormは使わない。<br>
項目だけ作って、非表示にする。入力フォームはhtmlにベタ書きする
```python
from django import forms

class CommentUpdateForm(forms.Form):
    """コメントアップデートフォーム（コメントid取得用）"""
    update_comment_text = forms.CharField(widget=forms.HiddenInput())
    update_id = forms.IntegerField(widget=forms.HiddenInput())
```

<br><br>

# templates
## templates/snippets/comment_form_update.html
- {{comment_update_form}}でForms.pyのCommentUpdateFormを呼んでいるが、非表示HiddenInputなので表示されない<br>
  `comment.pk`をテンプレートからviewに渡して上書きしたいので、formの中に設定しておく<br>
  forms.pyでvalue="{{ comment.pk }}"の記述の仕方がわからなかったので、forms.pyでは非表示にして、<br>
  テンプレートに直書きにした
- `<button name="CommentUpdateFormBtn">編集 送信</button>`でViewの処理を呼び出す

```html
<!-- 修正コメントフォーム -->
<form method="POST">
  {% csrf_token %}
  <!-- このフォームは非表示なので入力されない -->
  {{comment_update_form}}

  <!-- comment.idを含んで、処理する部分 -->
  <textarea
    name="update_comment_text"
    cols="40"
    rows="1"
    class="form-control my-2"
    maxlength="1000"
    required=""
  </textarea>
  <input type="hidden" name="update_id" value="{{ comment.pk }}">


  <button
    type="submit"
    class="btn btn-info btn-sm"
    name="CommentUpdateFormBtn"
  >
    編集 送信
  </button>
</form>
```


<br><br>

# views
## base/views/item_views.py
### 要約
```python
# Formの宣言
comment_update_form_class = CommentUpdateForm

# テンプレートからFormを取得
def post(self, request, *args, **kwargs):
  cuform = CommentUpdateForm(**self.get_form_kwargs())

def cuform_valid(self, cuform):
    # formの中身を受け取る
    update_comment_text = cuform.cleaned_data.get('update_comment_text')
    update_id = cuform.cleaned_data.get('update_id')
    # 上書きするオブジェクトを呼び出す
    comment = Comment.objects.get(pk=update_id)
    # 変更する内容を上書きする(コメントと更新時間)
    comment.comment_text = update_comment_text
    comment.updated_at = datetime.now()
    comment.save()
    return HttpResponseRedirect(reverse('item_detail', kwargs={'pk': self.object.pk}))

# request.POSTされたformに'CommentUpdateFormBtn'ボタンがあったら、
# バリデーションが通ったら、cuform_valid関数を実行し、テンプレートを返す
if 'CommentUpdateFormBtn' in request.POST:
    if cuform.is_valid():
        return cuform_valid(self, cuform)
    else:
        return self.render_to_response(self.get_context_data())

# テンプレートに渡す内容
def get_context_data(self, *args, **kwargs):
    context = super().get_context_data(*args, **kwargs)
    # comment_update_form_classをテンプレートに渡す
    comment_update_form = self.comment_update_form_class(self.request.GET or None)
    # 呼び出す時の名前を設定
    context.update({
        'comment_update_form,': comment_update_form,
    })
    return context
```

### 全文
```python
    class ItemDetailView(ModelFormMixin, DetailView):
        model = Item
        template_name = 'pages/item_detail.html'
        context_object_name = 'item'
        comment_form_class = CommentCreateForm
+       comment_update_form_class = CommentUpdateForm
        reply_form_class = ReplyCreateForm
        fields = () # 必要


        # no get_context_data override
        def post(self, request, *args, **kwargs):
            # first construct the form to avoid using it as instance
            cform = CommentCreateForm(**self.get_form_kwargs()) # Formを取得
+           cuform = CommentUpdateForm(**self.get_form_kwargs())
            rform = ReplyCreateForm(**self.get_form_kwargs())
            # form = self.get_form() # Formを取得
            self.object = self.get_object() # Itemのこと

            def cform_valid(self, cform):
                item = get_object_or_404(Item, pk=self.object.pk)
                comment = cform.save(commit=False)
                comment.target = item
                comment.author = self.request.user
                comment.save()
                return HttpResponseRedirect(self.get_success_url())

+           def cuform_valid(self, cuform):
+               update_comment_text = cuform.cleaned_data.get('update_comment_text')
+               update_id = cuform.cleaned_data.get('update_id')
+               comment = Comment.objects.get(pk=update_id)
+               comment.comment_text = update_comment_text # 上書き
+               comment.updated_at = datetime.now() # 上書き
+               comment.save()
+               return HttpResponseRedirect(reverse('item_detail', kwargs={'pk': self.object.pk}))

            def rform_valid(self, rform):
                item = get_object_or_404(Item, pk=self.object.pk)
                reply = rform.save(commit=False)
                reply.target = item
                reply.author = self.request.user
                reply.save()
                return HttpResponseRedirect(reverse('item_detail', kwargs={'pk': self.object.pk}))

            if 'CommentFormBtn' in request.POST:
                # print('CommentFormBtnボタン押した')
                if cform.is_valid():
                    return cform_valid(self, cform)
                else:
                    return self.render_to_response(self.get_context_data())

+           if 'CommentUpdateFormBtn' in request.POST:
+               if cuform.is_valid():
+                   return cuform_valid(self, cuform)
+               else:
+                   return self.render_to_response(self.get_context_data())

            if 'ReplyFormBtn' in request.POST:
                if rform.is_valid():
                    return rform_valid(self, rform)
                else:
                    return self.render_to_response(self.get_context_data())

        def get_success_url(self):
            return reverse('item_detail', kwargs={'pk': self.object.pk})

        # 動画に紐づいたコメント数を返す
        def get_context_data(self, *args, **kwargs):
            context = super().get_context_data(*args, **kwargs)
            item = get_object_or_404(Item, pk=self.object.pk)
            self.target = item.pk

            context['comment_count'] = Comment.objects.filter(target=self.target).count() # これもテンプレートで集計でもいいな
            comment_form = self.comment_form_class(self.request.GET or None)
+           comment_update_form = self.comment_update_form_class(self.request.GET or None)
            reply_form = self.reply_form_class(self.request.GET or None)
            context.update({
                # 'comment_form': comment_form,
                'form': comment_form,
+               'comment_update_form,': comment_update_form,
                'reply_form':reply_form,
            })
            return context
```

- [参考url: [Django] Formからの値の取得](https://qiita.com/kachuno9/items/aac61869890076f676fb)
- [参考url:【Django】FormViewとは？書き方・実例付で徹底解説](https://itc.tokyo/django/formview/)
- [参考url:【django】 formのcleaned_dataの記述てなに？](https://kuma-server.com/cleaned_data/)
- [参考url:【Django】入力Formに検証機能を実装する。(バリデーション- validation）](https://rurukblog.com/post/django-validation-url/)
- [参考url: Django SQL データベース INSERT UPDATE 追加更新方法 save() create() add() update()](https://opendata-web.site/blog/entry/22/)
- [参考url: Pythonで現在時刻・日付・日時を取得](https://note.nkmk.me/python-datetime-now-today/)
- [参考url: Djangoのget_object_or_404,get_list_or_404を使ってDBからレコードを取得する](https://www.nblog09.com/w/2019/05/11/django_or_404/)

# git add
-m'comment update add, reply update add'