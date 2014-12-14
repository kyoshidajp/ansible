パターン
++++++++

.. contents:: Topics

Ansible におけるパターンは管理対象のホストを決める方法になります。これはどのホストと通信するか、という事ですが :doc:`playbooks` の観点においては、特定の構成または IT プロセスを適用するホストを意味します。

コマンドラインでどのように使うかは :doc:`intro_adhoc` セクションで出てきますが、基本的なものは次のようになります。::

    ansible <pattern_goes_here> -m <module_name> -a <arguments>

例えば次のようになります。::

    ansible webservers -m service -a "name=httpd state=restarted"

パターンは通常グループのセット (ホストのセット) -- 上のケースでは "webservers" グループのマシンを参照します。

Ansible を使用するために最初に必要なのは、Ansible にどのように inventory のホストを伝えるかを知る事です。これは特定のホスト名またはホストのグループを指定する事で行われます。

次のパターンは inventory のすべてのホストが対象となります。::

    all
    *

また、特定のホスト名やホストのセットで指定する事も可能です。::

    one.example.com
    one.example.com:two.example.com
    192.168.1.50
    192.168.1.*

次のパターンは一つ以上のグループが対象となります。グループはコロンで区切られ、"OR" になります。
これはホストが一つのグループまたはそれ以外になります。::

    webservers
    webservers:dbservers

グループを除く指定、例えば、すべてのマシンは webservers グループになりますが、 phoenix グループは該当しないという事も可能です。::

    webservers:!phoenix

また、２つのグループに共通する指定も可能です。これはグループ webservers で、さらに stagging に属するホストが対象になります。::

    webservers:&staging

組み合わせることも出来ます。::

    webservers:dbservers:&staging:!phoenix

上の設定ではグループ 'webservers' と 'dbservers' のうち、グループ 'staging' に該当するが、グループ 'phoenix' には該当しないマシンが対象になります ... ふう。

ansible-playbook で "-e" 引数を使って変数を使用することもできますが、特別な使用です。::

    webservers:!{{excluded}}:&{{required}}

グループを厳密に指定する必要はありません。ホスト名、IP、グループはワイルドカードを使って指定可能です。::

    *.example.com
    *.com

ワイルドカードパターンとグループを同時に指定する事も可能です。::

    one*.com:dbservers

一歩進んで、グループの番号がつけられたサーバを選択可能です。::

    webservers[0]

グループの一部分の選択はこのようになります。::

    webservers[0:25]

ほとんどの人は指定しませんが、正規表現によるパターンの指定も可能です。パターンを '~' で開始します。::

    ~(web|db).*\.example\.com

/usr/bin/ansible または /usr/bin/ansible-playbook に ``--limit`` フラグを指定して除外条件を指定する事も可能です。::

    ansible-playbook site.yml --limit datacenter2

ファイルからホストのリストを読む場合、ファイルの先頭に '@' を指定します。Ansible 1.2 から追加されました。::

    ansible-playbook site.yml --limit @retry_hosts.txt

十分簡単です。:doc:`intro_adhoc` と、ここの内容を実施するための :doc:`playbooks` を参照してください。

.. seealso::

   :doc:`intro_adhoc`
       基本的なコマンドの例
   :doc:`playbooks`
       ansible の構成管理言語を学ぶ
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア? Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
