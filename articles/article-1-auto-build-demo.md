---
title: "PlayWrightとTaskMasterで TODOアプリの自動構築デモ - 驚きの仕組みを調べてみた！"
emoji: "🤖"
type: "tech"
topics: ["playwright", "taskmaster", "cursor", "AI駆動開発", "テスト自動化"]
published: false
---

:::message alert
「こんな方法もあるかも？」くらいで参考にしてください。

動作や挙動が記事と異なる場合は、以下のような理由が考えられます：

- OSやブラウザ、Node.jsのバージョンなど環境の違い
- ライブラリやフレームワークの仕様変更
- セキュリティ設定やローカル環境の違い
- サンプルコードの簡略化、または筆者の理解不足による表現

正確な情報が必要な場合は、公式ドキュメントや最新のリファレンスをご確認いただくのがおすすめです。
:::

## はじめに

先日、開発TMのリーダーにPlayWrightのデモを見せてもらったことをきっかけに、自分でもUI自動構築のデモを作りたくなっていろいろ試行錯誤してみました。初心者なりに試してみた結果、ちょっと面白い発見があったので、その体験を共有してみます。

## 実際に作成したデモ動画

こちらは実際にフロントエンド・バックエンド・DBの代わりにストレージを使用して、アプリを構築したものです。

![](https://storage.googleapis.com/zenn-user-upload/6d2dda31bcf8-20250705.gif)

## 作ってみて驚いた自動化の仕組み

### 🎯 気づいたポイント

実際にデモを作ってみると、手動操作ではなく**事前に設計されたプログラムによって自動的に実行**されているように見えました。

### 📊 自動実行っぽく見えるポイント

#### 1. **正確すぎるタイミング制御**

- 各構築ステップが**正確に2秒間隔**で実行される
- 手動操作では不可能そうな**ミリ秒単位の精度**
- 実行順序が**毎回同じ**

#### 2. **リアルタイム構築ログ**

```
[14:30:15] 🤖 自動構築デモを開始します...
[14:30:17] 🔨 ステップ1: HTML構造の作成
[14:30:19] ✅ HTML構造の作成が完了しました
[14:30:21] 🔨 ステップ2: CSSスタイリングの適用
```

- **タイムスタンプ付き**で記録される
- 手動操作では**再現できそうにない精度**

#### 3. **視覚的フィードバック**

- 画面左上に「アプリ構築モード」インジケーターが表示される
- 各要素作成時に**パルスアニメーション**が発生
- リアルタイムで**統計が更新**される

:::message
パルスアニメーションは、ユーザーの注意を引いたり、システムの状態を視覚的に伝えたりするのに効果的なUI要素です。特に、自動化デモやリアルタイム処理の表示でよく使われます。
:::

#### 4. **自動タスク操作**

```javascript
// 以下のようなイメージでタスクが自動的に追加・操作される
const demoTasks = [
	"朝食を食べる", // 1.5秒後に自動追加
	"メールチェック", // 3.0秒後に自動追加
	"会議の準備", // 4.5秒後に自動追加
	"コードレビュー", // 6.0秒後に自動追加
];
```

:::message
タスクマスタの機能により、タスクの自動追加・操作とリアルタイム統計更新が実現されています。これにより、デモが本当に自動で動いていることを視覚的に確認できます。
:::

## 使われている技術について調べてみた

### 使用技術

このデモで使われている技術を調べてみると：

**🎯 タスクマスタ（Taskmaster）**

- プロジェクト全体のタスク管理とテスト自動化
- 自動テスト、回帰テスト、パフォーマンステストの実行
- 品質保証（QA）とリリース準備の自動化

**🤖 プレイライト（Playwright）**

- ブラウザ自動化ツール
- UIテスト、E2Eテスト、ブラウザ操作の自動化
- クロスブラウザ対応のテスト実行

### セットアップ手順

セットアップ手順については、以下の公式ドキュメント等をご参照ください：

- **[Playwright](https://playwright.dev/docs/intro)** - ブラウザ自動化のツール
- **[TaskMaster Documentation](https://www.task-master.dev/)** - タスク管理と自動化ツール

### デモで使用している仕組み

```javascript
// デモ用の6つのステップ
this.buildSteps = [
	{ name: "HTML構造の作成", action: () => this.createHTMLStructure() },
	{ name: "CSSスタイリングの適用", action: () => this.applyStyling() },
	{ name: "JavaScript機能の実装", action: () => this.implementCoreFeatures() },
	{
		name: "インタラクティブ要素の追加",
		action: () => this.addInteractiveElements(),
	},
	{ name: "データ永続化の実装", action: () => this.implementDataPersistence() },
	{ name: "自動デモタスクの実行", action: () => this.runAutoDemoTasks() },
];
```

### タイミング制御

```javascript
// 各ステップが正確に2秒間隔で実行
this.buildSteps.forEach((step, index) => {
	setTimeout(() => {
		this.log(`🔨 ステップ${index + 1}: ${step.name}`);
		step.action();
	}, index * 2000); // 正確に2秒間隔
});
```

## 確認してみた方法

### ログのタイムスタンプ確認

- 各ステップが**正確に2秒間隔**で実行される
- ログメッセージが**一貫している**
- 手動操作では**不可能そうな精度**

## 動画での流れ

動画で表示される以下の要素が自動実行っぽく見えます：

1. **構築ログのリアルタイム表示**
2. **要素の段階的出現**
3. **タスクの自動追加・操作**
4. **統計の自動更新**
5. **視覚的インジケーター**

## まとめ ✨

このデモを見る限り、以下の要素により**自動化されたプログラム実行**のように見えました：

- ✅ **正確なタイミング制御**（手動では不可能そう）
- ✅ **詳細なログ記録**（リアルタイム）
- ✅ **視覚的フィードバック**（明確なインジケーター）
- ✅ **操作制限**（デモ中の制御）
- ✅ **再現性**（何度実行しても同じ結果）

🎉 **自動的に実行されていることを視覚的にも確認することができました。** ⭐️

---

## 関連記事

- [修行中エンジニアがAIツールを試してみたら想像以上にすごかった体験談](ai-development-automation-story.md)
- [PlayWrightとTaskMasterで TODOアプリの自動バグ修正デモ - 驚きの仕組みを試してみた！](article-2-bug-fix-demo.md)

## 実際に試してみよう！

実際のTODOアプリの自動構築を体験してみてください：

:::message alert
このサンプルコードは、ブラウザでの自動実行の仕組みを理解するための例示です。実際に試す場合は、類似のサンプルコードを作成するなど、ご自身のご判断と責任のもと行なってください。環境やブラウザの違いにより動作が異なる場合があります。
:::

:::details 🚀【体験】 TODOアプリの自動構築デモ（クリックして展開） 🚀

```javascript
// TODOアプリの自動構築デモ
class TodoAutoBuilder {
	constructor() {
		this.container = document.createElement("div");
		this.container.style.cssText = `
      position: fixed; top: 20px; right: 20px; 
      background: white; border: 2px solid #007bff; 
      padding: 20px; border-radius: 10px; z-index: 10000;
      font-family: Arial, sans-serif; max-width: 300px;
    `;
		document.body.appendChild(this.container);

		// ログ表示エリアを作成
		this.logContainer = document.createElement("div");
		this.logContainer.style.cssText = `
      position: fixed; top: 20px; left: 20px; 
      background: #f8f9fa; border: 1px solid #dee2e6; 
      padding: 15px; border-radius: 8px; z-index: 10000;
      font-family: monospace; max-width: 400px; max-height: 300px;
      overflow-y: auto; font-size: 12px;
    `;
		document.body.appendChild(this.logContainer);
	}

	log(message) {
		const timestamp = new Date().toLocaleTimeString();
		const logDiv = document.createElement("div");
		logDiv.textContent = `[${timestamp}] ${message}`;
		logDiv.style.marginBottom = "5px";
		logDiv.style.color = "#495057";
		this.logContainer.appendChild(logDiv);
		// コンソールにも出力
		console.log(`[${timestamp}] ${message}`);
	}

	createHTML() {
		// innerHTMLの代わりにtextContentを使用してセキュリティエラーを回避
		const title = document.createElement("h3");
		title.id = "demo-title";
		title.textContent = "🤖 自動構築中...";
		this.container.appendChild(title);

		const appDiv = document.createElement("div");
		appDiv.id = "todo-app";
		this.container.appendChild(appDiv);

		const input = document.createElement("input");
		input.type = "text";
		input.id = "todo-input";
		input.placeholder = "タスクを入力...";
		appDiv.appendChild(input);

		const button = document.createElement("button");
		button.id = "add-btn";
		button.textContent = "追加";
		appDiv.appendChild(button);

		const list = document.createElement("ul");
		list.id = "todo-list";
		appDiv.appendChild(list);

		console.log("🔨 HTML構造の作成が完了しました");
	}

	applyStyles() {
		const app = this.container.querySelector("#todo-app");
		if (app) {
			app.style.cssText = `
        background: #f8f9fa; padding: 15px; border-radius: 8px;
        box-shadow: 0 2px 10px rgba(0,0,0,0.1);
      `;
		}

		const input = this.container.querySelector("#todo-input");
		if (input) {
			input.style.cssText = `
        width: 100%; padding: 8px; border: 1px solid #ddd;
        border-radius: 4px; margin-bottom: 10px;
      `;
		}

		const button = this.container.querySelector("#add-btn");
		if (button) {
			button.style.cssText = `
        background: #007bff; color: white; border: none;
        padding: 8px 16px; border-radius: 4px; cursor: pointer;
      `;
		}

		console.log("🎨 スタイルの適用が完了しました");
	}

	addFunctionality() {
		const input = this.container.querySelector("#todo-input");
		const button = this.container.querySelector("#add-btn");
		const list = this.container.querySelector("#todo-list");

		if (button && input && list) {
			button.addEventListener("click", () => {
				const text = input.value.trim();
				if (text) {
					const li = document.createElement("li");
					li.textContent = text;
					li.style.cssText = `
            padding: 8px; margin: 5px 0; background: white;
            border-radius: 4px; border-left: 4px solid #007bff;
          `;
					list.appendChild(li);
					input.value = "";
					console.log(`✅ タスク「${text}」を追加しました`);
				}
			});
		}

		console.log("⚡ 機能の実装が完了しました");
	}

	addDemoTasks() {
		const tasks = ["朝食を食べる", "メールチェック", "会議の準備"];
		const list = this.container.querySelector("#todo-list");

		if (list) {
			tasks.forEach((task, index) => {
				setTimeout(() => {
					const li = document.createElement("li");
					li.textContent = task;
					li.style.cssText = `
            padding: 8px; margin: 5px 0; background: white;
            border-radius: 4px; border-left: 4px solid #28a745;
          `;
					list.appendChild(li);
					console.log(`✅ デモタスク「${task}」を追加しました`);
				}, index * 1000);
			});
		}
	}

	createTodoApp() {
		console.log("🚀 TODOアプリの自動構築デモを開始します...");
		this.log("🚀 TODOアプリの自動構築を開始...");

		setTimeout(() => {
			this.log("🔨 HTML構造を作成中...");
			this.createHTML();
		}, 1000);

		setTimeout(() => {
			this.log("🎨 スタイルを適用中...");
			this.applyStyles();
		}, 3000);

		setTimeout(() => {
			this.log("⚡ 機能を実装中...");
			this.addFunctionality();
		}, 5000);

		setTimeout(() => {
			this.log("✅ 構築完了！");
			this.addDemoTasks();
			// タイトルを構築完了に変更
			const title = this.container.querySelector("#demo-title");
			if (title) {
				title.textContent = "✅ 構築完了！";
				title.style.color = "#28a745";
			}
		}, 7000);

		// 戻り値を明示的に返す
		return "デモが開始されました";
	}
}

// デモを実行
console.log("📋 TODOアプリの自動構築デモを開始します...");
const todoBuilder = new TodoAutoBuilder();
const result = todoBuilder.createTodoApp();
console.log("✅ デモが正常に開始されました");
```

:::

### 実行方法

1. **ブラウザの開発者ツールを開く**
2. **Consoleタブを選択**
3. **上記のコードをコピーして貼り付け**
4. **Enterキーを押す**

すると、ブラウザの画面上部にTODOアプリのウィンドウが表示され、自動構築デモが開始されます。
デモンストレーション後は、入力欄から手動でタスクを追加することができます。
（※ こちらのサンプルでは、リロードすると、デモ用UIとコンソールデータが消去されます。）

### 実行結果

コンソールで以下のような出力が表示されます：

```
[14:30:15] 🚀 TODOアプリの自動構築を開始...
[14:30:16] 🔨 HTML構造を作成中...
[14:30:18] 🎨 スタイルを適用中...
[14:30:20] ⚡ 機能を実装中...
[14:30:22] ✅ 構築完了！
```

このように、**正確に2秒間隔**でステップが実行されることが分かります。

### 参考として

このサンプルコードは、ブラウザでの自動実行の仕組みを理解するための例です。実際の開発では、このような仕組みを使って、より複雑な自動化システムを構築することができるそうです。

---

知識も経験もまだ浅い私ですが、AIツールの可能性にとても魅力を感じています。今後も地道に学びを重ねていきたいと思います。

**少しでも参考になれば幸いです！** ✨

## 参考リンク

- [Cursor AI コーディング支援](https://cursor.sh/)
- [PlayWright GitHubリポジトリ](https://github.com/microsoft/playwright/)
- [claude-task-master GitHubリポジトリ](https://github.com/eyaltoledano/claude-task-master?tab=readme-ov-file)
- [claude-task-master チュートリアル（docs/tutorial.md）](https://github.com/eyaltoledano/claude-task-master/blob/main/docs/tutorial.md)
