環境設定（とプロキシ経由で動作させる）
==================================================

.. versionadded:: 1.1

プロキシ経由でパッケージのアップデートを取得したり、通常のパッケージアップデートではプロキシ経由で取得して、他のパッケージではプロキシを経由しないでアップデートを取得したいという必要があるかもしれませんが、これも可能です。または、正確に実行するために環境変数を確実にする必要のあるスクリプトかもしれません。

Ansible では 'environment' キーワードを使用することにより環境設定を簡単に行えます。これは例です。::

    - hosts: all
      remote_user: root

      tasks:

        - apt: name=cobbler state=installed
          environment:
            http_proxy: http://proxy.example.com:8080

環境は変数に設定することも可能で、このようにアクセスします。::

    - hosts: all
      remote_user: root

      # ディクショナリで "proxy_env" と名づけた変数を作成
      vars:
        proxy_env:
          http_proxy: http://proxy.example.com:8080

      tasks:

        - apt: name=cobbler state=installed
          environment: proxy_env

プロキシ設定を上で紹介したとはいえ、多数の設定が提供されています。環境ハッシュを定義するのに論理上もっともな場所は group_vars ファイルかもしれず、次のようになります。::

    ---
    # file: group_vars/boston

    ntp_server: ntp.bos.example.com
    backup: bak.bos.example.com
    proxy_env:
      http_proxy: http://proxy.bos.example.com:8080
      https_proxy: http://proxy.bos.example.com:8080

.. seealso::

   :doc:`playbooks`
       playbook の紹介
   `User Mailing List <http://groups.google.com/group/ansible-devel>`_
       質問がありますか？google group で確認しましょう！
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
