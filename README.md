# k8s-homepages (microk8s / IPv6-only)

`microk8s` で **IPv6 のみ外部ポート開放** している環境向けに、以下を構築する最小構成です。

- ingress (microk8s addon)
- Argo CD
- nginx で配信する複数サイト
- サイト単位の Ingress 公開
- 一部サイトで `icon.png` を公開

## 0. 前提

- microk8s が IPv6 クラスタとして構築済み
- 外部公開は TCP 80/443 を IPv6 のみで許可

## 1. microk8s addon を有効化

`ingress-nginx` は microk8s 組み込み ingress addon を使います。

```bash
microk8s enable dns ingress
```

## 2. Argo CD をインストール

```bash
microk8s kubectl apply -k k8s/platform/argocd
```

## 3. Argo CD のプロジェクトと Application を登録

`k8s/argocd/apps.yaml` の `repoURL` をあなたの Git リポジトリ URL に変更してから適用します。

```bash
microk8s kubectl apply -f k8s/argocd/project.yaml
microk8s kubectl apply -f k8s/argocd/apps.yaml
```

## 4. IPv6 DNS (AAAA) / hosts を設定

Ingress に向くノード IPv6 へ次の名前を向けます。

- `site-a.example.local`
- `site-b.example.local`

`/etc/hosts` の例:

```text
2001:db8::10 site-a.example.local
2001:db8::10 site-b.example.local
```

## 5. 動作確認

```bash
curl -g -H 'Host: site-a.example.local' http://[2001:db8::10]/
curl -g -H 'Host: site-b.example.local' http://[2001:db8::10]/
curl -g -H 'Host: site-b.example.local' http://[2001:db8::10]/icon.png -I
```

## サイト構成

- `site-a`: `index.html` のみ公開
- `site-b`: `index.html` と `icon.png` を公開
