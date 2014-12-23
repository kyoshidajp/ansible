よくある質問とその答え
==========================

ここではよく質問される問いとその答えを紹介しています。

.. _users_and_ports:

ログインにユーザアカウントとポート番号が必要な異なるマシンに対して操作するにはどうすればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

インベントリファイルでインベントリ変数を設定するのが最も簡単な方法です。

例えば、これらのホストは異なるユーザ名とポート番号であると仮定します。::

    [webservers]
    asdf.example.com  ansible_ssh_port=5000   ansible_ssh_user=alice
    jkl.example.com   ansible_ssh_port=5001   ansible_ssh_user=bob

必要であれば、接続タイプを指定することも可能です。::

    [testcluster]
    localhost           ansible_connection=local
    /path/to/chroot1    ansible_connection=chroot
    foo.example.com
    bar.example.com

You may also wish to keep these in group variables instead, or file in them in a group_vars/<groupname> file.
変数を編成する方法の詳細な情報については残りのドキュメントを参照してください。

.. _use_ssh:

接続を再利用し、Kerberos SSH を有効にする、または Ansible がローカル SSH 設定ファイルに注意を払うにはどうすればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

設定ファイルでデフォルトの接続タイプを 'ssh' に変更するか、'-c ssh' を指定して python paramiko ライブラリの代わりにネイティブ OpenSSH を使用します。Ansible 1.2.1 以降では、OpenSSH が ControlPersist をオプションとしてサポートするのに十分なバージョンであればデフォルトで 'ssh' が使用されます。

Paramiko は starting out には great ですが、OpenSSH タイプではたくさんのアドバンスドオプションが提供されています。ControlPersist をサポートするのに十分なバージョンのマシンから Ansible を実行できます。古いクライアントを管理できます。RHEL 6、CentOS 6、 SLES 10 または SLES 11 の OpenSSH のバージョンはやや古いので、Fedora または openSUSE クライアントから実行するか、parmiko を使用してください。

EL box で最初に Ansible をインストールした場合、paramiko がデフォルトとなり、新しいユーザにとってはよりよい経験となります。

.. _ec2_cloud_performance:

EC2 内部の管理でスピードアップするにはどうすればよいのでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

ラップトップから速い EC2 マシンの管理はしないでください。まず最初に管理ノードに接続し、そこから Ansible を実行してください。

.. _python_interpreters:

リモートマシンにて /usr/bin/python に Python 2.X が存在しない場合、どのように適用 Python パスを操作すればよいでしょうか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

ansible モジュールをどんな言語で記述できるといっても、ほとんどのモジュールは Python で記述されていて、そのうちのいくつかはコアです。

通常 Ansible はリモートシステム上で Python バージョン 2.X、明記されていれば 2.4 以降が /usr/bin/python にあると想定します。

inventory の変数 'ansible_python_interpreter' によって Ansible が Python モジュールを実行する際に使用するインタプリタのパスを自動で置き換えます。したがって、/usr/bin/python がシステム上に存在しない場合、Python 2.X インタプリタとして好みの python を指定する事ができます。

いくつかの Linux オペレーティングシステムでは、Arch のように、デフォルトで Python 3 しかインストールされていないものがあります。これは要件を満たさず、 Python 3 にてモジュールを実行しようとするとシンタックスエラーが発生します。Python 3 は基本的に Python 2 と同じではありません。Ansible モジュールは現状 Enterprise Linux 5 が配置されたユーザのために古い Python をサポートする必要があるため、Python 3.0 で実行できるように移植されていません。管理ホストに Python 2 をインストール可能で、これは問題にはなりません。

Python 3.0 のサポートはその利用が主流になれば行われるでしょう。

python モジュールのシェバン行を勝手に置き換えてはいけません。Ansible はデプロイ時にこれを自動的に行います。

.. _use_roles:

再使用/再配布可能なコンテンツを作成するベストな方法は何ですか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

まだ行っていなければ、playbook ドキュメントの "Roles" についてすべて読んでください。これは playbook コンテンツ作成の手助けとなり、コンテンツを共有する git サブモジュールのようによく動作します。

これらのプラグインのいくつかが奇妙ににみえる場合は、Ansible を拡張する方法について詳細な情報のために API ドキュメントを参照してください。

.. _configuration_file:

どこにある設定ファイルが有効で、それをどうやって設定する事ができますか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


See :doc:`intro_configuration`.

.. _who_would_ever_want_to_disable_cowsay_but_ok_here_is_how:

cowsay をどうやって無効にできますか？
+++++++++++++++++++++++++++++++++++++

cowsay がインストールされていれば、Ansible は playbook の実行時にあなたの一日をもっとハッピーにします。プロフェッショナルな cow-free 環境で動作させると決めた場合、cowsay をアンインストールするか、環境変数を次のように設定します。::

    export ANSIBLE_NOCOWS=1

.. _browse_facts:

ansible\_ 変数のすべてのリストを確認するにはどうすればよいでしょうか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Ansible はデフォルトでは管理下にあるマシンについて "ファクト" をかき集め、それらのファクトは Playbook やテンプレートでアクセスできます。マシンについて得られる事実のすべてのリストを見るためには、アドホックアクションとして "setup" モジュールを実行することができます。::

    ansible -m setup hostname

これは特定のホストから得られたファクトのすべてのディクショナリを出力します。

.. _host_loops:

How do I loop over a list of hosts in a group, inside of a template?
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

A pretty common pattern is to iterate over a list of hosts inside of a host group, perhaps to populate a template configuration
file with a list of servers. To do this, you can just access the "$groups" dictionary in your template, like this::

    {% for host in groups['db_servers'] %}
        {{ host }}
    {% endfor %}

If you need to access facts about these hosts, for instance, the IP address of each hostname, you need to make sure that the facts have been populated. For example, make sure you have a play that talks to db_servers::

    - hosts:  db_servers
      tasks:
        - # doesn't matter what you do, just that they were talked to previously.

Then you can use the facts inside your template, like this::

    {% for host in groups['db_servers'] %}
       {{ hostvars[host]['ansible_eth0']['ipv4']['address'] }}
    {% endfor %}

.. _programatic_access_to_a_variable:

変数名にプラグマティックにアクセスするにはどうすればよいでしょうか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

An example may come up where we need to get the ipv4 address of an arbitrary interface, where the interface to be used may be supplied
via a role parameter or other input.  変数名は次のように文字列を一緒に追加する事によって作られます。::

    {{ hostvars[inventory_hostname]['ansible_' + which_interface]['ipv4']['address'] }}

The trick about going through hostvars is necessary because it's a dictionary of the entire namespace of variables.  'inventory_hostname'
is a magic variable that indicates the current host you are looping over in the host loop.

.. _first_host_in_a_group:

グループの最初のホストの変数にアクセスするにはどうすればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

What happens if we want the ip address of the first webserver in the webservers group?  Well, we can do that too.  Note that if we
are using dynamic inventory, which host is the 'first' may not be consistent, so you wouldn't want to do this unless your inventory
was static and predictable.  (If you are using :doc:`tower`, it will use database order, so this isn't a problem even if you are using cloud
based inventory scripts).

Anyway, here's the trick::

    {{ hostvars[groups['webservers'][0]]['ansible_eth0']['ipv4']['address'] }}

Notice how we're pulling out the hostname of the first machine of the webservers group.  If you are doing this in a template, you
could use the Jinja2 '#set' directive to simplify this, or in a playbook, you could also use set_fact:

    - set_fact: headnode={{ groups[['webservers'][0]] }}

    - debug: msg={{ hostvars[headnode].ansible_eth0.ipv4.address }}

Notice how we interchanged the bracket syntax for dots -- that can be done anywhere.

.. _file_recursion:

ターゲットホストにファイルを再帰的にコピーするにはどうすればよいでしょうか？
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

"copy" モジュールには recursive パラメータがありますが、膨大な数のファイルに何か行いたい場合は、rsync をラップした "synchronize" モジュールも検討してください。これらのモジュールについてはモジュールインデックスから参照してください。

.. _shell_env:

シェル環境変数にアクセスするにはどうすればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

存在する変数にアクセスしたい場合、'env' ルックアッププラグインを使用します。例えば、管理マシンの HOME 環境変数にアクセスするには::

   ---
   # ...
     vars:
        local_home: "{{ lookup('env','HOME') }}"

環境変数に設定するには、環境について Advanced Playbooks を参照してください。

Ansible 1.4 は 'ansible_env' 変数の facts を経由してリモート環境変数を有効にします。::

   {{ ansible_env.SOME_VARIABLE }}

.. _user_passwords:

user モジュールのために暗号化されたパスワードをどうやって生成すればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

たいていの Linux システムにおいて mkpasswd ユーティリティが有効で、たくさんのオプションがあります。::

    mkpasswd --method=SHA-512

このユーティリティがシステムにインストールされていない場合（例えば、 OS X を使用している場合）、Python を使ってより簡単にパスワードを生成することができます。最初に、`Passlib <https://code.google.com/p/passlib/>`_ パスワードハッシュライブラリがインストールされた状態にします。

    pip install passlib

ライブラリがインストールされた状態であれば、SHA512 パスワード値を次のように生成できます。::

    python -c "from passlib.hash import sha512_crypt; import getpass; print sha512_crypt.encrypt(getpass.getpass())"

.. _commercial_support:

Ansible のトレーニングを受けたり商用のサポートを受けられますか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

はい！ オンラインサポートは `Guru オファー <http://www.ansible.com/ansible-guru>`_ を参照してください。また、より進んだ内容のために `サービスページ <http://www.ansible.com/ansible-services>`_ を読んだり、メール `info@ansible.com <mailto:info@ansible.com>`_ もあります。

.. _web_interface:

ウェブインターフェース / REST API / などはありますか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

はい！ Ansible, Inc では Ansible をよりパワフルかつ簡単に使用するための製品があります。:doc:`tower` を参照してください。

.. _docs_contributions:

ドキュメントへの変更をどうやって提出すればよいですか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++

すばらしい質問です！ Ansible のドキュメントはメインのプロジェクト git リポジトリで管理されていて、貢献するための指示は  `viewable on GitHub <https://github.com/ansible/ansible/blob/devel/docsite/README.md>`_ の README ドキュメントにあります。ありがとう！

.. _keep_secret_data:

playbook に秘密のデータを持たせるにはどうすればよいでしょうか？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Ansible 内に秘密のデータを持たせて、それを公開して共有したり、ソース管理で保持し続けたい場合、:doc:`playbooks_vault` を参照してください。

.. _i_dont_see_my_question:

Ansible 1.8 以降で、タスクの結果または -v (verbose) モードを指定したコマンドを隠したい場合、次のタスクまたは playbook 属性は役立ちます。::

    - name: 秘密の task
      shell: /usr/bin/do_something --value={{ secret_value }}
      no_log: True

これは詳細な出力を行いますが、出力を見る事のできるような別の方法からのデリケートな情報は隠します。

no_log 属性は play 内部で指定する事も可能です。::

    - hosts: all
      no_log: True

しかしながら、これはデバッグを多少困難にします。使用は単一のタスクのみにする事をおすすめします。

ここにはない質問があります
++++++++++++++++++++++++++++

下のセクションを参照して IRC や Google Group で質問をしてください。

.. seealso::

   :doc:`index`
       ドキュメントのインデックス
   :doc:`playbooks`
       playbook の紹介
   :doc:`playbooks_best_practices`
       ベストプラクティスアドバイス
   `User Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア? Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
