Playbooks
`````````

Playbooks は Ansible の構成、開発、そしてオーケストレーションの言語です。リモートシステムに対して実施したいポリシーであったり、一般的な IT 手順におけるステップの一式を記述することができます。

あなたの作業場所において、Ansible モジュールがツールであるとすれば playbooks は計画の設計になります。

基本段階として、playbooks は設定やリモートへのデプロイ管理として使われます。より進んだ段階では、ローリングアップデートを含む一連の多段ロールアウト、他のホストへのデリゲートアクション、対話的なサーバやロードバランサの監視に使われます。

ここにはたくさんの情報がありますが、一度にすべてを学ぶ必要はありません。小さく始めておいて、必要になった時に他の機能について学ぶ事もできます。

Playbooks は人に読めるように設計され、基本的なテキスト言語で開発されています。playbooks とそれらをインクルードして編成するには複数の方法があり、いくつかの提案をして、Ansible を活用します。

`Playbook の例 <https://github.com/ansible/ansible-examples>`_ を読むことをおすすめします。たくさんのコンセプトとともにベストプラクティスを紹介しています。

.. toctree::
   :maxdepth: 1

   playbooks_intro
   playbooks_roles
   playbooks_variables
   playbooks_conditionals
   playbooks_loops
   playbooks_best_practices
