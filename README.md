# k8s-homepages

Kubernetes 上に以下を構築するための最小構成です。

- ingress-nginx
- Argo CD
- nginx で配信する複数サイト
- サイト単位の Ingress 公開
- 一部サイトで `icon.png` を公開

## 1. ingress-nginx / Argo CD をインストール

```bash
kubectl apply -k k8s/platform/ingress-nginx
kubectl apply -k k8s/platform/argocd
```

## 2. Argo CD のプロジェクトと Application を登録

`k8s/argocd/apps.yaml` の `repoURL` をあなたの Git リポジトリ URL に変更してから適用します。

```bash
kubectl apply -f k8s/argocd/project.yaml
kubectl apply -f k8s/argocd/apps.yaml
```

## 3. DNS / hosts を設定

テスト用に次のホスト名を Ingress の EXTERNAL-IP に向けます。

- `site-a.example.local`
- `site-b.example.local`

## サイト構成

- `site-a`: `index.html` のみ公開
- `site-b`: `index.html` と `icon.png` を公開
