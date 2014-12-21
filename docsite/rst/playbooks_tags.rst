タグ
====

巨大な playbook があり、playbook 全体を実行せずに特定の部分だけ実行したくなる事があるかもしれません。

このため play と tasks は "tags:" 属性をサポートしています。

例::

    tasks:

        - yum: name={{ item }} state=installed
          with_items:
             - httpd
             - memcached
          tags:
             - packages

        - template: src=templates/src.j2 dest=/etc/foo.conf
          tags:
             - configuration

とても長い playbook の中の "configuration" と "packages" 部分だけを実行したい場合、このようにする事で可能です。::

    ansible-playbook example.yml --tags "configuration,packages"

その一方で、特定の task を *除いて* playbook を実行したい場合、次のようする事で可能です。::

    ansible-playbook example.yml --skip-tags "notification"

また、roles にタグを適用することも可能です。::

    roles:
      - { role: webserver, port: 5000, tags: [ 'web', 'foo' ] }

加えて include 文でも tag をつける事が出来ます。::

    - include: foo.yml tags=web,foo

Both of these have the function of tagging every single task inside the include statement.

.. seealso::

   :doc:`playbooks`
       playbooks の紹介
   :doc:`playbooks_roles`
       Playbook を roles で編成する
   `User Mailing List <http://groups.google.com/group/ansible-devel>`_
       質問がありますか？google group で確認しましょう！
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャネル
