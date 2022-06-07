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

### APIをlintする

```shell
buf lint
# google/type/datetime.proto:17:1:Package name "google.type" should be suffixed with a correctly formed version, such as "google.type.v1".
# pet/v1/pet.proto:44:10:Field name "petID" should be lower_snake_case, such as "pet_id".
# pet/v1/pet.proto:49:9:Service name "PetStore" should be suffixed with "Service".
```
