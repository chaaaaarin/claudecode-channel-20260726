# 🎁 Opus 5 挙動お直し処方箋7選

Anthropic公式ドキュメント「Prompting Claude Opus 5」に実際に書かれている指示文ブロックを、症状→処方箋の形でそのまま使える形にまとめました。英語の指示文＋日本語の解説つき。

**受け取り方**: 使いたい項目をコピーするか、このファイルごとダウンロードしてください。

**使い方**: 自分のCLAUDE.mdやシステムプロンプトの末尾に、症状が当てはまる項目だけ貼ってください。全部を一度に入れる必要はありません。

---

## 応答・実況まわり

### 1. 応答が長すぎるのを直す

症状: 何も言わなくても応答が長い。effortを下げても短くならない（effortは「考える量」だけを制御し、「話す量」は別）。

```text
Keep responses focused, brief, and concise. Keep disclaimers and caveats short, and spend most of the response on the main answer. When asked to explain something, give a high-level summary unless an in-depth explanation is specifically requested.
```

長いシステムプロンプトの終盤に置く短縮版:

```text
<tone_preference>
Keep outputs reasonably concise.
</tone_preference>
```

**ポイント**: この症状はeffortを下げるだけでは直らない。応答の長さは別建てで指示する必要がある。

### 2. 作業中の逐一実況を落ち着かせる

症状: 「これからこれをやります」と逐一実況し、1メッセージの分量も多くなりがち。

```text
Before your first tool call, say in one sentence what you're about to do. While working, give a brief update only when you find something important or change direction. When you finish, lead with the outcome: your first sentence should answer "what happened" or "what did you find," with supporting detail after it for readers who want it.
```

**ポイント**: 実況を止めるのではなく「リズム」を指定するのがコツ。

### 3. 書き出す資料そのものの長さを整える

症状: 会話の応答とは別に、ファイルに書き出すレポート・Markdown文書も長くなりやすい。

```text
Match the length of written documents to what the task needs: cover the substance, but do not pad with filler sections, redundant summaries, or boilerplate.
```

**ポイント**: 会話の簡潔さ指示（1番）とは別に必要な指示。

---

## 検証・範囲まわり

### 4. 頼んでもいない確認・範囲拡大を止める

症状: 指示なしで自分の作業を検証する。旧モデル向けの「必ず検証手順を入れて」「サブエージェントで検証して」が残っていると二重に検証してしまう。頼んだ範囲を勝手に広げることもある。

まず旧モデル向けの検証指示（例:「include a final verification step for any non-trivial task」）は削除してから、次の一言を足す:

```text
Deliver what was asked, at the scope intended. Make routine judgment calls yourself, and check in only when different readings of the request would lead to materially different work. If the request seems mistaken or a better approach exists, say so in a sentence and continue with the task as asked rather than quietly narrowing, widening, or transforming it. Finish the whole task, and stop short of actions that are clearly beyond what was asked.
```

**ポイント**: 「引き算」が先。指示を足す前に、古い検証指示を消す。

### 5. サブエージェントの分身しすぎを止める

症状: 独立していない小さな仕事にまで、他のサブエージェントへの委任を使いたがる。コストと時間が余計にかかる。

```text
Delegate to a subagent only for large tasks that are genuinely independent and parallelizable, such as a wide multi-file investigation. Do not delegate work you can finish yourself in a handful of tool calls, and do not use subagents to verify or double-check your own work. If one subagent can complete the task, use one rather than several, and keep spawn counts low.
```

**ポイント**: Fable 5では逆に「積極的に委任せよ」が公式推奨。モデルをまたいで使い回さないこと。

---

## 自己修正・出力まわり

### 6. 些細な言い直しの逐一報告を止める

症状: 自分の間違いを指示なしでよく直せるが、「さっきの発言を訂正します」と些細な言い直しまで逐一ナレーションしてしまう。

```text
Only correct an earlier statement when the error would change the user's code, conclusions, or decisions. State corrections plainly and briefly, then continue the task. For slips that change nothing for the user, make the fix and move on without noting it.
```

**ポイント**: 自己修正そのものを止める指示ではなく、報告するかどうかの基準を絞る指示。

### 7. thinking無効時の出力漏れを防ぐ（開発者向け・API利用時）

症状: Opus 5はthinkingがデフォルトON。effortがhigh以下でないとthinkingを無効化できない（xhigh/maxとdisabledの併用は400エラー）。無理に無効化すると、ツール呼び出しが文章として漏れたり、内部XMLタグが表示に混ざることがある。

公式推奨はthinkingを有効なままeffortを下げること。どうしても無効化が必要な場合のみ、次の一言を足す:

```text
When you use a tool, you may say a brief sentence first. If no tool can express what the user asked for, say so instead of guessing. Do not include internal or system XML tags in your response.
```

**ポイント**: 「thinking」「タグ」と名指しで禁止するより、この一般化した言い方の方が効くと公式ドキュメントに明記されている。

---

以上、7個です。内訳: 応答・実況まわり3／検証・範囲まわり2／自己修正・出力まわり2。
