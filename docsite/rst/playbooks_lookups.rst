ルックアップを使う
================

ルックアッププラグインは外部のソースから Ansible でデータのアクセスを可能にします。これらのプラグインは Ansible が操作しているマシンでの実行になり、ファイルシステムの読み取りだけでなく、外部のデータストアやサービスを含みます。
これらの値は Ansible の標準テンプレートシステムを使うことで有効になり、典型的な利用はこれらのシステムからの情報を変数またはテンプレートでの読み取りになります。

.. note:: これは高度な機能である事を考慮すると、多くのユーザはおそらくこれらの機能に依存することはないでしょう。

.. note:: ルックアップはローカルコンピュータで実行され、リモートコンピュータではありません。

.. contents:: トピックス

.. _getting_file_contents:

ルックアップのイントロ: ファイルの内容を取得する
```````````````````````````````````````

ファイルルックアップはもっとも基礎的なルックアップの種類です。

contents は以下のようにファイルシステムのデータを読み取ります。::

    - hosts: all
      vars:
         contents: "{{ lookup('file', '/etc/foo.txt') }}"

      tasks:

         - debug: msg="foo.txt の値は {{ contents }}"

.. _password_lookup:

passowrd ルックアップ
```````````````````

.. note::

    A great alternative to the password lookup plugin, if you don't need to generate random passwords on a per-host basis, would be to use :doc:`playbooks_vault`.  Read the documentation there and consider using it first, it will be more desirable for most applications.

``password`` はランダムなプレーンテキストのパスワードを生成し、指定されたファイルパスのファイルに保存します。


(crypted save に関するドキュメントは保留中です)

すでにファイルが存在すれば、内容を戻し、with_file のように動作します。ファイルパスで "{{ inventory_hostname }}" の様な変数の使用はホスト（'host_vars' 変数でもっともシンプルなパスワード管理）ごとのランダムなパスワードがセットアップするのに使用されます。

ランダムにミックスされた大文字または小文字の ASCII 文字列、数字の 0-9、句読点 (". , : - _") を含むパスワードを生成します。生成するパスワード文字列のデフォルトは 20 です。
この長さは追加パラメータによって変更することが出来ます。::

    ---
    - hosts: all

      tasks:

        # ランダムなパスワードで mysql ユーザを作成:
        - mysql_user: name={{ client }}
                      password="{{ lookup('password', 'credentials/' + client + '/' + tier + '/' + role + '/mysqlpassword length=15') }}"
                      priv={{ client }}_{{ tier }}_{{ role }}.*:ALL

        (...)

.. note:: ファイルがすでに存在すれば、それにデータは書き込まれません。ファイルに内容があった場合、パスワードとして読まれます。空のファイルであればパスワードは空文字列となります。

バージョン 1.4 より、password は生成するパスワードにカスタム文字列を定義する "chars" パラメータを指定できます。カンマで区切られたリストで文字列モジュール属性（ascii_letters,digits, etc）またはリテラルが使用されます。::

    ---
    - hosts: all

      tasks:

        # ascii 文字のみのランダムなパスワードで mysql ユーザを作成:
        - mysql_user: name={{ client }}
                      password="{{ lookup('password', '/tmp/passwordfile chars=ascii_letters') }}"
                      priv={{ client }}_{{ tier }}_{{ role }}.*:ALL

        # 数値のみのランダムなパスワードで mysql ユーザを作成:
        - mysql_user: name={{ client }}
                      password="{{ lookup('password', '/tmp/passwordfile chars=digits') }}"
                      priv={{ client }}_{{ tier }}_{{ role }}.*:ALL

        # 異なる文字のランダムなパスワードで mysql ユーザを作成:
        - mysql_user: name={{ client }}
                      password="{{ lookup('password', '/tmp/passwordfile chars=ascii_letters,digits,hexdigits,punctuation') }}"
                      priv={{ client }}_{{ tier }}_{{ role }}.*:ALL

        (...)

カンマを入力するには２つのカンマ ',,' を使い -　なるべく最後にします。クォートとダブルクォートはサポートしていません。

.. _more_lookups:

他のルックアップ
````````````````

.. note:: この機能が使用されるのはとてもまれです。このセクションはスキップしてもよいかもしれません。

.. versionadded:: 0.8

変数 *lookup plugins* はデータを繰り返す追加の方法です。:doc:`Loops <playbooks_loops>` には多数の型のコレクションを使う方法があります。しかし、シェルコマンドやキーバリューストアのようなリモートソースからデータを取得するのに使用する事ができます。このセクションではルックアッププラグインを可能な限り紹介します。

いくつかの例です::

    ---
    - hosts: all

      tasks:

         - debug: msg="{{ lookup('env','HOME') }} は環境変数"

         - debug: msg="{{ item }} is a line from the result of this command"
           with_lines:
             - cat /etc/motd

         - debug: msg="{{ lookup('pipe','date') }} はこのコマンドの生の結果"

         - debug: msg="{{ lookup('redis_kv', 'redis://localhost:6379,somekey') }} Redis での somekey の値"

         - debug: msg="{{ lookup('dnstxt', 'example.com') }} は example.com の DNS TXT レコード"

         - debug: msg="{{ lookup('template', './some_template.j2') }} はこのテンプレートを評価した値"

         - debug: msg="{{ lookup('etcd', 'foo') }} はローカルで etcd を実行した値"

選択方法として、変数のルックアッププラグインを割り当てるか、または別の使用が可能です。このマクロはタスク（またはテンプレート）で実行するたびに評価されます::

    vars:
      motd_value: "{{ lookup('file', '/etc/motd') }}"

    tasks:

      - debug: msg="motd の値は {{ motd_value }}"

.. seealso::

   :doc:`playbooks`
       playbook の紹介
   :doc:`playbooks_conditionals`
       playbook での条件文
   :doc:`playbooks_variables`
       変数に関する全て
   :doc:`playbooks_loops`
       playbook でのループ
   `User Mailing List <http://groups.google.com/group/ansible-devel>`_
       質問がありますか？google group で確認しましょう！
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャネル
