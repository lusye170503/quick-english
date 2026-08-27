<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>瞬間英作文リアル音声コーチ</title>
    <style>
        :root {
            --primary: #1A2B4C;
            --accent: #D97706;
            --accent-dark: #B45309;
            --bg: #F8FAFC;
            --card-bg: #FFFFFF;
            --text: #1E293B;
            --text-muted: #64748B;
            --border: #E2E8F0;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Hiragino Sans", "Yu Gothic", sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 0;
            padding-bottom: 70px;
            line-height: 1.6;
        }

        header {
            background-color: var(--primary);
            color: #fff;
            padding: 16px;
            text-align: center;
            font-size: 1.1rem;
            font-weight: bold;
        }

        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 15px;
        }

        .card {
            background: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 15px;
            border: 1px solid var(--border);
            box-shadow: 0 2px 8px rgba(0,0,0,0.04);
        }

        .badge {
            display: inline-block;
            background: var(--accent);
            color: #fff;
            font-size: 0.75rem;
            padding: 2px 8px;
            border-radius: 12px;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .question-box {
            font-size: 1.2rem;
            font-weight: bold;
            color: var(--primary);
            margin: 10px 0;
            padding: 12px;
            background: #F1F5F9;
            border-radius: 8px;
            border-left: 4px solid var(--primary);
        }

        textarea, input[type="text"], input[type="password"] {
            width: 100%;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid var(--border);
            box-sizing: border-box;
            font-size: 1rem;
            margin-top: 8px;
        }

        .btn-main {
            background-color: var(--primary);
            color: #fff;
            border: none;
            padding: 14px;
            width: 100%;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
        }
        .btn-main:disabled { background-color: #94A3B8; }

        .btn-mic {
            background-color: #EF4444;
            color: #fff;
            border: none;
            padding: 14px;
            width: 100%;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .btn-mic.recording {
            background-color: #DC2626;
            animation: pulse 1.5s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.6; }
            100% { opacity: 1; }
        }

        .fb-box {
            background: #F8FAFC;
            border-left: 4px solid var(--accent);
            padding: 15px;
            border-radius: 6px;
            margin-top: 15px;
            white-space: pre-wrap;
        }

        .nav-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 60px;
            background: var(--card-bg);
            display: flex;
            border-top: 1px solid var(--border);
        }
        .nav-item {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 0.75rem;
            color: var(--text-muted);
            cursor: pointer;
        }
        .nav-item.active { color: var(--primary); font-weight: bold; }

        .page { display: none; }
        .page.active { display: block; }

        /* A4印刷用スタイル */
        @media print {
            @page { size: A4; margin: 15mm 12mm; }
            body { background: #fff; padding-bottom: 0; }
            header, .nav-bar, .no-print { display: none !important; }
            .container { max-width: 100%; padding: 0; }
            .print-note {
                box-shadow: none !important;
                border: none !important;
            }
            .callout-box {
                border-left: 4px solid var(--accent) !important;
                background-color: #FFFBEB !important;
                padding: 10px;
                margin: 8px 0;
            }
            h2, h3 { page-break-after: avoid; }
        }
    </style>
</head>
<body>

    <header>🎙️ 瞬間英作文リアル音声コーチ</header>

    <div class="container">
        <!-- 🏠 レッスン画面 -->
        <div id="page-lesson" class="page active">
            <div class="card">
                <span class="badge" id="q-counter">第 1 問 / 20</span>
                <div style="font-size: 0.85rem; color: var(--text-muted);">日本語を見て、瞬時に英語で発話または入力してください。</div>
                <div class="question-box" id="q-text">問題を作成中...</div>

                <div style="margin-top: 15px;">
                    <button class="btn-mic" id="btn-record" onclick="toggleRecording()">🎤 押して録音スタート（音声回答）</button>
                    <div id="audio-preview-container" style="margin-top: 10px; display: none;">
                        <audio id="audio-preview" controls style="width: 100%;"></audio>
                    </div>
                </div>

                <div style="text-align: center; margin: 10px 0; color: var(--text-muted); font-size: 0.85rem;">またはテキスト入力</div>

                <textarea id="user-text-input" placeholder="ここに英文を入力して回答..." rows="2"></textarea>
                <button class="btn-main" id="btn-submit" onclick="submitAnswer()">回答を送信する ➔</button>
            </div>

            <!-- フィードバック表示エリア -->
            <div class="card" id="fb-card" style="display: none;">
                <span class="badge" style="background: var(--primary);">コーチのフィードバック</span>
                <div class="fb-box" id="fb-content"></div>
                <button class="btn-main" onclick="nextQuestion()" style="background-color: var(--accent);">次の問題に進む ➔</button>
            </div>

            <!-- 20問達成時ノート生成ボタン -->
            <div class="card no-print" id="note-trigger-card" style="display: none; text-align: center;">
                <h3>🎉 20問達成！お疲れ様でした！</h3>
                <p style="font-size: 0.9rem; color: var(--text-muted);">今回のレッスンの復習用A4ノートを生成できます。</p>
                <button class="btn-main" style="background-color: var(--accent);" onclick="generateNote()">📄 復習用A4ノートを表示・保存する</button>
            </div>
        </div>

        <!-- 📄 A4復習ノート画面 -->
        <div id="page-note" class="page">
            <div class="no-print" style="margin-bottom: 15px;">
                <button class="btn-main" onclick="switchTab('lesson')">← レッスンに戻る</button>
                <button class="btn-main" style="background-color: var(--accent); margin-top: 8px;" onclick="window.print()">🖨️ A4 PDFとして印刷・保存する</button>
            </div>
            <div id="note-render-area" class="card print-note">
                <p>20問完了後にノートがここに生成されます。</p>
            </div>
        </div>

        <!-- ⚙️ 設定画面 -->
        <div id="page-settings" class="page">
            <div class="card">
                <h3>🔑 Gemini APIキー設定</h3>
                <input type="password" id="api-key-input" placeholder="AI StudioのAPIキーを貼り付け" onchange="saveApiKey()">
                <p style="font-size: 0.8rem; color: #10B981; display: none;" id="key-saved-msg">✅ APIキーを保存しました</p>
            </div>
        </div>
    </div>

    <div class="nav-bar no-print">
        <div class="nav-item active" onclick="switchTab('lesson', this)">🏋️ レッスン</div>
        <div class="nav-item" onclick="switchTab('note', this)">📄 復習ノート</div>
        <div class="nav-item" onclick="switchTab('settings', this)">⚙️ 設定</div>
    </div>

    <script>
        let currentQIndex = 1;
        let historyLogs = []; // 全問題と回答の記録
        let mediaRecorder = null;
        let audioChunks = [];
        let currentAudioBase64 = null;
        let currentAudioMime = "";
        let currentQuestionText = "";

        document.addEventListener("DOMContentLoaded", () => {
            const savedKey = localStorage.getItem('gemini_api_key');
            if (savedKey) {
                document.getElementById('api-key-input').value = savedKey;
                document.getElementById('key-saved-msg').style.display = 'block';
            }
            fetchNextQuestion();
        });

        function saveApiKey() {
            const key = document.getElementById('api-key-input').value.trim();
            if (key) {
                localStorage.setItem('gemini_api_key', key);
                document.getElementById('key-saved-msg').style.display = 'block';
            }
        }

        function switchTab(tabId, el) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            document.getElementById('page-' + tabId).classList.add('active');
            if (el) el.classList.add('active');
        }

        // 🎙️ 録音処理
        async function toggleRecording() {
            const btn = document.getElementById('btn-record');
            if (mediaRecorder && mediaRecorder.state === "recording") {
                mediaRecorder.stop();
                btn.classList.remove('recording');
                btn.innerText = "🎤 押して録音スタート（音声回答）";
            } else {
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                    mediaRecorder = new MediaRecorder(stream);
                    audioChunks = [];
                    mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                    mediaRecorder.onstop = async () => {
                        const audioBlob = new Blob(audioChunks, { type: mediaRecorder.mimeType || 'audio/webm' });
                        currentAudioMime = audioBlob.type.split(';')[0];
                        const reader = new FileReader();
                        reader.readAsDataURL(audioBlob);
                        reader.onloadend = () => {
                            currentAudioBase64 = reader.result.split(',')[1];
                            const audioPreview = document.getElementById('audio-preview');
                            audioPreview.src = reader.result;
                            document.getElementById('audio-preview-container').style.display = 'block';
                        };
                    };
                    mediaRecorder.start();
                    btn.classList.add('recording');
                    btn.innerText = "⏹️ 録音中... 再度押して停止";
                } catch (err) {
                    alert("マイクの使用許可が必要です。");
                }
            }
        }

        async function callGemini(parts) {
            const apiKey = localStorage.getItem('gemini_api_key');
            if (!apiKey) throw new Error("APIキーが設定されていません。");

            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${apiKey}`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ contents: [{ role: "user", parts: parts }] })
            });

            if (!response.ok) throw new Error("通信エラー");
            const data = await response.json();
            return data.candidates[0].content.parts[0].text;
        }

        async function fetchNextQuestion() {
            document.getElementById('q-counter').innerText = `第 ${currentQIndex} 問 / 20`;
            document.getElementById('q-text').innerText = "問題を作成中...";
            document.getElementById('fb-card').style.display = 'none';
            document.getElementById('user-text-input').value = '';
            document.getElementById('audio-preview-container').style.display = 'none';
            currentAudioBase64 = null;

            let prompt = `あなたは英会話コーチです。瞬間英作文の出題を1問だけ行います。
条件:
- 中学英語レベル（英検3級〜準2級程度）
- 日常会話でよく使う表現
- 15語以内の短い日本語文1つ
- 直前までの履歴と文型が被らないようにする
- 英語の答えは絶対に出力しないこと

出力形式: 日本語の文章のみを出力してください。`;

            try {
                const result = await callGemini([{ text: prompt }]);
                currentQuestionText = result.trim();
                document.getElementById('q-text').innerText = currentQuestionText;
            } catch (e) {
                document.getElementById('q-text').innerText = "エラー：APIキーを確認してください。";
            }
        }

        async function submitAnswer() {
            const textAns = document.getElementById('user-text-input').value.trim();
            if (!textAns && !currentAudioBase64) return alert("音声で録音するか、テキストを入力してください！");

            document.getElementById('btn-submit').disabled = true;
            document.getElementById('fb-card').style.display = 'block';
            document.getElementById('fb-content').innerText = "コーチが判定・分析中...";

            let systemInstruction = `あなたは専属英会話コーチです。日本語「${currentQuestionText}」に対するユーザーの英訳をフィードバックしてください。
米国英語を基準とし、解説は日本語で行います。

回答フォーマット:
【フィードバック】
・採点（100点満点）：◯点
・良かった点：
・文法ミスの修正：
・より自然な言い方：
・ネイティブがよく使う表現（NAT）：
・覚えておきたいポイント：
${currentAudioBase64 ? '・音声フィードバック：発音、リンキング、イントネーションの良さやアドバイスを褒めて伝えてください。' : ''}`;

            let parts = [];
            if (currentAudioBase64) {
                parts.push({ inline_data: { mime_type: currentAudioMime, data: currentAudioBase64 } });
            }
            parts.push({ text: `テキスト入力回答: ${textAns \vert{}\vert{} 'なし（音声回答）'}\n\n${systemInstruction}` });

            try {
                const fbText = await callGemini(parts);
                document.getElementById('fb-content').innerText = fbText;

                // 履歴保持
                historyLogs.push({
                    index: currentQIndex,
                    q: currentQuestionText,
                    userAns: textAns || "（音声回答）",
                    fb: fbText
                });

                if (currentQIndex % 10 === 0 && currentQIndex < 20) {
                    append10QAnalysis();
                }

                if (currentQIndex === 20) {
                    document.getElementById('note-trigger-card').style.display = 'block';
                }
            } catch (e) {
                document.getElementById('fb-content').innerText = "判定中にエラーが発生しました。";
            } finally {
                document.getElementById('btn-submit').disabled = false;
            }
        }

        function nextQuestion() {
            if (currentQIndex < 20) {
                currentQIndex++;
                fetchNextQuestion();
            } else {
                alert("20問完了しました！復習ノートを生成できます。");
            }
        }

        async function append10QAnalysis() {
            let logsText = historyLogs.slice(-10).map(l => `問${l.index}: ${l.q} \vert{} 回答:${l.userAns}`).join('\n');
            let prompt = `直近10問の学習記録です:\n${logsText}\n\n弱点を分析し、以下のフォーマットでまとめてください:\n【10問ごとの苦手分析レポート】\n■文法解説:\n■単語（品詞含む）:\n■熟語・慣用句:`;
            const analysis = await callGemini([{ text: prompt }]);
            document.getElementById('fb-content').innerText += `\n\n====================\n${analysis}`;
        }

        async function generateNote() {
            switchTab('note');
            const renderArea = document.getElementById('note-render-area');
            renderArea.innerHTML = "<p>A4復習ノートを作成中...</p>";

            let logsFormatted = historyLogs.map(l => `【問${l.index}】${l.q}\nユーザー回答: ${l.userAns}\n${l.fb}`).join('\n---\n');

            let prompt = `以下の20問のレッスン記録から、A4印刷・保存用の美しい「復習用ノート」HTMLを作成してください。

記録:
${logsFormatted}

要件:
1. 色: ネイビー(#1A2B4C)、ゴールド(#D97706)、ライトグレー(#F8FAFC)
2. 構成:
   - タイトル（20問の総括テーマ）
   - 巻頭に「特に間違えやすい重要ポイントまとめ」
   - 各問題の「問題・正解・NAT表現・ポイント」
3. 重要部分は border-left: 4px solid #D97706 のコールアウトボックスにする。
4. HTMLのタグのみ（<div>や<h3>など）を出力し、```html 等の装飾タグは付けないこと。`;

            try {
                const noteHtml = await callGemini([{ text: prompt }]);
                renderArea.innerHTML = noteHtml;
            } catch (e) {
                renderArea.innerHTML = "<p>ノート生成に失敗しました。</p>";
            }
        }
    </script>
</body>
</html>