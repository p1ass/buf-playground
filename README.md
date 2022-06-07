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

### Breaking Changeを検出する

`Pet` message の `pet_type` の型を string にしてから試す。

```shell
buf breaking --against ".git#branch=master,subdir=." 
# pet/v1/pet.proto:20:3:Field "1" on message "Pet" changed type from "enum" to "string".

```

### コードを生成する

```shell
buf generate petapis
```

### dependenciesを追加する

`buf.yaml` の `deps` に `buf.build/googleapis/googleapis` を追加

```shell
buf build
# WARN    Specified deps are not covered in your buf.lock, run "buf mod update":
#         - buf.build/googleapis/googleapis
# pet/v1/pet.proto:7:8:google/type/datetime.proto: does not exist
```

lockファイルを追加する。

```shell
buf mod update
buf build
```

### Goのコードを生成する

```shell
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

```shell
buf generate petapis
```

### gRPCエンドポイントを実装

```shell
go mod init github.com/p1ass/buf-playground/petstore
go mod tidy
```

```shell
go run server/main.go

# 別のターミナルで
go run client/main.go
# 2022/06/08 00:30:47 Connected to 127.0.0.1:8080
# 2022/06/08 00:30:47 Successfully PutPet
```

### 別のモジュールを作成

```shell
mkdir paymentapis
cd paymentapis
buf mod init
```

```shell
buf mod update
buf build
```

### managed modeを使う

`proto` から `go_option` を消すとエラーになることを確認する

```shell
buf generate
# protoc-gen-go-grpc: unable to determine Go import path for "payment/v1alpha1/payment.proto"
# 
# Please specify either:
#         • a "go_package" option in the .proto source file, or
#         • a "M" argument on the command line.
```

https://developers.google.com/protocol-buffers/docs/reference/go-generated#package

`buf.gen.yaml` で `managed` の設定を追加する。

```shell
rm -rf gen
buf generate
```