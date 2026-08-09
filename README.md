# 📝 tech-blog-1

このリポジトリは、[Zenn CLI](https://zenn.dev/zenn/articles/zenn-cli-guide) を使用して執筆したテックブログ記事を管理するためのものです。

Zenn 上で公開されている記事のソース（Markdown 形式）は、このリポジトリでバージョン管理されています。

次にやってみたい事：共同編集などです。

---

## 📘 公開中の記事一覧

以下の記事は Zenn にて公開中です（記事作成にCursorを利用）：

- [React + TypeScript で「保存ボタンを 2 回押さないと動かない」問題の解決方法](https://zenn.dev/yucco/articles/e25cdcd6e839b6)
- [ドラッグ＆ドロップでリスト並び替え！@dnd-kit を使った実装の工夫](https://zenn.dev/yucco/articles/26463c7e00b2f6)
- [1年半の成長を振り返ってストーリーにしてみた件](https://zenn.dev/yucco/articles/2025-06-25-growth-story)
- [設計・実装上の課題を抱えたロードマップ機能を引き継ぎ、プロダクト標準に合わせて再構築した話](https://zenn.dev/yucco/articles/custom-learning-roadmap)

#### 🤖 NEW! AI駆動開発体験シリーズ（2025/7/9公開）

- [修行中エンジニアがAIツールを試してみたら想像以上にすごかった体験談](https://zenn.dev/yucco/articles/ai-development-automation-story)
- [PlayWrightとTaskMasterで TODOアプリの自動構築デモ - 驚きの仕組みを調べてみた！](https://zenn.dev/yucco/articles/article-1-auto-build-demo)
- [PlayWrightとTaskMasterで TODOアプリの自動バグ修正デモ - 驚きの仕組みを試してみた！](https://zenn.dev/yucco/articles/article-2-bug-fix-demo)
- [「動くプロトタイプ」を諦めて「静止画モック」に切り替えたら、レビューが一瞬で終わった話](https://zenn.dev/yucco/articles/claude-design-ui-mockup-story)

---

## 🛠 Zenn CLI の使い方

このリポジトリをクローンし、Zenn CLI を使ってローカルで記事をプレビューできます。

```bash
# Zenn CLI がグローバルにインストールされていない場合
npm install zenn-cli

# 初期セットアップ（必要に応じて）
zenn init

# 記事のローカルプレビューを開始
zenn preview
```

> 💡 `npx zenn preview` でも実行できますが、Mac 環境では `zenn preview` の方が安定する場合があります。

---

## 🔗 関連リンク

- 📘 Zenn プロフィール: [https://zenn.dev/yucco](https://zenn.dev/yucco)
- 📚 Zenn CLI ガイド: [https://zenn.dev/zenn/articles/zenn-cli-guide](https://zenn.dev/zenn/articles/zenn-cli-guide)
- 🐙 GitHub: [https://github.com/Yucco-K](https://github.com/Yucco-K)
- 🤖 TaskMaster公式: [https://www.task-master.dev/](https://www.task-master.dev/)
- 🖥️ Cursor公式: [https://cursor.sh/](https://cursor.sh/)

---

## 📝 補足

このリポジトリはポートフォリオとして公開しています。
Cursor と TaskMaster と Zenn と GitHub を連携し、構造的に記事を作成・管理・公開するワークフローの一例としてご活用ください。
