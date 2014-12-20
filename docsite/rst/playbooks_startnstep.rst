開始とステップ
======================

playbook を実行する他の方法を紹介します。これらのモードは新しい play をテストしたりデバッグするのに便利です。


.. _start_at_task

特定のタスクからの開始
```````````````````
playbook の特定のタスクから実行したい場合は ``--start-at`` オプションで可能です。::

    ansible-playbook playbook.yml --start-at="install packages"

上記の場合、"install packages" と名付けられたタスクから実行されます。

.. _step

ステップ
````````

playbook は ``--step`` を指定することでインタラクティブな実行が可能です。::

    ansible-playbook playbook.yml --step

各タスクでいったん停止し、そのタスクを実行するか確認します。
例えば "configure ssh" と呼ばれるタスクがあると、playbook は実行し確認します::

    Perform task: configure ssh (y/n/c):

"y" はタスクを実行し、"n" はタスクをスキップ、"c" は残りのすべてのタスクを確認せずに実行します。
