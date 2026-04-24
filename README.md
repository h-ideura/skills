# skills

エージェント向けの **Skill**（手順・ドメイン知識をまとめた `SKILL.md`）を置き、今後も追加していく予定のリポジトリです。

## 構成

スキルは `skills/<スキル名>/SKILL.md` に配置します。

## 現在のスキル

| 名前 | 説明 |
|------|------|
| [cmp-preview-screenshot](.agent/.skills/cmp-preview-screenshot/SKILL.md) | **Android Studio の Agent Chat** を想定。`commonMain` のプレビューをスクリーンショット化する手順を支援します。`render_compose_preview` が `androidMain` 前提のため、一時的に移動して取得する手法を記述しています |

## インストール
```shell
gh skill install h-ideura/skills
```

## 今後

新しいスキルを順次追加し、エージェントが参照できる手順・文脈を蓄積していきます。
