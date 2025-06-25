# <!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mermaid ローカル版ビューワー</title>
    <!-- ローカルのMermaid.jsファイルを読み込み -->
    <script src="./mermaid.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Hiragino Kaku Gothic ProN', 'ヒラギノ角ゴ ProN W3', Meiryo, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            margin: 0;
            padding: 0;
            overflow: hidden;
            color: #2c3e50;
        }
        
        .container {
            width: 100vw;
            height: 100vh;
            background: rgba(255, 255, 255, 0.95);
            display: flex;
            flex-direction: column;
            animation: slideUp 0.8s ease-out;
        }

        @keyframes slideUp {
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        
        .header {
            background: linear-gradient(135deg, #2c3e50, #3498db);
            color: white;
            padding: 8px 16px;
            position: relative;
            overflow: hidden;
            flex-shrink: 0;
        }

        .header::before {
            content: '';
            position: absolute;
            top: -50%;
            right: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);
            animation: rotate 20s linear infinite;
        }

        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        .header-content {
            position: relative;
            z-index: 2;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .product-name {
            font-size: 1.4rem;
            font-weight: 700;
            letter-spacing: 1px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }
        
        .subtitle {
            font-size: 0.8rem;
            opacity: 0.9;
            font-weight: 300;
            margin-left: 15px;
        }
        
        .status {
            padding: 6px 16px;
            background: linear-gradient(135deg, rgba(46, 204, 113, 0.9), rgba(39, 174, 96, 0.9));
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            color: white;
            font-size: 12px;
            flex-shrink: 0;
            font-weight: 500;
        }
        
        .status.error {
            background: linear-gradient(135deg, rgba(231, 76, 60, 0.9), rgba(192, 57, 43, 0.9));
        }
        
        .controls {
            padding: 8px 16px;
            background: rgba(255, 255, 255, 0.9);
            border-bottom: 1px solid rgba(102, 126, 234, 0.1);
            flex-shrink: 0;
        }
        
        .drop-info {
            margin-bottom: 8px;
            padding: 8px 12px;
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.05), rgba(118, 75, 162, 0.05));
            border-radius: 6px;
            border: 1px solid rgba(102, 126, 234, 0.1);
            font-size: 12px;
        }
        
        .drop-info h4 {
            color: #2c3e50;
            margin-bottom: 4px;
            font-size: 13px;
        }
        
        .drop-info p {
            color: #667eea;
            font-weight: 500;
            margin: 0;
        }
        
        .action-buttons {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
        }
        
        .clear-button, .download-button {
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
            padding: 6px 12px;
            border-radius: 6px;
            border: none;
            cursor: pointer;
            font-size: 12px;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3);
        }
        
        .download-button {
            background: linear-gradient(135deg, #2ecc71, #27ae60);
            box-shadow: 0 4px 20px rgba(46, 204, 113, 0.3);
        }
        
        .clear-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 30px rgba(102, 126, 234, 0.4);
        }
        
        .download-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 30px rgba(46, 204, 113, 0.4);
        }
        
        .filename {
            margin-left: 15px;
            font-weight: 600;
            color: #2c3e50;
        }
        
        .content {
            display: flex;
            flex: 1;
            min-height: 0;
            position: relative;
            overflow: hidden;
        }
        
        .editor-panel {
            width: 33.33%;
            border-right: 1px solid rgba(102, 126, 234, 0.2);
            display: flex;
            flex-direction: column;
            min-width: 200px;
            background: rgba(255, 255, 255, 0.95);
        }
        
        .preview-panel {
            width: 66.67%;
            background: rgba(255, 255, 255, 0.95);
            display: flex;
            flex-direction: column;
            min-width: 200px;
        }
        
        .resizer {
            width: 6px;
            background: linear-gradient(180deg, rgba(102, 126, 234, 0.6), rgba(118, 75, 162, 0.6));
            cursor: col-resize;
            position: relative;
            flex-shrink: 0;
            transition: all 0.3s ease;
        }
        
        .resizer:hover {
            background: linear-gradient(180deg, rgba(102, 126, 234, 0.9), rgba(118, 75, 162, 0.9));
            width: 8px;
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.5);
        }
        
        .resizer::after {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 3px;
            height: 30px;
            background: rgba(255, 255, 255, 0.8);
            border-radius: 2px;
            transition: all 0.3s ease;
        }
        
        .resizer:hover::after {
            background: #ffffff;
            width: 4px;
            height: 40px;
        }
        
        .panel-header {
            background: linear-gradient(135deg, #667eea, #764ba2);
            padding: 8px 12px;
            font-weight: 600;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-shrink: 0;
            color: white;
            font-size: 13px;
        }
        
        .zoom-controls {
            display: flex;
            gap: 4px;
            align-items: center;
        }
        
        .zoom-btn {
            background: rgba(255, 255, 255, 0.2);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.3);
            padding: 4px 8px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 11px;
            min-width: 24px;
            font-weight: 600;
            transition: all 0.3s ease;
        }
        
        .zoom-btn:hover {
            background: rgba(255, 255, 255, 0.3);
            transform: translateY(-1px);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
        }
        
        .zoom-btn:disabled {
            background: rgba(255, 255, 255, 0.1);
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
            opacity: 0.5;
        }
        
        .zoom-info {
            font-size: 11px;
            color: rgba(255, 255, 255, 0.95);
            min-width: 40px;
            text-align: center;
            font-weight: 600;
        }
        
        .editor {
            width: 100%;
            flex: 1;
            border: none;
            padding: 12px;
            font-family: 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;
            font-size: 13px;
            resize: none;
            outline: none;
            background: #ffffff;
            color: #2c3e50;
            line-height: 1.5;
        }
        
        .editor::placeholder {
            color: #667eea;
            opacity: 0.7;
        }
        
        .preview {
            padding: 12px;
            flex: 1;
            overflow: hidden;
            text-align: center;
            position: relative;
            background: #ffffff;
            cursor: grab;
        }
        
        .preview.dragging {
            cursor: grabbing;
        }
        
        .preview-content {
            position: absolute;
            transition: transform 0.1s ease-out;
            transform-origin: top left;
        }
        
        .preview-content svg {
            shape-rendering: geometricPrecision;
            text-rendering: geometricPrecision;
            image-rendering: -webkit-optimize-contrast;
            image-rendering: crisp-edges;
            max-width: none;
            height: auto;
        }
        
        .error {
            color: #e74c3c;
            background: linear-gradient(135deg, rgba(231, 76, 60, 0.1), rgba(192, 57, 43, 0.1));
            border: 1px solid rgba(231, 76, 60, 0.3);
            border-radius: 12px;
            padding: 20px;
            margin: 20px;
            box-shadow: 0 4px 20px rgba(231, 76, 60, 0.2);
        }
        
        .info {
            color: #667eea;
            font-style: italic;
            margin: 50px 20px;
            line-height: 1.6;
        }
        
        .drop-zone {
            border: 2px dashed rgba(102, 126, 234, 0.5);
            border-radius: 8px;
            padding: 40px 15px;
            margin: 20px;
            text-align: center;
            transition: all 0.3s ease;
            background: rgba(102, 126, 234, 0.05);
        }
        
        .drop-zone.dragover {
            border-color: rgba(102, 126, 234, 0.9);
            background: rgba(102, 126, 234, 0.1);
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.3);
        }
        
        .drop-zone h3 {
            color: #667eea;
            margin-bottom: 10px;
            font-weight: 600;
            font-size: 1.2rem;
        }
        
        .drop-zone p {
            color: #764ba2;
            margin: 8px 0;
            line-height: 1.4;
            font-size: 13px;
        }
        
        .setup-guide {
            background: linear-gradient(135deg, rgba(255, 193, 7, 0.1), rgba(255, 152, 0, 0.1));
            border: 1px solid rgba(255, 193, 7, 0.3);
            border-radius: 12px;
            padding: 20px;
            margin: 20px;
            color: #f39c12;
            box-shadow: 0 4px 20px rgba(255, 193, 7, 0.2);
        }
        
        .setup-guide h3 {
            margin-top: 0;
            color: #e67e22;
        }
        
        .setup-guide code {
            background: rgba(255, 193, 7, 0.15);
            padding: 2px 6px;
            border-radius: 6px;
            font-family: 'JetBrains Mono', 'Fira Code', monospace;
            color: #d68910;
        }

        @media (max-width: 768px) {
            .product-name {
                font-size: 2rem;
            }
            
            .content {
                flex-direction: column;
            }
            
            .editor-panel,
            .preview-panel {
                width: 100%;
                min-height: 300px;
            }
            
            .resizer {
                display: none;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header class="header">
            <div class="header-content">
                <div>
                    <span class="product-name">🧜‍♀️ Mermaid ローカル版ビューワー</span>
                    <span class="subtitle">CDNに依存しないオフライン対応版</span>
                </div>
            </div>
        </header>
        
        <div id="status" class="status">
            ✅ Mermaid.js ローカル版を使用中
        </div>
        
        <div class="controls">
            <div class="drop-info">
                <h4>📂 ファイル読み込み方法</h4>
                <p>💡 下のエディタエリアにMermaidファイル（.mmd, .txt, .md）をドラッグ&ドロップ</p>
            </div>
            
            <div class="action-buttons">
                <button class="clear-button" onclick="clearAll()">🗑️ クリア</button>
                <button class="download-button" onclick="downloadMermaid()">📝 Mermaid保存</button>
                <button class="download-button" onclick="downloadSVG()">💾 SVG保存</button>
                <span class="filename" id="filename"></span>
            </div>
        </div>
        
        <div class="content">
            <div class="editor-panel">
                <div class="panel-header">📝 Mermaid コード</div>
                <textarea class="editor" id="editor" placeholder="💡 Mermaidコードの入力方法:

【方法1】ここにMermaidファイルをドラッグ&ドロップ
【方法2】直接コピー&ペースト

ファイル読み込み後も、ここで自由に編集できます！

📊 サンプルコード:
graph TD
    A[開始] --> B{条件}
    B -->|Yes| C[処理1]
    B -->|No| D[処理2]
    C --> E[終了]
    D --> E

🎯 その他の図表タイプ:
sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    Bob-->>Alice: Great!

pie title ペットの種類
    &quot;犬&quot; : 45
    &quot;猫&quot; : 30
    &quot;鳥&quot; : 25"></textarea>
            </div>
            
            <div class="resizer" id="resizer"></div>
            
            <div class="preview-panel">
                <div class="panel-header">
                    <span>🖼️ プレビュー</span>
                    <div class="zoom-controls">
                        <button class="zoom-btn" onclick="zoomOut()" title="縮小">－</button>
                        <span class="zoom-info" id="zoomInfo">100%</span>
                        <button class="zoom-btn" onclick="zoomIn()" title="拡大">＋</button>
                        <button class="zoom-btn" onclick="resetView()" title="リセット">🔄</button>
                        <button class="zoom-btn" onclick="fitToWindow()" title="画面に合わせる">📐</button>
                    </div>
                </div>
                <div class="preview" id="preview">
                    <div class="drop-zone" id="dropZone">
                        <h3>📎 Mermaidファイルをここにドロップ</h3>
                        <p>または左側のエディタに直接コピー&ペースト</p>
                        <p style="margin-top: 20px; font-size: 12px;">
                            <strong>対応形式:</strong> .mmd, .txt, .md<br>
                            <strong>操作方法:</strong> ＋/－で拡大縮小、ドラッグで移動、🔄でリセット
                        </p>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Mermaid.jsが見つからない場合のセットアップガイド -->
        <div id="setupGuide" class="setup-guide" style="display: none;">
            <h3>🚀 セットアップガイド</h3>
            <p><strong>Mermaid.jsファイルをダウンロードして設置してください：</strong></p>
            <ol>
                <li>以下のURLからMermaid.jsをダウンロード：<br>
                    <code>https://cdnjs.cloudflare.com/ajax/libs/mermaid/10.6.1/mermaid.min.js</code></li>
                <li>このHTMLファイルと同じフォルダに <code>mermaid.min.js</code> として保存</li>
                <li>ページを再読み込み</li>
            </ol>
            <p><strong>フォルダ構成例：</strong></p>
            <pre>
📁 mermaid-viewer/
├── 📄 mermaid_viewer.html
└── 📄 mermaid.min.js
            </pre>
        </div>
    </div>

    <script>
        // Mermaid.jsの読み込み確認
        function checkMermaidLoaded() {
            if (typeof mermaid === 'undefined') {
                document.getElementById('status').className = 'status error';
                document.getElementById('status').innerHTML = '❌ Mermaid.js が見つかりません';
                document.getElementById('setupGuide').style.display = 'block';
                return false;
            }
            return true;
        }

        // Mermaid初期化（読み込み確認後）
        function initializeMermaid() {
            if (!checkMermaidLoaded()) return;
            
            mermaid.initialize({
                startOnLoad: false,
                theme: 'default',
                securityLevel: 'loose',
                fontFamily: 'Arial, sans-serif'
            });
        }

        // DOM要素の取得
        const editor = document.getElementById('editor');
        const preview = document.getElementById('preview');
        const filename = document.getElementById('filename');
        const zoomInfo = document.getElementById('zoomInfo');
        const resizer = document.getElementById('resizer');
        const editorPanel = document.querySelector('.editor-panel');
        const previewPanel = document.querySelector('.preview-panel');
        const dropZone = document.getElementById('dropZone');
        
        // ファイル名管理
        let currentFileName = 'newfile';

        // ズームとパン関連の変数
        let currentZoom = 1.0;
        let panX = 0;
        let panY = 0;
        let isDragging = false;
        let lastMouseX = 0;
        let lastMouseY = 0;
        let previewContent = null;

        // リサイザー関連の変数
        let isResizing = false;

        // ドラッグ&ドロップ機能
        function setupDropZone() {
            ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
                editor.addEventListener(eventName, preventDefaults, false);
                preview.addEventListener(eventName, preventDefaults, false);
                dropZone.addEventListener(eventName, preventDefaults, false);
                document.body.addEventListener(eventName, preventDefaults, false);
            });

            function preventDefaults(e) {
                e.preventDefault();
                e.stopPropagation();
            }

            ['dragenter', 'dragover'].forEach(eventName => {
                editor.addEventListener(eventName, highlight, false);
                preview.addEventListener(eventName, highlight, false);
                dropZone.addEventListener(eventName, highlight, false);
            });

            ['dragleave', 'drop'].forEach(eventName => {
                editor.addEventListener(eventName, unhighlight, false);
                preview.addEventListener(eventName, unhighlight, false);
                dropZone.addEventListener(eventName, unhighlight, false);
            });

            function highlight(e) {
                if (dropZone) {
                    dropZone.classList.add('dragover');
                }
            }

            function unhighlight(e) {
                if (dropZone) {
                    dropZone.classList.remove('dragover');
                }
            }

            editor.addEventListener('drop', handleDrop, false);
            preview.addEventListener('drop', handleDrop, false);
            dropZone.addEventListener('drop', handleDrop, false);

            function handleDrop(e) {
                const dt = e.dataTransfer;
                const files = dt.files;

                if (files.length > 0) {
                    handleFiles(files);
                }
            }
        }

        // ファイル処理
        function handleFiles(files) {
            const file = files[0];
            if (file && (file.name.endsWith('.mmd') || file.name.endsWith('.txt') || file.name.endsWith('.md'))) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const content = e.target.result;
                    editor.value = content;
                    
                    // ファイル名を保存（拡張子なし）
                    currentFileName = file.name.replace(/\.(mmd|txt|md)$/, '');
                    filename.textContent = `📄 ${file.name}`;
                    renderMermaid();
                };
                reader.readAsText(file);
            } else {
                alert('対応ファイル形式: .mmd, .txt, .md');
            }
        }

        // エディタの内容変更時にリアルタイムでプレビュー更新
        editor.addEventListener('input', function() {
            renderMermaid();
        });

        // Mermaidレンダリング関数
        async function renderMermaid() {
            if (!checkMermaidLoaded()) return;
            
            const code = editor.value.trim();
            
            if (!code) {
                preview.innerHTML = `
                    <div class="drop-zone" id="dropZone">
                        <h3>📎 Mermaidファイルをここにドロップ</h3>
                        <p>または左側のエディタに直接コピー&ペースト</p>
                        <p style="margin-top: 20px; font-size: 12px;">
                            <strong>対応形式:</strong> .mmd, .txt, .md<br>
                            <strong>操作方法:</strong> ＋/－で拡大縮小、ドラッグで移動、🔄でリセット
                        </p>
                    </div>
                `;
                setupDropZone();
                return;
            }

            try {
                // プレビューをクリア
                preview.innerHTML = '<div class="preview-content" id="preview-content"></div>';
                previewContent = document.getElementById('preview-content');
                
                // Mermaidでレンダリング
                const { svg } = await mermaid.render('mermaid-graph', code);
                previewContent.innerHTML = svg;
                
                // ズームとパンをリセット
                resetView();
                
                // ドラッグイベントを設定
                setupDragEvents();
                
                // 少し遅延してから自動フィット（SVGの描画完了を待つ）
                setTimeout(() => {
                    fitToWindow();
                }, 100);
                
            } catch (error) {
                preview.innerHTML = `<div class="error">
                    <strong>⚠️ エラーが発生しました:</strong><br>
                    ${error.message}
                </div>`;
            }
        }

        // ズーム機能
        function zoomIn() {
            currentZoom += 0.2;
            updateTransform();
        }

        function zoomOut() {
            if (currentZoom > 0.1) {
                currentZoom -= 0.2;
                updateTransform();
            }
        }

        function resetView() {
            currentZoom = 1.0;
            panX = 0;
            panY = 0;
            updateTransform();
        }

        function fitToWindow() {
            if (!previewContent) return;
            
            const svgElement = previewContent.querySelector('svg');
            if (!svgElement) return;
            
            const previewRect = preview.getBoundingClientRect();
            const svgRect = svgElement.getBoundingClientRect();
            
            const scaleX = (previewRect.width - 40) / svgRect.width;
            const scaleY = (previewRect.height - 40) / svgRect.height;
            
            currentZoom = Math.min(scaleX, scaleY);
            
            // 左上を基準にした位置調整
            panX = 20; // 左端から20pxのマージン
            panY = 20; // 上端から20pxのマージン
            
            updateTransform();
        }

        function updateTransform() {
            if (previewContent) {
                previewContent.style.transformOrigin = 'top left';
                previewContent.style.transform = `translate(${panX}px, ${panY}px) scale(${currentZoom})`;
                zoomInfo.textContent = `${Math.round(currentZoom * 100)}%`;
            }
        }

        // ドラッグ機能
        function setupDragEvents() {
            if (!previewContent) return;
            
            preview.addEventListener('mousedown', startDrag);
            preview.addEventListener('mousemove', drag);
            preview.addEventListener('mouseup', endDrag);
            preview.addEventListener('mouseleave', endDrag);
            
            // ホイールズーム
            preview.addEventListener('wheel', handleWheel);
        }

        function startDrag(e) {
            if (!previewContent || isResizing) return;
            
            isDragging = true;
            lastMouseX = e.clientX;
            lastMouseY = e.clientY;
            preview.classList.add('dragging');
            e.preventDefault();
        }

        function drag(e) {
            if (!isDragging || !previewContent || isResizing) return;
            
            const deltaX = e.clientX - lastMouseX;
            const deltaY = e.clientY - lastMouseY;
            
            panX += deltaX;
            panY += deltaY;
            
            updateTransform();
            
            lastMouseX = e.clientX;
            lastMouseY = e.clientY;
            e.preventDefault();
        }

        function endDrag() {
            isDragging = false;
            preview.classList.remove('dragging');
        }

        function handleWheel(e) {
            if (!previewContent) return;
            
            e.preventDefault();
            
            const delta = e.deltaY > 0 ? -0.1 : 0.1;
            const newZoom = Math.max(0.1, currentZoom + delta);
            
            if (newZoom !== currentZoom) {
                currentZoom = newZoom;
                updateTransform();
            }
        }

        // パネルリサイザー機能
        function setupResizer() {
            resizer.addEventListener('mousedown', startResize);
            document.addEventListener('mousemove', resize);
            document.addEventListener('mouseup', endResize);
        }

        function startResize(e) {
            isResizing = true;
            document.body.style.cursor = 'col-resize';
            document.body.style.userSelect = 'none';
            e.preventDefault();
        }

        function resize(e) {
            if (!isResizing) return;
            
            const containerRect = document.querySelector('.content').getBoundingClientRect();
            const mouseX = e.clientX - containerRect.left;
            const containerWidth = containerRect.width;
            
            // 最小幅200px、最大80%まで
            const minWidth = 200;
            const maxEditorWidth = containerWidth * 0.8;
            const newEditorWidth = Math.max(minWidth, Math.min(maxEditorWidth, mouseX));
            
            const editorPercent = (newEditorWidth / containerWidth) * 100;
            const previewPercent = 100 - editorPercent;
            
            editorPanel.style.width = `${editorPercent}%`;
            previewPanel.style.width = `${previewPercent}%`;
            
            e.preventDefault();
        }

        function endResize() {
            isResizing = false;
            document.body.style.cursor = '';
            document.body.style.userSelect = '';
        }

        // Mermaidダウンロード機能（Save As対応）
        async function downloadMermaid() {
            const mermaidCode = editor.value.trim();
            if (!mermaidCode) {
                alert('保存するMermaidコードがありません');
                return;
            }
            
            const blob = new Blob([mermaidCode], { type: 'text/plain;charset=utf-8' });
            
            // ファイル名を準備
            const defaultFileName = `${currentFileName}.mmd`;
            
            // File System Access API対応ブラウザの場合
            if ('showSaveFilePicker' in window) {
                try {
                    const fileHandle = await window.showSaveFilePicker({
                        suggestedName: defaultFileName,
                        types: [{
                            description: 'Mermaid files',
                            accept: { 
                                'text/plain': ['.mmd', '.txt'],
                                'text/markdown': ['.md']
                            }
                        }]
                    });
                    
                    const writable = await fileHandle.createWritable();
                    await writable.write(blob);
                    await writable.close();
                    
                    alert('Mermaidファイル保存完了！');
                    return;
                } catch (err) {
                    if (err.name !== 'AbortError') {
                        console.error('Save As failed:', err);
                    }
                    // キャンセルされた場合や失敗した場合は従来の方法にフォールバック
                }
            }
            
            // 従来の自動ダウンロード（フォールバック）
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = defaultFileName;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
        }

        // SVGダウンロード機能（Save As対応）
        async function downloadSVG() {
            const svgElement = document.querySelector('#preview-content svg');
            if (!svgElement) {
                alert('ダウンロードするSVGがありません');
                return;
            }
            
            const svgData = new XMLSerializer().serializeToString(svgElement);
            const blob = new Blob([svgData], { type: 'image/svg+xml' });
            
            // ファイル名を準備
            const defaultFileName = `${currentFileName}.svg`;
            
            // File System Access API対応ブラウザの場合
            if ('showSaveFilePicker' in window) {
                try {
                    const fileHandle = await window.showSaveFilePicker({
                        suggestedName: defaultFileName,
                        types: [{
                            description: 'SVG files',
                            accept: { 'image/svg+xml': ['.svg'] }
                        }]
                    });
                    
                    const writable = await fileHandle.createWritable();
                    await writable.write(blob);
                    await writable.close();
                    
                    alert('SVG保存完了！');
                    return;
                } catch (err) {
                    if (err.name !== 'AbortError') {
                        console.error('Save As failed:', err);
                    }
                    // キャンセルされた場合や失敗した場合は従来の方法にフォールバック
                }
            }
            
            // 従来の自動ダウンロード（フォールバック）
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = defaultFileName;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
        }

        // クリア機能
        function clearAll() {
            editor.value = '';
            preview.innerHTML = `
                <div class="drop-zone" id="dropZone">
                    <h3>📎 Mermaidファイルをここにドロップ</h3>
                    <p>または左側のエディタに直接コピー&ペースト</p>
                    <p style="margin-top: 20px; font-size: 12px;">
                        <strong>対応形式:</strong> .mmd, .txt, .md<br>
                        <strong>操作方法:</strong> ＋/－で拡大縮小、ドラッグで移動、🔄でリセット
                    </p>
                </div>
            `;
            filename.textContent = '';
            currentFileName = 'newfile'; // ファイル名をリセット
            previewContent = null;
            resetView();
            setupDropZone();
        }

        // 初期化とサンプル表示
        window.addEventListener('load', function() {
            // 少し遅延してMermaidの読み込みを確認
            setTimeout(() => {
                initializeMermaid();
                setupResizer();
                setupDropZone();
                
                if (checkMermaidLoaded()) {
                    const sampleCode = `graph TD
    A[開始] --> B{条件}
    B -->|Yes| C[処理1]
    B -->|No| D[処理2]
    C --> E[終了]
    D --> E`;
                    
                    editor.value = sampleCode;
                    renderMermaid();
                }
            }, 100);
        });
    </script>
</body>
</html>
