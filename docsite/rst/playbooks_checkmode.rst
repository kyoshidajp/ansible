チェックモード ("Dry Run")
=========================

.. versionadded:: 1.1

.. contents:: Topics

ansible-playbook を ``--check`` オプションを付けて実行すると、リモートシステムに変更を行いません。代わりに、'チェックモード' をサポート（core モジュールに含まれていますが、すべてのモジュールでこれを行う必要はない）しているあらゆるモジュールは、実施される変更を出力します。チェックモードをサポートしていない他のモジュールは何も行わず、実施される変更も出力されません。

チェックモードはまさしくシミュレートであり、先に実行するコマンドの結果に依存した手順がある場合にはあまり役に立たないかもしれません。しかし、1度に1ノードの基本的な構成管理のユースケースには最適です。

例::

    ansible-playbook foo.yml --check

.. _forcing_to_run_in_check_mode:

チェックモードで task を実行
````````````````````````````

.. versionadded:: 1.3

時々、チェックモードでタスクを実行したい事があるかもしれません。これにはタスクで 'always_run' 節を使います。この値は Jinja2 式で、ちょうど `when` 節のようなものです。単純なケースでは値として boolean の YAML 値で十分です。

例::

    tasks:

      - name: this task is run even in check mode
        command: /something/to/run --even-in-check-mode
        always_run: yes

注意として、タスクを `when` 節で使うと false で評価され、`always_run` で実行してスキップされると true になります。

.. _diff_mode:

``--diff`` で差分を表示
```````````````````````````````````

.. versionadded:: 1.1

ansible-playbook の ``--diff`` オプションは ``--check`` （詳細は上記）と一緒に使用するすばらしいのですが、それだけでも使用できます。このフラグが渡されると、リモートシステム上でテンプレート出力ファイルが変更された際に、ファイルへ対して行われたテキストの変更内容（または、``--check`` と一緒に使った場合は、行われる変更内容）が ansible-playbook CLI に出力されます。差分機能は大量の出力があるので、次のように一度にひとつのホストをチェックするのがベストです。::

    ansible-playbook foo.yml --check --diff --limit foo.example.com
