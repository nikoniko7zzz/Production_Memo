python3 -m venv myvenv
source myvenv/bin/activate
python3 -V
Python 3.10.7
deactivate
pip freeze > requirements.txt
pip install -r requirements.txt

python manage.py runserver

(myvenv) basketball-tube % python manage.py makemigrations base
(myvenv) basketball-tube % python manage.py migrate

```python
# 先程作成したユーザー、データベースへアクセス
(myvenv) basketball-tube % psql -U user_basketball_tube -d db_basketball_tube
psql (14.5 (Homebrew))
Type "help" for help.
# これが出て来ればOK
db_basketball_tube=>
# データベースのテーブル一覧
db_basketball_tube=> select schemaname, tablename, tableowner from pg_tables;


{% comment "下記の部分は一時的にコメントアウト" %}
<h1>タイトル</h1>
<p>本文</p>
{% endcomment %}

{# コメントアウト非表示部分 #}Hello # Helloのみ表示

<iframe width="560" height="315" src="https://www.youtube.com/embed/suzcpRPH7MU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
</iframe>
<iframe width="560" height="315" src="https://www.youtube.com/embed/5e14TVcoaRg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


作成アカウント
matsuo
matsuokuniko7@gmail.com
0925kuni
赤木 剛憲

teacher
dev2022teacher@gmail.com
all
安西先生

hanamichi
dev2022hanamichi@gmail.com
user1
桜木 花道

rukawa
dev2022rukawa@gmail.com
user2
流川 楓



