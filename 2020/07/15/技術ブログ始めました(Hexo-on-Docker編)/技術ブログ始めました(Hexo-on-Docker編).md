---
title: 技術ブログ始めました(Hexo-on-Docker編)
date: 2020-07-16T11:19:39+09:00
thumbnail: /tech-blog/images/hexo_pic.png
categories: 
  - "ブログ環境"
tags: 
  - "hexo"
---

# はじめに

MONET Technologies株式会社、2020年新卒入社の竹内です。

この記事では、技術ブログ開始に当たって、静的サイトジェネレータHexoを導入した話です。

全体構成のお話は{% post_link 技術ブログ始めました(構成編) %}でお話しています。

## Docker使ってみます

さて、それでは本題に入っていきましょう。
前提として、今回はHexo環境をDockerで構築します。
Docker環境自体の構築については[こちら](https://awesome-linus.com/2019/08/17/mac-docker-install/)を参照しました。
筆者は今回初めてDockerを使ってみているので、変なところがあれば指摘していただければと思います。

流れとしては、

1. 執筆用Dockerイメージのビルド
2. Dockerコンテナの実行

といったところです。

## 執筆用Dockerイメージのビルド

まず、執筆用のDockerイメージをビルドするところから始まります。
Hexo用のDockerイメージとして[こちら](https://github.com/spurin/docker-hexo)を参考にしました。

Dockerfileをお好みに記述したら、記述したファイルの存在するディレクトリ上で、

```
$ docker build ./ -t docker-hexo
```

とすればDockerイメージがビルドされます。

上記リンクで紹介したDockerファイルでは

- Hexo環境の導入
- デプロイ用Gitの導入(SSHキーの配置も含めて)

まで行ってくれるようになっています。

今回は、

1. ブログのソースリポジトリをGithubにプッシュ
2. Github Pagesの公開用リポジトリを更新

という流れを自動化したかったので、Gitのhook機能を使ってHexoのコマンドを紐づけるようにしています。

具体的には、Hexo用のデプロイコマンドを記述したファイル```pre_push```を作り、

```sh
#!/bin/sh

echo "---hooked hexo commands start---"
#hexo clean
hexo deploy --generate
echo "---hooked hexo commands end---"
echo "---If you get a ""hexo command does not exist"" error,"
echo "it's seems You' re not running git on Docker.---"
```

```~/.git/hooks/```以下に配置するようにしています。

今回はpushする前というhookになっていますが、複数人で編集することを考えると、リモートリポジトリでのマージが終わったタイミングの方が良かったかもしれないと思っています。が、今回Githubがリモートリポジトリになっているので、リモートサーバ上でのhookが実装できず、ひとまずpushをトリガーにしています。
Gitlabなど自前でGitサーバを立てている際には、

1. ブログを書く時にはまず執筆ブランチを切る
2. ブログ執筆後、執筆ブランチ上の変更をpush
3. リモートリポジトリで執筆ブランチをマスターブランチにマージ
4. マージ後のhookで公開用リポジトリへソースをデプロイ

みたいな流れが安全そうです。

## Dockerコンテナの実行

Dockerイメージをビルドしたら、

```
$ docker create --name=hexo-domain.com \
-e HEXO_SERVER_PORT=4000 \
-e GIT_USER="hoge" \
-e GIT_EMAIL="huga@sample.com" \
-v /Path/To/Blog/app:/app \
-p 4000:4000 \
docker-hexo

$ docker start hexo-domain.com && docker logs --follow hexo-domain.com
```

でコンテナを作成し開始します。
これを行うことで、Dockerコンテナ上のHexoサーバが動き、

http://localhost:4000

を参照することでローカルなサイトのプレビューができるようになります。

# まとめ

Dockerを使うことによって、複数人が使うブログの執筆環境をある程度簡易に構築することができました。
便利なツールが増える一方、依存関係などで導入が面倒になることもよくあるので、Dockerのようなツールもうまく使えるとよりハッピーになりそうですね。

~~時代の主流はSaaSでローカルに環境を整える場面も少なくなってきそうですが。。。~~