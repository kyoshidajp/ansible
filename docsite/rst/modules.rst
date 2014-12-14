モジュール
=============

.. toctree::
   :maxdepth: 4

.. _modules_intro:

イントロダクション
````````````````

Ansible には直接、または :doc:`Playbooks <playbooks>` を経由してリモートホストで実行される事のできる('モジュールライブラリ' と呼ばれる)複数のモジュールが存在します。

ユーザがモジュールを作成する事も可能です。これらの services や packages や file(本当に何でも) handle モジュールではシステムリソースの管理やシステムコマンドの実行ができます。

コマンドラインから3つの異なるモジュールを実行する方法を見てみましょう。::

    ansible webservers -m service -a "name=httpd state=started"
    ansible webservers -m ping
    ansible webservers -m command -a "/sbin/reboot -t now"

各モジュールでは引数をサポートしています。ほぼすべてのモジュールではスペースで区切られた ``key=value`` 引数をとります。いくつかのモジュールでは引数をとらず、 command/shell モジュールではシンプルに、実行したいコマンドの文字列をとります。

playbooks からの場合、Ansible モジュールはとても似た方法で実行されます。::

    - name: reboot the servers
      action: command /sbin/reboot -t now

次のように省略することもできます。::

    - name: reboot the servers
      command: /sbin/reboot -t now

すべてのモジュールは JSON フォーマットのデータを返しますが、コマンドラインからの実行でも playbooks からの実行であっても、それについてきちんと知っておく必要はありません。モジュールを自作する場合、これに注意すれば特定の言語で記述する必要はありません -- 言語は選ぶ事ができます。

モジュールには `べき等性`、つまり変更する必要がなければ変更しない、という性質があります。Ansible playbooks を使う際に、追加のタスクを実行するために 'ハンドラ' に通知する形式で '変更イベント' がトリガーできます。

各モジュールのドキュメントはコマンドラインから ansible-doc の実行で確認する事ができます。::

    ansible-doc yum

.. seealso::

   :doc:`intro_adhoc`
       /usr/bin/ansible でモジュールを使用する例
   :doc:`playbooks`
       /usr/bin/ansible-playbook でモジュールを使用する例
   :doc:`developing_modules`
       モジュールを自作する方法
   :doc:`developing_api`
       Python API でモジュールを使用する例
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア?  Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
