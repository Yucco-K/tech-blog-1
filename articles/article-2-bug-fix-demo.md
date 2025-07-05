---
title: "PlayWrightとTaskMasterで TODOアプリの自動バグ修正デモ - 驚きの仕組みを試してみた！"
emoji: "🔧"
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

最近、開発TMのリーダーにPlayWrightのデモを見せてもらったことをきっかけに、自分でもUI自動構築のデモを作りたくなっていろいろ試行錯誤してみました。初心者なりに試してみた結果、興味深い発見があったので、その体験を共有してみます。

## 実際に作成したデモ動画

こちらは実際にフロントエンド・バックエンド・DBの代わりにストレージを使用して、アプリの中に仕込まれたバグをPlayWrightが自動修正する様子を録画したものです。

![](https://storage.googleapis.com/zenn-user-upload/ba3127a06b2d-20250705.gif)

## 作ってみて驚いた自動化の仕組み

### 🎯 気づいたポイント

この高速バグ修正デモは、手動操作ではなく**事前に設計されたプログラムによって自動的に実行**されているように見えました。

### 📊 自動実行っぽく見えるポイント

#### 1. **正確すぎるタイミング制御**

- 各修正ステップが**正確に1.5秒間隔**で実行される
- 手動操作では不可能そうな**ミリ秒単位の精度**
- 修正順序が**毎回同じ**

#### 2. **リアルタイム修正ログ例**

```
[14:30:15] 🐛 バグ修正デモを開始します...
[14:30:16] 🔍 ステップ1: バグの検出
[14:30:18] ✅ バグの検出が完了しました
[14:30:19] 🔧 ステップ2: 修正コードの生成
[14:30:21] ✅ 修正コードの生成が完了しました
```

- **タイムスタンプ付き**で記録される
- 手動操作では**再現できそうにない精度**

#### 3. **視覚的フィードバック例**

- 画面左上に「視覚的バグ検出モード」インジケーターが表示される
- 各修正箇所に**ハイライトアニメーションやパルスアニメーション**が発生
- リアルタイムで**修正進捗が更新**される

:::message
パルスアニメーションは、ユーザーの注意を引いたり、システムの状態を視覚的に伝えたりするのに効果的なUI要素です。特に、自動化デモやリアルタイム処理の表示でよく使われます。
:::

#### 4. **自動コード修正例**

```javascript
// 以下のバグが自動的に検出・修正される
const bugFixes = [
	"構文エラーの修正", // 1.5秒後に自動修正
	"型エラーの修正", // 3.0秒後に自動修正
	"ロジックエラーの修正", // 4.5秒後に自動修正
	"パフォーマンス改善", // 6.0秒後に自動修正
];
```

:::message
**TaskMasterの自動化機能**

TaskMasterの機能により、バグの自動検出・修正とリアルタイム統計更新が実現されています。これにより、デモが本当に自動で動いていることを視覚的に確認できます。
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

セットアップ手順については、以下の公式ドキュメントをご参照ください：

- **[Playwright](https://playwright.dev/docs/intro)** - ブラウザ自動化のツール
- **[TaskMaster Documentation](https://www.task-master.dev/)** - タスク管理と自動化ツール

### デモで使用している仕組み

```javascript
// デモ用の4つのステップ
this.fixSteps = [
	{ name: "バグの検出", action: () => this.detectBugs() },
	{ name: "修正コードの生成", action: () => this.generateFix() },
	{ name: "コードの適用", action: () => this.applyFix() },
	{ name: "テストの実行", action: () => this.runTests() },
];
```

### タイミング制御例

```javascript
// 各ステップが正確に1.5秒間隔で実行
this.fixSteps.forEach((step, index) => {
	setTimeout(() => {
		this.log(`🔧 ステップ${index + 1}: ${step.name}`);
		step.action();
	}, index * 1500); // 正確に1.5秒間隔
});
```

## バグ修正プロセスの詳細

### 段階的な修正プロセス

デモは以下の4つのステップに分かれて自動実行されているようです：

1. **バグの検出** (1.5秒後)
   - 構文エラー、型エラー、ロジックエラーの自動検出
   - エラーの詳細分析と優先度付け

2. **修正コードの生成** (3.0秒後)
   - 検出されたバグに対する修正コードの自動生成
   - ベストプラクティスに基づく修正案の提示

3. **コードの適用** (4.5秒後)
   - 生成された修正コードの自動適用
   - 安全なコード変更の実行

4. **テストの実行** (6.0秒後)
   - 修正後のコードの自動テスト実行
   - 修正の有効性確認

### パフォーマンスとセキュリティ例

- **検出時間**: 高速（1秒間隔）
- **修正時間**: 即座
- **総処理時間**: 8秒以内
- **セキュリティ**: XSS対策、入力検証、安全なDOM操作

## 確認してみた方法

### ログのタイムスタンプ確認

- 各ステップが**正確に1.5秒間隔**で実行される
- ログメッセージが**一貫している**
- 手動操作では**不可能そうな精度**

## 動画での流れ

動画で表示される以下の要素が自動実行っぽく見えます：

1. **修正ログのリアルタイム表示**
2. **コードの段階的修正**
3. **エラーの自動検出・修正**
4. **テスト結果の自動更新**
5. **視覚的インジケーター**

## まとめ ✨

このデモを見る限り、以下の要素により**自動化されたプログラム実行**のように見えました：

- ✅ **正確なタイミング制御**（手動では不可能そう）
- ✅ **詳細なログ記録**（リアルタイム）
- ✅ **視覚的フィードバック**（明確なインジケーター）
- ✅ **操作制限**（デモ中の制御）
- ✅ **再現性**（何度実行しても同じ結果）

🎉**自動的に実行されていることを視覚的にも確認することができました。** ⭐️

---

## 関連記事

- [修行中エンジニアがAIツールを試してみたら想像以上にすごかった体験談](ai-development-automation-story.md)
- [PlayWrightとTaskMasterで TODOアプリの自動構築デモ - 驚きの仕組みを調べてみた！](article-1-auto-build-demo.md)

## 実際に試してみよう！

### まずはシンプルなバグ修正デモから試してみよう

:::message alert
このサンプルコードは、ブラウザを使用して、バグ自動修正の仕組みを体験するための例示です。実際に試す場合は、類似のサンプルコードを作成するなど、ご自身のご判断と責任のもと行なってください。環境やブラウザの違いにより動作が異なる場合があります。
:::

（※ こちらのシンプルなサンプルコードでは、UIには何も表示されず、コンソールでのみタスク実行の様子が表示されます。）

```javascript
// シンプルなバグ修正デモのサンプル
class SimpleBugFixDemo {
	constructor() {
		this.bugs = [
			{ type: "構文エラー", description: "括弧の不整合", fixed: false },
			{ type: "型エラー", description: "文字列を数値として使用", fixed: false },
			{
				type: "ロジックエラー",
				description: "無限ループの可能性",
				fixed: false,
			},
			{ type: "パフォーマンス", description: "非効率な配列操作", fixed: false },
		];
		this.currentBug = 0;
	}

	log(message) {
		const timestamp = new Date().toLocaleTimeString();
		const logEntry = `[${timestamp}] ${message}`;
		console.log(logEntry);
	}

	start() {
		this.log("🐛 バグ修正デモを開始します...");

		this.bugs.forEach((bug, index) => {
			setTimeout(() => {
				this.log(`🔍 バグ${index + 1}を検出: ${bug.type} - ${bug.description}`);
				this.currentBug = index;

				setTimeout(() => {
					this.log(`🔧 バグ${index + 1}を修正中...`);
					bug.fixed = true;

					setTimeout(() => {
						this.log(`✅ バグ${index + 1}の修正が完了しました`);

						if (index === this.bugs.length - 1) {
							this.log("🎉 すべてのバグ修正が完了しました！");
						}
					}, 500);
				}, 500);
			}, index * 1500);
		});
	}
}

// デモを実行
const bugFixDemo = new SimpleBugFixDemo();
bugFixDemo.start();
```

### 実行方法

1. **ブラウザの開発者ツールを開く**
2. **Consoleタブを選択**
3. **上記のコードをコピーして貼り付け**
4. **Enterキーを押す**

すると、コンソールにバグの検出から修正までのプロセスが自動的に実行されます。
（※ こちらのサンプルでは、リロードすると、コンソールデータが消去されます。）

### 実行結果

コンソールで以下のような出力が表示されます：

```
[14:38:05] 🐛 バグ修正デモを開始します...
VM594:20 [14:38:05] 🔍 バグ1を検出: 構文エラー - 括弧の不整合
VM594:20 [14:38:06] 🔧 バグ1を修正中...
VM594:20 [14:38:06] ✅ バグ1の修正が完了しました
VM594:20 [14:38:07] 🔍 バグ2を検出: 型エラー - 文字列を数値として使用
VM594:20 [14:38:07] 🔧 バグ2を修正中...
VM594:20 [14:38:08] ✅ バグ2の修正が完了しました
VM594:20 [14:38:08] 🔍 バグ3を検出: ロジックエラー - 無限ループの可能性
VM594:20 [14:38:09] 🔧 バグ3を修正中...
VM594:20 [14:38:09] ✅ バグ3の修正が完了しました
VM594:20 [14:38:10] 🔍 バグ4を検出: パフォーマンス - 非効率な配列操作
VM594:20 [14:38:10] 🔧 バグ4を修正中...
VM594:20 [14:38:11] ✅ バグ4の修正が完了しました
VM594:20 [14:38:11] 🎉 すべてのバグ修正が完了しました！
```

このように、**正確に0.5秒間隔**でバグが検出・修正されることが分かります！

### 次に、実際に使えそうなコード修正を体験してみよう

:::message alert
このサンプルコードは、ブラウザでの自動実行の仕組みを理解するための例示です。実際に試す場合は、類似のサンプルコードを作成するなど、ご自身のご判断と責任のもと行なってください。環境やブラウザの違いにより動作が異なる場合があります。
:::

実際のコード修正を体験したい場合は、以下のコードも参考にしてみてください：

:::details 🚀【体験】 実際のコード修正デモ（クリックして展開） 🚀

```javascript
// 実際のコード修正デモ
class CodeFixDemo {
	constructor() {
		// 既存のコンテナがあれば削除
		const existingContainer = document.getElementById("code-fix-demo");
		if (existingContainer) {
			existingContainer.remove();
		}

		// 新しいコンテナを作成
		this.container = document.createElement("div");
		this.container.id = "code-fix-demo";
		this.container.style.position = "fixed";
		this.container.style.top = "20px";
		this.container.style.left = "20px";
		this.container.style.background = "white";
		this.container.style.border = "2px solid #dc3545";
		this.container.style.padding = "20px";
		this.container.style.borderRadius = "10px";
		this.container.style.zIndex = "10000";
		this.container.style.fontFamily = "monospace";
		this.container.style.maxWidth = "500px";
		this.container.style.boxShadow = "0 4px 20px rgba(0,0,0,0.2)";
		this.container.style.fontSize = "12px";

		document.body.appendChild(this.container);
		console.log("🐛 コード修正デモのUIを初期化しました");
	}

	log(message, type = "info") {
		const timestamp = new Date().toLocaleTimeString();
		const logDiv = document.createElement("div");
		logDiv.textContent = `[${timestamp}] ${message}`;
		logDiv.style.marginBottom = "5px";
		logDiv.style.padding = "5px";
		logDiv.style.borderRadius = "3px";

		// タイプに応じて色を設定
		if (type === "error") {
			logDiv.style.background = "#f8d7da";
			logDiv.style.color = "#721c24";
		} else if (type === "success") {
			logDiv.style.background = "#d4edda";
			logDiv.style.color = "#155724";
		} else {
			logDiv.style.background = "#d1ecf1";
			logDiv.style.color = "#0c5460";
		}

		this.container.appendChild(logDiv);
		console.log(`[${timestamp}] ${message}`); // コンソールにも出力
	}

	showCode(code, title, isFixed = false) {
		const titleDiv = document.createElement("div");
		titleDiv.textContent = title;
		titleDiv.style.fontWeight = "bold";
		titleDiv.style.marginBottom = "5px";
		titleDiv.style.color = isFixed ? "#28a745" : "#dc3545";

		const codeDiv = document.createElement("div");
		codeDiv.textContent = code;
		codeDiv.style.background = isFixed ? "#f8fff8" : "#fff8f8";
		codeDiv.style.border = `1px solid ${isFixed ? "#28a745" : "#dc3545"}`;
		codeDiv.style.padding = "10px";
		codeDiv.style.margin = "10px 0";
		codeDiv.style.borderRadius = "5px";
		codeDiv.style.whiteSpace = "pre-wrap";
		codeDiv.style.fontSize = "11px";

		this.container.appendChild(titleDiv);
		this.container.appendChild(codeDiv);
	}

	start() {
		console.log("🐛 コード修正デモを開始します...");
		this.log("🐛 コード修正デモを開始します...");

		// バグのあるコードを表示
		setTimeout(() => {
			this.log("🔍 バグのあるコードを検出しました", "error");
			this.showCode(
				`function calculateTotal(items) {
	let total = 0;
	for (let i = 0; i <= items.length; i++) {  // バグ: <= は < であるべき
		total += items[i];
	}
	return total;
}

const numbers = [1, 2, 3, 4, 5];
console.log(calculateTotal(numbers));  // 結果: NaN (undefined + 数値)`,
				"❌ バグのあるコード"
			);
		}, 1000);

		// 修正コードを表示
		setTimeout(() => {
			this.log("🔧 コードを修正中...", "info");
		}, 3000);

		setTimeout(() => {
			this.log("✅ 修正が完了しました！", "success");
			this.showCode(
				`function calculateTotal(items) {
	let total = 0;
	for (let i = 0; i < items.length; i++) {  // 修正: < に変更
		total += items[i];
	}
	return total;
}

const numbers = [1, 2, 3, 4, 5];
console.log(calculateTotal(numbers));  // 結果: 15 (正しい合計)`,
				"✅ 修正されたコード",
				true
			);
		}, 4000);

		// テスト実行
		setTimeout(() => {
			this.log("🧪 テストを実行中...", "info");
		}, 6000);

		setTimeout(() => {
			this.log("🎉 すべてのテストが通りました！", "success");
		}, 7000);
	}
}

// デモを実行
console.log("🔧 コード修正デモを開始します...");
try {
	const codeFixDemo = new CodeFixDemo();
	codeFixDemo.start();
	console.log("✅ デモが正常に開始されました");
} catch (error) {
	console.error("❌ デモの実行中にエラーが発生しました:", error);
}
```

:::

### 実行方法

1. **ブラウザの開発者ツールを開く**
2. **Consoleタブを選択**
3. **上記のコードをコピーして貼り付け**
4. **Enterキーを押す**

すると、画面左上部にコード修正デモのウィンドウが表示され、バグの検出から修正までのプロセスが自動的に実行されます。
（※ こちらのサンプルでは、リロードすると、デモ用UIとコンソールデータが消去されます。）

### 実行結果

コンソールで以下のような出力が表示されます：

```
🐛 コード修正デモのUIを初期化しました
VM543:76 🐛 コード修正デモを開始します...
VM543:51 [14:35:38] 🐛 コード修正デモを開始します...
VM543:135 ✅ デモが正常に開始されました
VM543:51 [14:35:39] 🔍 バグのあるコードを検出しました
VM543:51 [14:35:41] 🔧 コードを修正中...
VM543:51 [14:35:42] ✅ 修正が完了しました！
VM543:51 [14:35:44] 🧪 テストを実行中...
VM543:51 [14:35:45] 🎉 すべてのテストが通りました！
```

このように、**正確に1.5秒間隔**でバグが検出・修正されることが分かります！

---

知識も経験もまだ浅い私ですが、AIツールの可能性にとても魅力を感じています。今後も地道に学びを重ねていきたいと思います。

**少しでも参考になれば幸いです！** ✨

## 参考リンク

- [Cursor AI コーディング支援](https://cursor.sh/)
- [PlayWright GitHubリポジトリ](https://github.com/microsoft/playwright/)
- [claude-task-master GitHubリポジトリ](https://github.com/eyaltoledano/claude-task-master?tab=readme-ov-file)
- [claude-task-master チュートリアル（docs/tutorial.md）](https://github.com/eyaltoledano/claude-task-master/blob/main/docs/tutorial.md)
