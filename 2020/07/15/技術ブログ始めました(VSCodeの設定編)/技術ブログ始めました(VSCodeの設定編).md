---
title: 技術ブログ始めました(VSCodeの設定編)
date: 2020-07-16T17:31:46+09:00
thumbnail: /tech-blog/images/vscode_logo.jpeg
categories:
  - "ブログ環境"
tags: 
  - "hexo"
  - "vscode"
---

# はじめに

MONET Technologies株式会社、2020年新卒入社の竹内です。

この記事では、技術ブログ開始に当たって、VSCodeでのHexo記事執筆環境を導入した話です。

全体構成のお話は{% post_link 技術ブログ始めました(構成編) %}でお話しています。

静的サイトジェネレータであるHexoを導入したお話は{% post_link 技術ブログ始めました(Hexo-on-Docker編) %}でお話しています。


## 楽な執筆環境に向けて

技術ブログ、エンジニアの方なら一度は書こうとするものだと思うのですが、3ヶ月続けられた方には心からの拍手を送りたいです。

やはり、モノを書く、アウトプットするのってコストが高いんですよね。単なるメモ程度なら雑に書いても良いわけですが、ブログとして書こうとするとある程度体裁を整えないといけなかったり。。。といったことを考えると、筆が重くなり。。。

そこで今回は、執筆を楽にするためのツール、VSCode上のエクステンションを紹介してみます。

## vscode-hexo-utils

Hexo+VSCodeで執筆する際には必須のエクステンションです。
この[vscode-hexo-utils](https://marketplace.visualstudio.com/items?itemName=fantasy.vscode-hexo-utils)を使うと

- 記事一覧やタグ一覧を表示するサイドバーの追加
- マークダウンに画像を挿入したプレビュー時、Hexoならではの画像ディレクトリ構成に対応してプレビューを見せてくれる
- Win:ctrl-alt-v Mac:opt-cmd-vでクリップボードの画像を貼り付け(記事に対応した画像配置用ディレクトリの作成とマークダウン上の参照文記述)

などなどしてくれるので大分執筆が楽になります。特に画像の挿入は重要なので、開発者には頭が上がりません。

## Remote Development

[こちら](https://code.visualstudio.com/docs/remote/remote-overview)はDockerなど、リモートとして作った環境に対してVSCodeを接続できるエクステンションです。今回はDocker上にHexoやGitを構成しているので、直接接続して、Docker上のコマンドを呼び出す、なんてことが簡単になります。

ただ、残念なことが、 **リモートで接続した際にクリップボードが共有化されていないらしく、上記のvscode-hexo-utilsの画像コピペ機能が上手く働きません。**
そのため、こちらは残念ながら、執筆中には使えない感じになっています。。。
もしクリップボードの共有化ができると、

1. vscodeを直接Dockerに繋げる
2. vscode-hexo-utilsを使いながら執筆
3. 直接Docker内のコマンドを叩いてデプロイ

といった使い方ができるようになります。

もちろん今のRemote Development機能も大変良いモノで、友人なんかは、RaspberryPiなどでリモート環境を作る、VSCodeでリモート接続して手元のPCでIoT開発、なんてこともしているのでおすすめです。

## VSCodeのタスク機能

今回はDockerを通じてのデプロイになりますが、VSCodeの方でコマンドを打ってデプロイまで行いたいですよね。
そこで使うのが、VSCodeのタスク機能です。

参考になるのが[こちら](https://fereria.github.io/reincarnation_tech/98_Other/VSCode/vscode_create_task/)です。

自分は
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "deploy-hexo-on-docker",
      "type": "shell",
      "command": "docker exec -it hexo-domain.com git add . && docker exec -it hexo-domain.com git commit -m 'update ${fileBasenameNoExtension}' && docker exec -it hexo-domain.com git push"
    }
  ]
}
```

というコマンドを登録して、VSCodeからDocker上のGitを叩き、pushするとともにデプロイを行っています。
このタスク機能を使えば、任意のコマンドをVSCode上で使えるので良いですね。

## vscode-drawio

最後に紹介するエクステンションは、
[vscode-drawio](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)です。

こちらはブラウザ上で動くdraw.ioをvscode上で動かそうというモノで、簡単な図を書きたい時に使えます。

この、なんでもエディタ上にのせてやろうという心意気、自分は嫌いじゃないです。笑

# まとめ

様々なVSCodeのエクステンションがあって、見ていて楽しいです。
もちろんエンジニアは何を生み出すか、も重要ですが、たまには息抜きに自分の道具を磨いて見るのも良いですね。
