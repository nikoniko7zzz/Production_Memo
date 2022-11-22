<!-- omit in toc -->
# Herokuで再度マイグレーション
いままで作ったアプリをデプロイしたherokuアプリに反映させる
- やったことを書くけど、`git push heroku master`を先にしたほうがよかった
- 次回こうしたほうがよい

<br><br>

## やったこと
[参考url: HerokuのDB再構築](https://qiita.com/katumaruno3/items/65f1c4a8b590ce760b55)
```python
# githubにpush
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % git add .
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % git commit -m"everyone_comment.html add & script.js add"
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % git push origin master

# heroku ログイン、dbリセット
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku login
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku pg:reset DATABASE_URL
# herokuでのアプリ名を入力
> ryukoku-basketball
# heroku マイグレーション（再構築）
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku run rake db:migrate
# ここでテーブルが作られないから、アレ？？っとは思った。gitをherokuにpushしてないやんw


# Herokuの再起動
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku restart --app ryukoku-basketball
# herokuにgit push
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % git push heroku master
# heroku マイグレーション
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku run python manage.py migrate
# ここでたくさんテーブルが作られる

# heroku 起動 アプリのウィンドウが開く
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku open
# ログインしようとしても入れなかったので、再度スーパーユーザーを作成する
(myvenv) niko@kunikonoMacBook-Pro basketball-tube % heroku run python manage.py createsuperuser
Running python manage.py createsuperuser on ⬢ ryukoku-basketball... up, run.4476 (Free)
Username: matsuo
Email: matsuokuniko7@gmail.com
Password:
Password (again):
Superuser created successfully.
```

1. https://ryukoku-basketball.herokuapp.com
2. ログイン
3. Tagを作成
4. 動画アイテムを5個追加する

<br><br>
__画面の確認 OK !__

で気づいたのが、ログインしなくてもコメントできたりするので、<br>
トップページにログインしないと見れないで、ログインページに飛ぶ機能を追加する(次回)