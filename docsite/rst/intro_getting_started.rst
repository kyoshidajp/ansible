はじめに
===============

.. contents:: Topics

.. _gs_about:

まえがき
````````

すでに :doc:`intro_installation` を読んで Ansible がインストールされているので、詳細について掘り下げて、いくつかのコマンドを実行してみましょう。

まず最初に playbooks と呼ばれる設定・デプロイ・オーケストレーションについて簡単に紹介します。Playbooks は区切られたセクションでカバーされています。

このセクションでは最初にどうやって実行するかを学びます。その後、より詳細について :doc:`intro_adhoc`  を読めば playbooks の内部に飛び込む準備が出来、もっとも面白い部分を探検する事が出来るでしょう！

.. _remote_connection_information:

リモート接続の情報
`````````````````````````````

はじめに、Ansible がどのようにしてリモートマシンと SSH で通信するのかを理解することが重要です。

Ansible 1.3 以降ではデフォルトで、リモートマシンと通信するのにネイティブ OpenSSH を使います。これは ControlPersist（パフォーマンス機能）、Kerberos や Junp Host セットアップのような ~/.ssh/config でのオプションを有効にします。管理マシンとして Enterprise Linux 6 (Red Hat Enterprise Linux と CentOSなどその派生)を使用する際、OpenSSH は ControlPersist をサポートしておらず、古すぎるかもしれません。これらのオペレーティングシステムでは、Ansible は 'paramiko' と呼ばれる OpenSSH の高品質な Python 実装を使用してフォールバックします。もし Kerberos 認証 SSH のような機能を使用したい場合、管理マシンとなるプラットフォームにおいて OpenSSH の新しいバージョンが有効になるまで Fedora、OS X、Ubuntu を使用することを検討してください。 :doc:`playbooks_acceleration` を参照してください。

Ansible 1.2 以前では、厳密には paramiko がデフォルトなので、ネイティブ SSH を使用するには -c ssh を指定するか、設定ファイルに指定する必要があります。

たまに、SFTP を実行できないデバイスがあります。これはまれですが、SFTP をサポートしないリモートデバイスと通信する際に、SCP モード :doc:`intro_configuration` に変更する事もできます。

リモートマシンと通信を行う際に、Ansible はデフォルトでは SSH キー -- これを推奨します -- の使用を想定していますが、パスワードでも可能です。パスワード認証を有効にするには ``--ask-pass`` オプションを指定します。パスワードが必要な sudo を有効にするには ``--ask-sudo-pass`` オプションを指定します。

常識かもしれませんが、: Any management system benefits from being run near the machines being managed を共有することに価値があります。
クラウドで実行する場合、クラウド内のマシンから Ansible を実行する事を考えます。オープンなインターネットではよくあるケースで、快適に動作するでしょう。

進んだトピックとして、Ansible は SSH 経由でリモートに接続する必要はありません。
転送はプラガブルで、chroot や lxc 、jail コンテナと同様のローカル管理のためのオプションがあります。これは 'ansible-pull' と呼ばれるモードで、can also invert the system and have systems 'phone home' via scheduled git checkouts to pull configuration directives from a central repository.

.. _your_first_commands:

最初のコマンド
```````````````````

Ansible がインストールされたので、いくつか基礎を始めます。

/etc/ansible/hosts を編集（または作成）し、そこに ``authorized_keys`` に存在する SSH キーのひとつ以上のリモートホストを追加します。

    192.168.1.50
    aserver.example.org
    bserver.example.org

これはインベントリファイルと呼ばれます。詳細は :doc:`intro_inventory` にあります。

認証のために SSH キーを使用しているとします。次のようにしてパスワード入力なしで接続できるようにします。

.. code-block:: bash

    $ ssh-agent bash
    $ ssh-add ~/.ssh/id_rsa

(設定によっては、代わりに pem ファイルを記述するために の ``--private-key`` オプションを使用する事もできます)

次はすべてのノードに対して ping を実行します。

.. code-block:: bash

   $ ansible all -m ping

Ansible は SSH の様に、現在のユーザ名でマシンに対してリモート接続を試みます。リモートでのユーザ名を指定するには '-u' パラメータを指定します。

sudo でアクセスしたい場合は次の様にオプションを指定します。

.. code-block:: bash

    # ユーザ bruce で実行
    $ ansible all -m ping -u bruce
    # ユーザ bruce で、 sudo によって root として実行
    $ ansible all -m ping -u bruce --sudo
    # ユーザ bruce で、 sudo によって batman として実行
    $ ansible all -m ping -u bruce --sudo --sudo-user batman

(sudo を指定する実行は Ansible の設定ファイルによる設定が変更されます。このフラグは (-H の様な) sudo の設定を無効にします。)

すべてのノードに生存確認を行います。

.. code-block:: bash

   $ ansible all -a "/bin/echo hello"

おめでとうございます。Ansible でノードと通信ができました。 :doc:`intro_adhoc` を読んで、Ansible :doc:`playbooks` だけでなく、別のモジュールでやりたいことを見つける事ができます。Ansible はコマンドを実行させるだけでなく、設定やデプロイの管理を行う機能もあります。あなたはもうインフラを完全に動作させる事ができます。

.. _a_note_about_host_key_checking:

ホストキーの確認
`````````````````

Ansible 1.2.1 以降ではデフォルトでホストキーの確認が有効になっています。

ホストが再インストールされ、'known_hosts' のキーが異なってしまうと、正しくなるまで結果はエラーになります。ホストが 'known_hosts' に登録されていない状態では、キー入力の確認プロンプトが表示されます。この動作は望ましいものではないかもしれません。

もし、その影響を理解した上でこの動作を無効にするには、/etc/ansible/ansible.cfg または ~/.ansible.cfg を次のように編集します。

    [defaults]
    host_key_checking = False

あるいは環境変数に設定する事でも可能です。

.. code-block:: bash

    $ export ANSIBLE_HOST_KEY_CHECKING=False

paramiko モードによるホストキーの確認はおおむね速度が遅いので注意してください。そのため、代わりに 'ssh' を使用する事をおすすめします。

.. _a_note_about_logging:

Ansible は "no_log: True" が指定された場合を除き、リモートマシンのリモート syslog にログを残します。これは後ほど説明します。

管理マシンで基本的なロギングを有効にするには :doc:`intro_configuration` を参照し、設定ファイルの 'log_path' を変更します。エンタープライズユーザは :doc:`tower` に興味を持たれるかもしれません。Tower には REST API を経由してドリルダウン、ホストやプロジェクトの履歴閲覧を可能にする非常に強力なデータベースのロギング機能があります。

.. seealso::

   :doc:`intro_inventory`
       インベントリに関するより詳細な情報
   :doc:`intro_adhoc`
       基本的なコマンドの例
   :doc:`playbooks`
       Ansible の構成管理言語を学びます
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア?  Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
