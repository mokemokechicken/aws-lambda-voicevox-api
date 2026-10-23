# 配備情報の確認

確認日: 2026-10-24

## 配備扱い

Owner確認では、このPython実装は未配備。別実装のruntimeをこのrepositoryの配備物として扱わない。会社account ID、resource名、内部URL、私有repository情報はここに記録しない。

このrepositoryのsourceは、そのままでは現行runtimeやconsumerの証拠にならない。実装と配備targetの対応がowner確認されるまで、runtimeは未特定として扱う。

## Sourceだけの確認

```bash
git status --short
git branch -avv
git ls-tree -r --name-only HEAD
rg -n 'cdk|lambda|runtime|handler|Docker' README.md package.json cdk.json bin lib
```

これらはcheckout内のsource/branch構成を確認するためのread-only操作であり、AWSへの問い合わせや変更を行わない。配備先の照合は、ownerが同一性を確認した別の運用手順で行う。
