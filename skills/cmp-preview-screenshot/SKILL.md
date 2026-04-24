---
name: cmp-preview-screenshot
description: Compose Multiplatform (CMP) の commonMain にあるプレビューのスクリーンショットを取得するためのワークフローを提供します（Android Studio の Agent Chat 上での利用を想定）。一時的にプレビュー関数を androidMain に移動してレンダリングし、完了後に元に戻す手順を自動化・支援します。
---

# CMP Preview Screenshot Skill

このスキルは、Compose Multiplatform プロジェクトにおいて、`commonMain` に定義されたプレビューをレンダリングしてスクリーンショット（`render_compose_preview`）を取得するための手順をガイドします。

## 想定利用環境

- **Android Studio の Agent Chat** での利用を前提としています。`render_compose_preview` や `gradle_sync` など、手順で参照するツールは Android Studio のエージェント機能が提供するものとして説明しています。
- Cursor など別のエージェント環境では、同等のツール名・挙動がない場合があります。その場合は手順の意図（一時的な `androidMain` への配置と同期後のレンダリング）に沿って、利用可能な手段に置き換えてください。

## 背景
現在、`commonMain` にある `@Preview` は、`render_compose_preview` ツールが `androidMain` (Android ターゲット) でのレンダリングを想定しているなどの理由で、直接レンダリングできない場合があります。そのため、一時的に `androidMain` に移動してレンダリングを行う必要があります。

## 実行手順

### 1. プレビューの実装 (新規の場合)
- 新しくプレビューを作成する場合は、まず **`commonMain` の対象ファイル内**（例: スクリーン定義の末尾）に `@Preview` 関数を実装します。
- これにより、ドメインモデルや共通コンポーネントとの整合性を `commonMain` のコンテキストで確認できます。

### 2. 対象の特定と一時移動
- スクリーンショットを撮りたい `@Preview` 関数が含まれる `commonMain` のファイルパスと、関数名を特定します。
- `androidMain` 内に一時的なファイル（例: `composeApp/src/androidMain/kotlin/ui/TempPreview.kt`）を作成し、対象のプレビュー関数をコピーします。
- **注意**:
    - `androidMain` 側のパッケージ名は、参照先の Composable と同じにするか、適切にインポートを設定してください。
    - プレビュー対象の Composable が `commonMain` で `public` または `internal` であれば、`androidMain` から参照可能です。

### 3. Gradle Sync による同期
- プレビュー関数を `androidMain` に配置した後、必ず **`gradle_sync`** を実行してください。
- これにより、IDE およびレンダリングツールが `androidMain` に追加された新しいシンボルを正しく認識し、依存関係を解決できるようになります。

### 4. レンダリングの実行
- `render_compose_preview` ツールを使用して、`androidMain` に配置したファイルと関数名を指定して実行します。
    - `filePath`: `androidMain` の一時ファイルパス
    - `composableName`: プレビュー関数名
- 取得した画像を確認し、必要に応じて調整します。

### 5. 元の状態に復元
- レンダリングが完了したら、`androidMain` に作成した一時的なコードまたはファイルを削除します。
- `commonMain` に追加したプレビュー関数をそのまま残すか、削除するかはタスクの指示に従ってください。

## 注意事項
- `render_compose_preview` が失敗する場合は、ビルドが通る状態であることを確認してください（必要に応じて `gradle_build` を検討）。
- `commonMain` と `androidMain` で同じ関数名が重複するとビルドエラー（Conflicting overloads）が発生するため、一時移動の際は注意してください。
