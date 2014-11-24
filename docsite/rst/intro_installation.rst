インストール
============

.. contents:: トピックス

.. _getting_ansible:

Ansible の入手
```````````````

github アカウントを持っている場合、`Github プロジェクト <https://github.com/ansible/ansible>`_ から Ansible を入手することが出来ます。github はバグや機能のアイデアを共有するための issue トラッカを管理する場所です。

.. _what_will_be_installed:

基本 / 何をインストールするか
```````````````````````````````

Ansible はデフォルトで SSH プロトコルでマシンを管理します。

一度 Ansible をインストールすれば、データベースを追加したり、デーモンを起動して実行させる必要はありません。マシン（ラップトップだと手軽）にインストールし、全体のリモートマシンを管理する事ができます。　Ansible でリモートマシンを管理する際に、リモートマシンにソフトウェアがインストールされているか、それが実行中であるかを気にする必要がないため、新しいバージョンに移行した時に、どうやって Ansible をアップグレードすればよいかという事は問題にはなりません。

.. _what_version:

どのバージョンを選べばよいか?
`````````````````````

ソースからの実行はとても簡単なので、リモートマシンでインストールする必要はありません。実際、多くのユーザは開発バージョンを追っています。

Ansible のリリース周期は通常2ヶ月です。この短いリリース周期のため、マイナーバグは安定版ブランチでのバックポートと比較して、一般に次のリリースで修正されます。

メジャーバグは、まだメンテナンスされている事もありますが、もっともそれはまれです。

Red Hat Enterprise Linux (TM), CentOS, Fedora, Debian, あるいは Ubuntuを使っていて、最新のリリースバージョンを取得するには OS のパッケージマネージャを利用できます。

他には Python パッケージマネージャの "pip" を利用できます。

最新の機能を使ったりテストするために開発バージョンを使用したいのであれば、実行のために情報をソースから取得できます。ソースから実行するためのプログラムをインストールする必要はありません。

.. _control_machine_requirements:

管理マシンの要件
````````````````````````````

今のところ Ansible は Python 2.6 がインストールされている管理マシンから実行することが出来ます。(管理マシンとして Windows のサポートはされていません)

これは Red Hat, Debian, CentOS, OS X, BSD ではインストールされていることでしょう。

.. _managed_node_requirements:

対象ノードの要件
`````````````````````````
管理対象のノードには Python 2.4 またはそれ以降のバージョンがインストールされている必要があります。ただし、Python 2.5 以前であれば次のパッケージがインストールされている必要があります。

* ``python-simplejson``

.. note::

   "raw" モジュール（手軽で荒い方法でコマンドを実行するためのモジュール）と script モジュールはこれを必要としません。Ansible の raw モジュールを使って python-simplejson をインストールする事もできます。

.. note::

   リモートノードで SELinux を有効にしている場合、copy/file/template モジュールを使用する前に libselinux-python をインストールしておく必要があります。
   yum モジュールを使う場合、当然ですがリモートノードに yum をインストールしておく必要があります。

.. note::

   Python 3 は Python 2 とは少し異なっていて、ほとんどの Python プログラム（Ansible を含む）ではまだ利用することができません。しかし、いつくかの Linux ディストリビューション（Gentoo や Arch）では Python 2.X インタプリタがデフォルトではインストールされていません。それらのシステムではインストールして、インベントリファイル (参照 :doc:`intro_inventory`) の 'ansible_python_interpreter' 変数に Python 2.X のパスを設定する必要があります。Red Hat Enterprise Linux, CentOS, Fedora, や Ubuntu のような ディストリビューションでは 2.X のインタプリタがインストールされています。これは全ての Unix システムでも同様です。もしそれら Python 2.X がインストールされているリモートシステムを自動実行させる必要があれば、 'raw' モジュールを使えばリモート作業を行う事ができます。

.. _installing_the_control_machine:

管理マシンへのインストール
``````````````````````````````

.. _from_source:

ソースからの実行
+++++++++++++++++++

Ansible はチェックアウトから実行までとても簡単で、実行に root 権限は必要なく、インストールにはソフトウェアは必要ありません。

デーモンやデータベースのセットアップは必要ありません。そのため、コミュニティの多くのユーザは Ansible の開発バージョンをいつでも使うことが出来ますし、新機能が実装された際に利用して、プロジェクトに簡単に貢献する事ができます。インストールするためのものがないので、開発バージョンのフォローはほとんどのオープンソースプロジェクトよりとても簡単です。

ソースからインストールするには次のコマンドを実行します。

.. code-block:: bash

    $ git clone git://github.com/ansible/ansible.git --recursive
    $ cd ./ansible
    $ source ./hacking/env-setup

使用している Python のバージョンで pip をインストールしていない場合は、次のようにして pip をインストールします。

    $ sudo easy_install pip

また、Ansible は次の Python モジュールを使うため、インストールされている必要があります。

    $ sudo pip install paramiko PyYAML Jinja2 httplib2

Ansible をアップデートする際の注意として、ソースツリーのアップデートだけでなく、 git にある Ansible 自身のモジュールである "submodules" のアップデートも行ってください。

.. code-block:: bash

    $ git pull --rebase
    $ git submodule update --init --recursive

一度 env-setup スクリプトを実行すれば、/etc/ansible/hosts にデフォルトのインベントリファイルが作成されます。インベントリファイル(参照 :doc:`intro_inventory`)は /etc/ansible/hosts　以外にも記述することができます。

.. code-block:: bash

    $ echo "127.0.0.1" > ~/ansible_hosts
    $ export ANSIBLE_HOSTS=~/ansible_hosts

インベントリファイルについてはマニュアルに後ほど登場するので、内容について確認することが出来ます。

ping コマンドでテストをしてみましょう。

.. code-block:: bash

    $ ansible all -m ping --ask-pass

お望みであれば "sudo make install" を実行する事もできます。

.. _from_yum:

Yum による最新リリース
++++++++++++++++++++++

Fedora ディストリビューションでサポートしている `EPEL
<http://fedoraproject.org/wiki/EPEL>`_ 6, 7 から yum によるインストールも可能です。

Ansible は Python 2.4 かそれ以上（EL5）のオペレーティングシステムを管理する事ができます。

Fedora ユーザは Ansible を直接インストールする事ができます。RHEL または CentOS を使っていて EPEL の設定を行っていない場合は `EPEL の設定 <http://fedoraproject.org/wiki/EPEL>`_ を参照します。

.. code-block:: bash

    # CentOS, RHEL または Linux 系統において epel リリースの RPM をインストール
    $ sudo yum install ansible

また、RPM 自身でビルド可能です。チェックアウトまたは tarball から ``make rpm`` コマンドで RPM をビルドし、インストールする事ができます。``rpm-build``, ``make``, and ``python2-devel`` で、インストールされている事を確認できます。

.. code-block:: bash

    $ git clone git://github.com/ansible/ansible.git
    $ cd ./ansible
    $ make rpm
    $ sudo rpm -Uvh ~/rpmbuild/ansible-*.noarch.rpm

.. _from_apt:

Apt (Ubuntu)による最新リリース
++++++++++++++++++++++++++++++++

Ubuntu によるビルドは `PPA <https://launchpad.net/~ansible/+archive/ansible>`_ から可能です。

PPA の configure と Ansible のインストールには次のコマンドを実行します。

.. code-block:: bash

    $ sudo apt-get install software-properties-common
    $ sudo apt-add-repository ppa:ansible/ansible
    $ sudo apt-get update
    $ sudo apt-get install ansible

.. note:: 古い Ubuntu ディストリビューションでは "software-properties-common" は "python-software-properties" から呼ばれます。

Debian/Ubuntu パッケージは次のようにしてソースをチェックアウトしてビルドできます。

.. code-block:: bash

    $ make deb

最新のソースを入手して実行する場合、上記の方法で可能です。

.. _from_pkg:

pkg (FreeBSD)による最新リリース
+++++++++++++++++++++++++++++++++

.. code-block:: bash

    $ sudo pkg install ansible

次の様に実行して ports からもインストールが可能です。

.. code-block:: bash

    $ sudo make -C /usr/ports/sysutils/ansible install

.. _from_brew:

Homebrew (Mac OSX)による最新リリース
++++++++++++++++++++++++++++++++++++++

Mac へのインストールには、Homebrew で次の様に実行します。

.. code-block:: bash

    $ brew update
    $ brew install ansible

.. _from_pip:

Pip による最新リリース
+++++++++++++++++++++++

Ansible は Python パッケージマネージャの "pip" でインストールが可能です。'pip' をインストールしていない場合、次のようにして pip をインストールする事が出来ます。

   $ sudo easy_install pip

Ansible をインストールします。

   $ sudo pip install ansible

OS X Mavericks をインストールしている場合、いくつか警告が出るかもしれません。回避策として次のように実行します。

   $ sudo CFLAGS=-Qunused-arguments CPPFLAGS=-Qunused-arguments pip install ansible

virtualenv 環境で Ansible をインストールする事もできます。virtualenv 環境は Ansible をグローバル環境にインストールされないか心配する心配する必要がありません。Ansible を直接インストールするために easy_install を使わないでください。

.. _tagged_releases:

タグ付けされたリリースのTarballs
+++++++++++++++++++++++++++

Ansible をパッケージングしたりビルドしたい場合、git チェックアウトは必要ありません。`Ansible downloads <http://releases.ansible.com/ansible>`_ から Tarboalls を取得できます。

これらのリリースは `git repository <https://github.com/ansible/ansible/releases>`_ のリリースバージョンになります。

.. seealso::

   :doc:`intro_adhoc`
       基本的なコマンドの例
   :doc:`playbooks`
       Ansible の設定管理言語を学ぶ
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア?  Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
