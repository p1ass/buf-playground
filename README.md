# buf-playground

[buf](https://docs.buf.build/introduction) で遊ぶ

## Installation

bufをインストールしておく。

```shell
brew install bufbuild/buf/buf
buf --help
```

使っているエディターの拡張機能をインストールしておく。
これにより、自動的にlintがかかるようになる。

https://docs.buf.build/editor-integration

## セットアップメモ

### buf.yamlを作成

protcの `-I` のようなもの

```shell
buf mod init
```

### APIをビルドする

```shell
buf build
echo $? # 0

buf ls-files
# google/type/datetime.proto
# pet/v1/pet.proto

```