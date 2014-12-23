プロンプト
=========

playbook の実行時にユーザ入力のプロンプトを出したい場合は 'vars_prompt' セクションの指定により可能です。

一般的に、記録したくないデリケートなデータを尋ねるために使用されます。

セキュリティを考慮して、例えば、すべてのソフトウェアリリースで同じ playbook を利用し、push-script の特定のリリースバージョンのプロンプトを出したいという事があるかもしれません。

これは最も基本的な例です::

    ---
    - hosts: all
      remote_user: root

      vars:
        from: "camelot"

      vars_prompt:
        name: "名前は何ですか？"
        quest: "何を探していますか？"
        favcolor: "好きな色は何ですか？"

まれに変更する変数があり、それは上書き可能なデフォルト値を持たせたい事があるかもしれません。これは default 引数を使うことで実現できます。::

   vars_prompt:

     - name: "release_version"
       prompt: "製品のリリースバージョン"
       default: "1.0"

vars_prompt にはユーザの入力を隠す設定があり、今後その他のオプションをサポートする予定ですが、それ以外は同じように動作します。::

   vars_prompt:

     - name: "some_password"
       prompt: "パスワードを入力"
       private: yes

     - name: "release_version"
       prompt: "製品のリリースバージョン"
       private: no

`Passlib <http://pythonhosted.org/passlib/>`_ がインストールされていれば、vars_prompt は入力値を暗号化する事が可能で、例えば、パスワードを指定するユーザモジュールでの利用があります。::

   vars_prompt:

     - name: "my_password2"
       prompt: "password2 を入力"
       private: yes
       encrypt: "sha512_crypt"
       confirm: yes
       salt_size: 7

'Passlib' によってサポートされている次の crypt スキーマを使用可能です:

- *des_crypt* - DES Crypt
- *bsdi_crypt* - BSDi Crypt
- *bigcrypt* - BigCrypt
- *crypt16* - Crypt16
- *md5_crypt* - MD5 Crypt
- *bcrypt* - BCrypt
- *sha1_crypt* - SHA-1 Crypt
- *sun_md5_crypt* - Sun MD5 Crypt
- *sha256_crypt* - SHA-256 Crypt
- *sha512_crypt* - SHA-512 Crypt
- *apr_md5_crypt* - Apache’s MD5-Crypt variant
- *phpass* - PHPass’ Portable Hash
- *pbkdf2_digest* - Generic PBKDF2 Hashes
- *cta_pbkdf2_sha1* - Cryptacular’s PBKDF2 hash
- *dlitz_pbkdf2_sha1* - Dwayne Litzenberger’s PBKDF2 hash
- *scram* - SCRAM Hash
- *bsd_nthash* - FreeBSD’s MCF-compatible nthash encoding

ただ、'salt' または 'salt_size' パラメータのみ指定可能です。自分のソルト値を 'salt' で指定するか、'salt_size' で自動的に生成されたものを使うことが出来ます。何も指定しなければ、サイズ 8 の salt が生成されます。

.. seealso::

   :doc:`playbooks`
       playbook の紹介
   :doc:`playbooks_conditionals`
       playbook での条件文
   :doc:`playbooks_variables`
       変数に関するすべて
   `User Mailing List <http://groups.google.com/group/ansible-devel>`_
       質問がありますか？google グループで確認する！
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC チャットチャネル
