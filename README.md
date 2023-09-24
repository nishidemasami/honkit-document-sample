# Honkitで書いたドキュメントをDockerでPDFに変換する

以下のコマンドでHonkitで書いたMarkdownによるドキュメントをDockerでPDFに変換することができます
```bash
docker run --rm -v .:/honkit nishidemasami/markdown-docs:v1.2.1 npx honkit pdf ./ ./output.pdf
```

このドキュメントは常にビルドされ、以下URLで最新版を確認することができます。

HTML版：  
https://nishidemasami.github.io/honkit-document-sample/

PDF版：  
https://github.com/nishidemasami/honkit-document-sample/releases/download/pdf_relrase/output.pdf
