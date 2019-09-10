# kustomize-sample

[kustomize](https://github.com/kubernetes-sigs/kustomize) を使ってみる。

## 環境構築

### `kubectl kustomize`

`kubectl` のバージョンが 1.14 以上の場合は `kubectl kustomize` が入ってる。

```bash
$ kubectl kustomize -h
Print a set of API resources generated from instructions in a kustomization.yaml file.
...
```

|kubectl version|kustomize version|
|:---|:---|
|v1.15.x|v2.0.3|
|v1.14.x|v2.0.3|

### kustomize

`kubectl kustomize` は kustomize のバージョン追従が遅い可能性があるので、素で使いたい場合は以下。

```bash
$ brew install kustomize
```

## kustomize

### 基本

- **base ＝ `kustomization.yaml` ＋ resources**
- **overlay ＝ `kustomization.yaml` ＋ patches** ＋ more resources

#### base

- **`base/kustomization.yaml`**
  - resources を束ねるファイルで、 `kubectl kustomize <dir>` あるいは `kustomize build <dir>` の起点となる
- **resources**
  - k8s リソースを定義するファイル群
  - deployment や service といったリソース単位で yaml ファイルを作成

```bash
.
└── base
    ├── kustomization.yaml # resources で以下を読み込む
    ├── deployment.yaml    # resource
    └── service.yaml       # resource
```

```bash
$ kubectl kustomize ./base
# 作成されたマニフェストが標準出力される
$ kubectl apply -k ./base
# 標準出力されるマニフェストをそのまま適用する
```

#### overlay

- **`overlays/<env>/kustomization.yaml`**
  - base を参照し、patches を束ねるファイルで、 `kubectl kustomize <dir>` あるいは `kustomize build <dir>` の起点となる
- **patches**
  - resources のパラメータを上書きするファイル群

```bash
.
├── base # 上記と同じ
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays
    ├── development
    │   ├── kustomization.yaml # patches で以下を読み込み、 bases で base を指定
    │   ├── cpu_count.yaml     # patch
    │   └── replica_count.yaml # patch
    └── production
        ├── kustomization.yaml
        ├── cpu_count.yaml
        └── replica_count.yaml
```

```bash
$ kubectl kustomize ./overlays/development/
# 作成されたマニフェストが標準出力される
$ kubectl apply -k ./overlays/development/
# 標準出力されるマニフェストをそのまま適用する
```