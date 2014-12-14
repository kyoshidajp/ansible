イントロダクション
==================

.. _about_playbooks:

Playbooks について
```````````````````

Playbook はタスク実行モードとは全く異なる方法で、とても強力です。

簡単にいえば、playbooks は複雑なアプリケーションのデプロイに特化したものとも、既存のいずれのものとも違い、本当にシンプルな構成管理と複数マシンへのデプロイシステムのための基盤です。

Playbooks は設定を宣言出来ますが、任意の順序で手動で実施することも可能です。　タスクは同期または非同期に実行できます。

アドホックなタスクに/usr/bin/ansible プログラムを実行可能ですが、playbooks はソース管理して、構成を push したり、リモートシステムの構成が仕様通りになっていることを保証するのに適しています。

たくさんのテクニックを解説した playbooks が `ansible-examples リポジトリ <https://github.com/ansible/ansible-examples>`_ にあります。ドキュメントとは別の方法としておすすめします。

playbooks を学ぶ出発点はたくさんありますが、このセクションで学んだあと、ドキュメントインデックスに戻ってきます。

.. _playbook_language_example:

Playbook の例
`````````````````````````

Playbooks は最小限の構文で、意図的にプログラム言語やスクリプトとは異なる YAML フォーマット(参照 :doc:`YAMLSyntax`) で表現されます。

各 playbook はリストの中のひとつ以上の 'plays' で作成されます。

play の目標はホストのグループを定義されたロールに関連付け、タスクと呼んでいるものにマップすることです。基本的なレベルでは、タスクは前の章で学んだ Ansible モジュールを呼び出す以上の何ものでもありません。

複数の 'plays' が記述された playbook によって、ウェブサーバグループのマシンへのデプロイ、データベースサーバグループのマシンでの実行、そしてウェブサーバグループにもどってコマンドを実行する、などといった事が可能です。

"plays" は多少スポーツと似ています。異なったもので、システムに影響をおよぼすたくさんの play を持てます。個々の状態やモデルを定義するのでなく、別のタイミングで別の play を実行できます。

まずはじめに、次の playbook には一つの play が含まれています。::

    ---
    - hosts: webservers
      vars:
        http_port: 80
        max_clients: 200
      remote_user: root
      tasks:
      - name: ensure apache is at the latest version
        yum: pkg=httpd state=latest
      - name: write the apache config file
        template: src=/srv/httpd.j2 dest=/etc/httpd.conf
        notify:
        - restart apache
      - name: ensure apache is running
        service: name=httpd state=started
      handlers:
        - name: restart apache
          service: name=httpd state=restarted

次から、playbook での様々な機能について注目していきます。

.. _playbook_basics:

基本
``````

.. _playbook_hosts_and_users:

ホストとユーザ
+++++++++++++++

playbook の実行において、インフラ上のどのマシンをターゲットにして、どのユーザで(タスクと呼ばれる)ステップを実行するか設定します。

`hosts` 行は :doc:`intro_patterns` に記述されているように、コロンで区切られた、ひとつ以上のグループホストのパターンのリストになります。`remote_user` はユーザのアカウント名です。::

    ---
    - hosts: webservers
      remote_user: root

.. note::

    `remote_user` パラメータは正式には `user` になります。Ansible 1.4 で `user`　モジュール(リモートシステムにてユーザを作成するのに使われる)と区別するために名前が変更になりました。

リモートユーザはタスクごとに指定可能です。::

    ---
    - hosts: webservers
      remote_user: root
      tasks:
        - name: test connection
          ping:
          remote_user: yourname

.. note::

  `remote_user` パラメータは 1.4 で追加されました。


sudo での実行もサポートしています。::

    ---
    - hosts: webservers
      remote_user: yourname
      sudo: yes

また、sudo をタスク個別に指定することも可能です。::

    ---
    - hosts: webservers
      remote_user: yourname
      tasks:
        - service: name=nginx state=started
          sudo: yes


ログイン後、root とは異なるユーザで sudo を実行する事もできます。::

    ---
    - hosts: webservers
      remote_user: yourname
      sudo: yes
      sudo_user: postgres

sudo のパスワードが必要であれば、`ansible-playbook` を ``--ask-sudo-pass`` (`-K`) オプションをつけて実行します。
sudo で playbook を実行してハングした場合、 `Control-C` を実行して、再度 `-K` オプションとともに実行します。

.. important::

   `sudo_user`　
   `sudo_user` を root 以外のユーザにした場合、モジュールの引数は /tmp のランダムな一時ファイルに書き込まれます。これらはコマンドが実行された後、すぐに削除されます。これは 'bob' から　'timmy' へ sudo した際に出現するもので、'bob' から 'root' になる場合や、直接 'bob' から 'root' でログインした場合には起こりません。もし、このデータが即座に読まれる(書き込みは不可)ことが心配なら、暗号化されていないパスワードによる`sudo_user`セットを取り除く必要があります。他のケースでは '/tmp' は使用されず、play にはなりません。Ansible も考慮して、パスワードのパラメータをログに残さないようになっています。

.. _tasks_list:

タスクリスト
++++++++++++

各 play にはタスクのリストがあります。タスクは次のタクスを実行する前に、ホストパターンにマッチするマシンに対して順番に実行されます。play の中では、すべてのホストが同じタスクディレクティブを取得しようとする事を理解するのが重要です。タスク選択したホストをマッピングすることが play の目的です。

上から下に playbook を実行している時、失敗したタスクのホストは playbook 全体のローテーションからは外されます。失敗した場合は単純に playbook ファイルを修正し、再実行してください。

各タスクの目的はモジュールを指定された引数で実行することです。変数はモジュールの引数として使用できます。

モジュールには、再度実行してもシステムが期待する状態となる 'べき等性' という性質があります。これにより、playbook を何度も実行できてとても安全です。必要がなければ何も変更はしません。

`command` と `shell` モジュールは通常同じコマンドを実行しますが、'chmod' や 'setsebool' などのような問題ありません。これらのモジュールもべき等性を持つために、'creates' フラグもあります。

すべてのタスクには `name` があり、その値は playbook を実行した時の出力に含まれます。これは人のためにあり、各タスクのステップを表す記述であればよいでしょう。`name` が指定されていなければ、'action' に送られた文字列が出力されます。

タスクはレガシーな "action: module options" フォーマットで指定する事が可能ですが、"module: options" フォーマットを使用する事を推奨します。
この推奨するフォーマットはドキュメント中で使用されていますが、playbooks の古いフォーマットを目にする事もあるでしょう。

次は基礎的なタスクで、たいていのモジュールと同様 key=value の引数をとるものです。::

   tasks:
     - name: apache が実行されている
       service: name=httpd state=running

`command` と `shell` モジュールは引数のリストだけを取るモジュールで、key=value の形式ではありません。あなたが期待するような動作となるには単純に次の様にします::

   tasks:
     - name: selinux を無効にする
       command: /sbin/setenforce 0

command と shell モジュールは戻り値を考慮します。コマンド成功時の終了コードが 0 でなければ、このようにします。::

   tasks:
     - name: コマンドを実行し、結果を無視する
       shell: /usr/bin/somecommand || /bin/true

別の方法です。::

   tasks:
     - name: コマンドを実行し、結果を無視する
       shell: /usr/bin/somecommand
       ignore_errors: True

アクションの行が長い場合、スペースの部分で改行してインデントで続きの行を記述することができます。::

    tasks:
      - name: ansbile inventory ファイルをクライアントにコピーする
        copy: src=/etc/ansible/hosts dest=/etc/ansible/hosts
                owner=root group=root mode=0644

アクションの行では変数を使用する事ができます。変数 'vhost' が定義されている場合、次の様に記述できます。::

   tasks:
     - name: {{ vhost }} のバーチャルホストファイルを作成
       template: src=somefile.j2 dest=/etc/httpd/conf.d/{{ vhost }}

これらの変数は後ほど登場する template で使用することができます。

'include' ディレクティブを使ってタスをタスクを分割するほうが理にかなっているでしょうが、現在、非常に基本的な playbook ではすべてのタスクは play の中に直接記述しています。そのことについては少し後で説明します。

.. _action_shorthand:

アクションの省略記法
`````````````````````

.. versionadded:: 0.8

0.8 以降で次の様な記述が使われる様になりました。::

    template: src=templates/foo.j2 dest=/etc/foo.conf

初期のバージョンでは次の様な記述のみ有効なので注意してください。::

    action: template src=templates/foo.j2 dest=/etc/foo.conf

古い記述方式が新しいバージョンでも動作するように計画的に利用してください。

.. _handlers:

ハンドラ: 変更によって実行する操作
``````````````````````````````````````

すでに紹介した通り、モジュールには 'べき等性' があり、リモートシステムで変更があった際に連携をとる事ができます。Playbooks にはこれを認識し、変更に対応するのに使える基本的なイベントシステムがあります。


これらの '通知' アクションは playbook の中の各タスクブロックの終わりでトリガとなり、複数の異なるタスクによって通知があっても、トリガとなるのは一度だけです。

例えば、複数のリソースは設定ファイルが変更されているので、apache を再起動する必要がありますが、不要な再起動を避けるため、apache が知らされるのは一度だけです。

次はファイルの内容が変更された際に、２つのサービスを再起動する例ですが、ファイルの変更があった時のみ実行されます。::

   - name: template configuration file
     template: src=template.j2 dest=/etc/foo.conf
     notify:
        - restart memcached
        - restart apache

'notify' セクションにリストされているのはハンドラと呼ばれるタスクです。

ハンドラはタスクのリストで、実際には通常のタストとは違いがなく、名前で参照されます。ハンドラは notify に通知するものです。ハンドラを何も通知しないと実行されません。どれだけの数のハンドラに通知されたかには関係なく、特定の play の中のすべてのタスクが完了した後に、一度だけ実行されます。

次はハンドラセクションの例です。::

    handlers:
        - name: memcached を再起動
          service:  name=memcached state=restarted
        - name: apache を再起動
          service: name=apache state=restarted

ハンドラはサービスの再起動やシステムの再起動に最もよく使われます。ほとんどの場合、必要になる事はないかもしれません。

.. note::
   ハンドラの通知は常に記述された順番で実行される事に注意してください。

ロールについては後ほど登場します。ハンドラが 'pre_tasks'、'roles'、'tasks'、'post_tasks' の間で自動的に処理されることは注目に値します。もしすべてのハンドラコマンドをフラッシュしたい場合、1.2 以降であれば次のように記述します。::

    tasks:
       - shell: some tasks go here
       - meta: flush_handlers
       - shell: some other tasks

上の例では 'meta' 文に到達した時に、キューに登録されているハンドラは先に処理されます。これは少しニッチなケースですが、時々便利なことがあります。

.. _executing_a_playbook:

Playbook の実行
````````````````````

playbook の構文についてはすでに学びましたが、それをどうやって実行するのでしょうか？それはとてもシンプルです。
並列処理レベル10で playbook を実行しましょう。

    ansible-playbook playbook.yml -f 10

.. _ansible-pull:

Ansible-Pull
````````````

Ansible のアーキテクチャを逆にして、つまり、リモートノードに対して設定を push する代わりに、リモートノードが中央リポジトリからチェックアウトにする事も可能です。

Ansible-pull は git から設定命令のリポジトリをチェックアウトし、ansible-playbook を内容に反して実行する小さなスクリプトです。

チェックアウト場所を負荷分散すれば、ansible-pull は実質、無限にスケールします。

詳細は ``ansible-pull --help`` を実行します。

また、次の内容も参考になります。 `clever playbook <https://github.com/ansible/ansible-examples/blob/master/language_features/ansible_pull.yml>`_ - push モードから crontab 経由で ansible-pull に設定する

.. _tips_and_tricks:

Tips とコツ
```````````````

実行されたノードやどのように実行されたかの概要は playbook を実行した最後を確認をしてください。一般的な失敗や、致命的な失敗である "到達不能" などがそれぞれカウントされています。

失敗したモジュールだけでなく、成功からもより詳細について確認したい場合は、 ``--verbose`` を付けて実行します。これは Ansible 0.5 以降で利用可能です。

cowsay パッケージがインストールされていれば Ansible playbook の出力は大幅にアップグレードします。試してください！

playbook で実行前に対象となるホストを確認するには次のようにします。

    ansible-playbook playbook.yml --list-hosts

.. seealso::

   :doc:`YAMLSyntax`
       YAML の文法について学ぶ
   :doc:`playbooks_best_practices`
       実際に playbooks を管理するティップスについて
   :doc:`index`
       playboos の特別なトピックスのためにドキュメントインデックスに戻る
   :doc:`modules`
       モジュールについて学ぶ
   :doc:`developing_modules`
       モジュールを作成して Ansible を拡張する方法について学ぶ
   :doc:`intro_patterns`
       ホストを選択する方法について学ぶ
   `Github examples directory <https://github.com/ansible/ansible-examples>`_
       完全なエンドツーエンドの playbook の例
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア? Google Groups メーリングリスト
