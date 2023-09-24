# Honkitで書いたドキュメントをDockerでPDFに変換する
このコマンドでできます
```bash
docker run --rm -v .:/honkit nishidemasami/markdown-docs:v1.2.0 npx honkit pdf ./ ./output.pdf
```
