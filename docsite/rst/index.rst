Ansible ドキュメント
=====================

Ansible について
`````````````

Ansible ドキュメントへようこそ!

Ansible は IT 自動化ツールです。システムの環境設定やソフトウェアのデプロイ、継続的デプロイや休止時間がゼロのローリングアップデートといった高度な IT タスクを実行可能にします。

Ansible の目標はシンプルでありながら出来るだけ簡単に使用できる事です。It also has a strong focus on security and reliability, featuring a minimum of moving parts, usage of OpenSSH for transport (with an accelerated socket mode and pull modes as alternatives), and a language that is designed around auditability by humans -- even those not familiar with the program.

私達はシンプルである事はどんな規模の環境にとっても適切であると考え、開発者やシステム管理者、リリースエンジニア、IT マネージャ、その他あらゆる多忙なユーザのためにデザインされています。Ansible は何千ものエンタープライズ環境だけではなく、インスタンス数の少ない小規模なセットアップ管理にも適しています。

Ansible は対象マシンへのエージェントを必要としません。デーモンがインストールされないため、リモートのデーモンをどのようにしてアップグレードするか、システムを管理するかを考慮する必要はありません。また、オープンソースとしてもっともレビューされているうちのひとつである OpenSSH によって、セキュリティ上のリスクは大幅に軽減されます。Ansible は分離されていて、リモートマシンへのコントールアクセスのために OS の資格情報に頼る事になります。もし Kerberos、LDAP やその他の集中型認証管理システムへの接続が必要な場合でも簡単に行う事ができます。

このドキュメントは最新のリリースバージョンである Ansible (1.7.2) と開発バージョン (1.8) をカバーしています。それぞれのセクションにおいて、最近の機能のために、Ansible のバージョンが表記されています。Ansible, Inc ではおおよそ2ヶ月ごとに新しいメジャーバージョンをリリースしています。コアアプリケーションは多少控えめにリリースされます。言語デザインやセットアップにおいてシンプルである事には価値があり、とても早いスピードで新しいモジュールやプラグインが開発され、一般的にリリースのたびに20程度の新しいモジュールが追加されています。

.. _an_introduction:

.. toctree::
   :maxdepth: 1

   intro
   quickstart
   playbooks
   playbooks_special_topics
   modules
   modules_by_category
   guides
   developing
   tower
   community
   galaxy
   test_strategies
   faq
   glossary
   YAMLSyntax
