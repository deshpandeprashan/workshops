# 演習 2.4 - Survey 機能

**Read this in other languages**: ![uk](../../../images/uk.png) [English](README.md),  ![japan](../../../images/japan.png) [日本語](README.ja.md).

テンプレート構成ビューの **SURVEYの追加** ボタンに気付いたかもしれません。Survey は、**テンプレート**が**ジョブ**として起動した時に利用する変数の値を入力する簡単なフォームです。  

先ほどの演習で、全てのホストに Apache をインストールしました。次にこれを拡張します。  

- Jinja2テンプレートを持つ適切なロールを使用して、`index.html` ファイルをデプロイします  

- Survey を含むジョブテンプレートを作成し、 `index.html` テンプレート用の情報を収集します  

- ジョブテンプレートを起動します  

さらに、このロールでは、他の演習で失敗していることも想定し、Apache 構成が適切にセットアップされていることも確認します。  

> **ヒント**  
> 
> Survey は入力するデータの単純なクエリのみを提供します。動的なデータに基づくクエリやネストされたメニューには対応していません。  

## プロジェクトを作成する  

Playbook と Jinja2 テンプレートを持つ Roles は Github リポジトリ

**https://github.com/ansible/workshop-examples** の `rhel/apache` にあります。  

Github UIに移動して、 `apache_role_install.yml` の中身を見てみてください。単に Role を参照しているだけです。 Role は `roles/role_apache` サブディレクトリに存在します。Role 内の jinja2 テンプレート `templates/index.html.j2` 内に定義された二つの変数を確認します。変数は `{{…?}}` で定義されるんでしたね。また、メインのタスクを担う、 `tasks/main.yml` の中で、template からファイルをコピーするタスクをチェックします。この Playbook は何をやっているのでしょう、分かりますか？  テンプレート (**src**) から、対象ホスト上にファイル (**dest**) としてコピーしています。この際、コピー先のファイルには変数に値が入力されます。値を入力するのは、そう、 Survey インターフェースです。  

Role は、Apache の設定内容もデプロイします。前の章で行われた全ての変更が上書きされ、今回の演習が問題なく実行できることを保証するためのものです。  

## Survey を作成  

Survey を含むジョブテンプレートを作成します。  

### テンプレートの作成  

- 左のメニューで**テンプレート**を選択し、![plus](images/green_plus.png) ボタンをクリック。 **ジョブテンプレート**を選択します。  

- **名前** Create index.html

- テンプレートを次のように構成してください！
  
    - 既存の**プロジェクト** の中から今回の件に適応するものを選択。さらに適切な **Playbook**を選びます。
  
    - `node1` のみで実行されるようにインベントリーを定義します
  
    - 権限昇格を行います

設定方法は分かりますか？答えは以下の通りです。ここまで演習を積まれた皆さんならそんなに難しくないと思います。♪  

> **回答**

- **名前:** Create index.html

- **ジョブタイプ:** 実行

- **インベントリー:** Webserver  

- **プロジェクト:** Ansible Workshop Examples  

- **PLAYBOOK:** `rhel/apache/apache_role_install.yml`  

- **認証情報:** Workshop Credentials  

- **オプション:** 権限昇格の有効化

- **保存**をクリック  

> **注意**  
>
> **まだジョブテンプレートを実行しないでください！！　そう、まだ変数の値が定義されてません！！**  

### Survey を追加する  

- ジョブテンプレートの中で、**SURVEY の追加** ボタンをクリックします  

- **Survey プロンプトの追加** フォームで以下を入力します  
  
    - **プロンプト:** First Line  
  
    - **回答の変数名:** `first_line`  
  
    - **回答タイプ:** テキスト  

- **+ADD**をクリックします。  

- 同じように2つ目の変数入力フォームを定義します 
  
    - **プロンプト:** Second Line
  
    - **回答の変数名:** `second_line`
  
    - **回答タイプ:** テキスト

- **+ADD**をクリックします  

- **保存** をクリックし、 Survey を保存します

- **保存** をクリックし、ジョブテンプレートを保存します

## テンプレートを起動します

作成したジョブテンプレート **Create index.html** を起動してみます。  

実際の起動の際に、Survey は 2つの変数について入力を要求します。お好きなテキストを入力して、「次へ」をクリックします。次のウィンドウに入力した値が表示されます。問題なければ、「起動」をクリックしてジョブを実行します。  

> **ヒント**
> 
> 入力した 2つの値がジョブ実行画面の左下の **追加変数**に表示されていることを確認します。  

ジョブが完了したら、Apache ホームページを確認してください。確認するのは、そう、`node1` です。 Tower Server の SSH コンソールの curl コマンドで確認したもよいですし、ブラウザで直接 node1 に接続してみてもOKです。  

```bash
$ curl http://<node1>
<body>
<h1>Apache is running fine</h1>
<h1>This is survey field "First Line": line one</h1>
<h1>This is survey field "Second Line": line two</h1>
</body>
```
この index.html ファイルが Playbook と Survey によってどのように作成されたのか、よく理解しておて下さい。  

## さらに次のタスクを実行ください  

タスクのリストは次のとおりです。  

> **注意**
> 
> **次の章で利用しますので、以下の手順は必ず完了してください！**

- インベントリー `Webserver` に、 `node2` と `node3` を追加します  

- ジョブテンプレート `Create index.html` を再度実行します。

- Curl コマンドもしくはブラウザで `node2`, `node3` にアクセスし、結果を確認します。

----

[Ansible Tower ワークショップ表紙に戻る](../README.ja.md#section-2---ansible-towerの演習)
