# 简明COSBench教程

英文名是《A Byte of COSBench》。

这个仓库收录了 COSBench 教程文档，并使用 MkDocs Material 构建静态站点。

## 本地预览

```bash
python3 -m pip install -r requirements.txt
python3 -m mkdocs serve
```

## 构建检查

```bash
python3 -m mkdocs build --strict
```

## 发布

推送到 `main` 后，GitHub Actions 会自动构建并部署文档到 GitHub Pages。

首次启用时，请在仓库 `Settings -> Pages` 中将发布来源设置为 `GitHub Actions`。
