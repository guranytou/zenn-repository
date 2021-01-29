---
title: "docker-composeでAWS用Terraform開発環境を構築した" # 記事のタイトル
emoji: "🐳" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["Terraform", "Docker", "AWS", "環境構築"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
業務でAWS&Terraformを触ることになったのですが、Windowsで環境構築するのがしんどい&人にやり方を展開するのがつらいなと感じたので、Docker-composeでサクッと立ちあげられる環境を作りました。

# Repository
https://github.com/guranytou/docker-compose-develop-environment-terraform

# 簡単な解説
## 概要
ローカルでaws-vaultなどで予めcredential情報が設定されてる前提で構築しましたが、exportで設定したり.envを使ったりで渡すこともできます。  
terraformを動かす環境はLinuxベースにしたかったものの、エディタはWindows側のものが使いたかったので、作業用ディレクトリをマウントできるようにしました。


## 各ファイル
- docker-compose.yml
    - 上記理由でVolumesを設計しています。
    - 環境変数でAWS環境にデプロイするのに必要な設定を渡しています。
- dockerfile
    - alpineで構築しました。aws-cli導入のためにglibcを入れています。
    - php入りコンテナを使ってpipでツールをインストールするという方法や、aws-cli公式コンテナにTerraformを入れるという方法もあるかなと後から思いました。
-  `docker-entrypoint.sh`
    - 環境変数が渡されない場合は、コンテナ起動しないようにしています。
    - Create credential file以下で、aws-cli利用する際に必要となるファイルを生成しています。

# 実際に使ってみて
`terraform apply/destroy` で突然固まってもコンテナ停止→再起動すれば動くようになるので、開発環境をコンテナにするのは楽でいいなと思いました。
あと、他の人にも気軽に動作確認済みの開発環境を配布できるのが強いなと感じました。

# 今後やりたい事
あんまり長くコマンドを打ちたくないので、最終的には `docker-compose up -d`だけで立ち上がるようにしたいなあと
思っています。

この記事が何かの参考になれば幸いです。

