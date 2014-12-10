YAML シンタックス
==================

このページでは Ansible playbooks (構成管理言語)を記述する YAML シンタックスについて解説します。

Ansible では XML や JSON などに比べて、人が読み書きがしやすい YAML をデータフォーマットに採用しました。ほとんどのプログラミング言語では YAML のライブラリが存在します。

また、実際の記述方法については :doc:`playbooks` が参考になるでしょう。

YAML の基本
-----------

Ansible において、YAML ファイルはリストで開始します。リストの各要素は "ハッシュ" または "ディクショナリ" と呼ばれるキーと値のリストです。そのため、最初に YAML におけるリストとディクショナリの記述方法について知る必要があります。

YAML には若干癖があります。(Ansible に関連するしないにかからわず）すべての YAML ファイルは ``---`` で始めなければなりません。これは YAML フォーマットの書式であり、ドキュメントの開始となります。

リストのすべての要素は ``-`` (ハイフン)で始まり、同じインデントレベルになります。::

    ---
    # A list of tasty fruits
    - Apple
    - Orange
    - Strawberry
    - Mango

ディクショナリは次のフォーマットで ``key:`` と ``value`` を表します。::

    ---
    # An employee record
    name: Example Developer
    job: Developer
    skill: Elite

ディクショナリは次の様に省略して記述することも可能です。::

    ---
    # An employee record
    {name: Example Developer, job: Developer, skill: Elite}

.. _truthiness:

boolean 値(true/false)を記述する事もできます。::

    ---
    create_key: yes
    needs_agent: no
    knows_oop: True
    likes_emacs: TRUE
    uses_cvs: false

これまで学んだ YAML の例を組み合わせてみましょう。次の記述では Ansible は何も実行しないのですが、フォーマットについて学ぶ事ができるでしょう。::

    ---
    # An employee record
    name: Example Developer
    job: Developer
    skill: Elite
    employed: True
    foods:
        - Apple
        - Orange
        - Strawberry
        - Mango
    languages:
        ruby: Elite
        python: Elite
        dotnet: Lame

実際に YAML について学ぶのに必要なのは `Ansible` playbooks を書く事です。

Gotchas
-------

YAML は全体的にフレンドリーである一方で、次の記述ではシンタックスエラーになります。

    foo: somebody said I should put a colon here: so I did

この場合、次のようにコロンを値で囲みます。

    foo: "somebody said I should put a colon here: so I did"

こうする事でコロンは保持されます。

Ansible は変数を "{{ var }}" で記述します。コロンの後に "{" で開始すれば、YAML はディレクトリであると判断するので、次のように囲む必要があります。

    foo: "{{ variable }}"


.. seealso::

   :doc:`playbooks`
       playbooks では何を実行できて、どうやって記述、実行するかを学びます。
   `YAMLLint <http://yamllint.com/>`_
       問題があった際に YAML シンタックスをデバッグする助けになる YAML Lint(オンライン)
   `Github examples directory <https://github.com/ansible/ansible/tree/devel/examples/playbooks>`_
       github プロジェクトのコンプリート playbook ファイル
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       質問? Help? アイデア?  Google Groups メーリングリスト
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャンネル
