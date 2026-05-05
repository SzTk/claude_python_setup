# claude_python_setup

Python + Rust 拡張ライブラリを開発する際に **Claude Code** と組み合わせて使うための指示・規約テンプレート集です。
このリポジトリを対象プロジェクトに配置するだけで、Claude Code エージェントが必要に応じてスキル・コマンド・エージェント定義を参照します。

## 構成

```
skills/
  pyo3-maturin/        # PyO3 + maturin による Rust 拡張開発の規約
  pyo3-type-mapping/   # Python–Rust 型マッピングのガイドライン
  python-uv-workflow/  # uv による Python 仮想環境管理の標準手順
commands/
  python-env.md        # /python-env  : 仮想環境のセットアップ・検証
  lint.md              # /lint        : Python (ruff/mypy) + Rust (clippy) の静的解析
  test.md              # /test        : lint + pytest + cargo test の一括実行
agents/
  pre-commit-test.md   # git commit 前に /test を自動実行して品質ゲートを適用するエージェント
```

## 前提ツール

| ツール | 用途 |
|--------|------|
| [uv](https://github.com/astral-sh/uv) | Python 仮想環境・パッケージ管理 |
| [maturin](https://github.com/PyO3/maturin) | Rust → Python 拡張のビルド |
| [ruff](https://github.com/astral-sh/ruff) | Python lint / formatter |
| [mypy](https://mypy-lang.org/) | Python 型チェック |
| Rust toolchain (`cargo`, `clippy`) | Rust ビルド・lint |

## 使い方

1. このリポジトリの内容を対象プロジェクトに配置する。
2. あとは Claude Code エージェントが作業に応じて各ファイルを自律的に参照する。

必要であれば `/python-env`、`/lint`、`/test` などのコマンドを明示的に指示することも可能。

## スキルの概要

### `python-uv-workflow`

Python 作業はすべて `uv run ...` 経由で実行するよう Claude に強制します。
グローバルの `python` / `pip` / `pytest` 直接呼び出しを禁止し、`.venv` の自動作成・同期も行います。

### `pyo3-maturin`

PyO3 + maturin を使った Rust 拡張の開発フローを定義します。
プロジェクト構成・ビルドループ・Python–Rust 境界設計・型マッピング・エラー変換のルールを含みます。

### `pyo3-type-mapping`

Python と Rust の型対応表および変換時の注意点をまとめたガイドラインです。

