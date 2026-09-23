<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>キャラクター相関図</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: 'Helvetica Neue', Arial, sans-serif;
            background-color: #1e1e24;
            color: #fff;
            overflow: hidden;
            width: 100vw;
            height: 100vh;
        }
        /* 外枠（ステージ全体） */
        #canvas-container {
            width: 100%;
            height: 100%;
            position: relative;
            cursor: grab;
            overflow: hidden;
        }
        #canvas-container:active { cursor: grabbing; }
        /* 広大な背景（スクロール用スペース） */
        #workspace {
            width: 3000px;
            height: 3000px;
            position: absolute;
            top: 0;
            left: 0;
            background-image: radial-gradient(#333 1px, transparent 1px);
            background-size: 20px 20px;
        }
        /* 関係線を描くSVG */
        #svg-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }
        /* キャラクターアイコン */
        .character {
            position: absolute;
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: #2a2a35;
            border: 3px solid #00ffff;
            box-shadow: 0 0 15px rgba(0,255,255,0.4);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
            z-index: 10;
            transition: border-color 0.3s, box-shadow 0.3s, background-color 0.3s;
            font-size: 14px;
            font-weight: bold;
            text-align: center;
            padding: 5px;
        }
        /* クローズ状態（線を隠す設定） */
        .character.close {
            border-color: #ff4757;
            box-shadow: 0 0 15px rgba(255,71,87,0.4);
            background: #3a2226;
        }
        .character .status-badge {
            font-size: 9px;
            background: rgba(0,0,0,0.5);
            padding: 2px 6px;
            border-radius: 10px;
            margin-top: 4px;
            color: #00ffff;
        }
        .character.close .status-badge {
            color: #ff4757;
        }
        /* 説明パネル */
        #info-panel {
            position: fixed;
            top: 10px;
            left: 10px;
            background: rgba(0,0,0,0.8);
            padding: 10px;
            border-radius: 8px;
            font-size: 12px;
            z-index: 100;
            pointer-events: none;
            line-height: 1.5;
        }
    </style>
</head>
<body>

    <div id="info-panel">
        <strong>【操作方法】</strong><br>
        ・背景をドラッグ/スライド：画面のスクロール<br>
        ・アイコンをタップ：Open / Close 切り替え（線の表示/非表示）
    </div>

    <!-- 全体を動かすためのコンテナ -->
    <div id="canvas-container">
        <div id="workspace">
            <!-- 線を描画するレイヤー -->
            <svg id="svg-layer"></svg>
            
            <!-- キャラクター要素（初期配置はJavaScriptで制御） -->
            <div id="character-container"></div>
        </div>
    </div>

    <script>
        // 1. データ定義（キャラクターと関係性）
        const characters = [
            { id: 'ch1', name: '主人公', x: 400, y: 300, state: 'open' },
            { id: 'ch2', name: 'ライバル', x: 200, y: 150, state: 'open' },
            { id: 'ch3', name: '幼馴染', x: 600, y: 200, state: 'open' },
            { id: 'ch4', name: '謎の組織ボス', x: 300, y: 550, state: 'open' }
        ];

        const relations = [
            { from: 'ch1', to: 'ch2', type: 'ライバル', color: '#ff9f43' },
            { from: 'ch1', to: 'ch3', type: '友人', color: '#1dd1a1' },
            { from: 'ch1', to: 'ch4', type: '敵対', color: '#ff4757' },
            { from: 'ch2', to: 'ch4', type: '裏取引？', color: '#fece57' }
        ];

        const container = document.getElementById('canvas-container');
        const workspace = document.getElementById('workspace');
        const charContainer = document.getElementById('character-container');
        const svgLayer = document.getElementById('svg-layer');

        // 2. 画面のスクロール（ドラッグ＆スライド）機能
        let isDragging = false;
        let startX, startY;
        let scrollLeft = 100, scrollTop = 100; // 初期表示位置の微調整

        // 初期位置を少し中央寄りにセット
        workspace.style.transform = `translate(${-scrollLeft}px, ${-scrollTop}px)`;

        function startDrag(e) {
            // アイコンのタップ時はスクロールさせない
            if (e.target.closest('.character')) return;
            isDragging = true;
            const pageX = e.pageX || e.touches[0].pageX;
            const pageY = e.pageY || e.touches[0].pageY;
            startX = pageX + scrollLeft;
            startY = pageY + scrollTop;
        }

        function moveDrag(e) {
            if (!isDragging) return;
            const pageX = e.pageX || e.touches[0].pageX;
            const pageY = e.pageY || e.touches[0].pageY;
            scrollLeft = startX - pageX;
            scrollTop = startY - pageY;
            
            // 範囲制限（ワークスペースからはみ出さない）
            scrollLeft = Math.max(0, Math.min(scrollLeft, 3000 - window.innerWidth));
            scrollTop = Math.max(0, Math.min(scrollTop, 3000 - window.innerHeight));

            workspace.style.transform = `translate(${-scrollLeft}px, ${-scrollTop}px)`;
        }

        function stopDrag() { isDragging = false; }

        // マウスイベント (PC)
        container.addEventListener('mousedown', startDrag);
        window.addEventListener('mousemove', moveDrag);
        window.addEventListener('mouseup', stopDrag);
        // タッチイベント (スマホ)
        container.addEventListener('touchstart', startDrag);
        window.addEventListener('touchmove', moveDrag, { passive: true });
        window.addEventListener('touchend', stopDrag);


        // 3. キャラクターと線の描画処理
        function init() {
            // キャラクターの配置
            characters.forEach(char => {
                const div = document.createElement('div');
                div.className = `character ${char.state}`;
                div.id = char.id;
                div.style.left = `${char.x}px`;
                div.style.top = `${char.y}px`;
                
                div.innerHTML = `
                    <div>${char.name}</div>
                    <div class="status-badge" id="badge-${char.id}">${char.state.toUpperCase()}</div>
                `;

                // タップイベント（Open/Close切り替え）
                div.addEventListener('click', (e) => {
                    e.stopPropagation();
                    const targetChar = characters.find(c => c.id === char.id);
                    if (targetChar.state === 'open') {
                        targetChar.state = 'close';
                        div.classList.add('close');
                        document.getElementById(`badge-${char.id}`).innerText = 'CLOSE';
                    } else {
                        targetChar.state = 'open';
                        div.classList.remove('close');
                        document.getElementById(`badge-${char.id}`).innerText = 'OPEN';
                    }
                    drawLines(); // 状態が変わったので線を再描画
                });

                charContainer.appendChild(div);
            });

            drawLines();
        }

        // 4. 関係線を描画する関数
        function drawLines() {
            svgLayer.innerHTML = ''; // 一旦クリア

            relations.forEach(rel => {
                const fromChar = characters.find(c => c.id === rel.from);
                const toChar = characters.find(c => c.id === rel.to);

                // どちらか一方でも 'close' 状態なら線を描画しない
                if (fromChar.state === 'close' || toChar.state === 'close') return;

                // アイコンの中心点を計算 (幅100px, 高さ100px なので +50)
                const x1 = fromChar.x + 50;
                const y1 = fromChar.y + 50;
                const x2 = toChar.x + 50;
                const y2 = toChar.y + 50;

                // 直線を作成
                const line = document.createElementNS('http://w3.org', 'line');
                line.setAttribute('x1', x1);
                line.setAttribute('y1', y1);
                line.setAttribute('x2', x2);
                line.setAttribute('y2', y2);
                line.setAttribute('stroke', rel.color);
                line.setAttribute('stroke-width', '3');
                line.setAttribute('opacity', '0.8');
                svgLayer.appendChild(line);

                // 中間点に関係性テキストを配置
                const mx = (x1 + x2) / 2;
                const my = (y1 + y2) / 2;

                // テキストの背景（見やすくするための黒い四角）
                const rect = document.createElementNS('http://w3.org', 'rect');
                rect.setAttribute('x', mx - 35);
                rect.setAttribute('y', my - 12);
                rect.setAttribute('width', '70');
                rect.setAttribute('height', '24');
                rect.setAttribute('fill', '#1e1e24');
                rect.setAttribute('rx', '5'); // 角丸
                rect.setAttribute('stroke', rel.color);
                rect.setAttribute('stroke-width', '1');
                svgLayer.appendChild(rect);

                // 文字本体
                const text = document.createElementNS('http://w3.org', 'text');
                text.setAttribute('x', mx);
                text.setAttribute('y', my + 5);
                text.setAttribute('fill', '#fff');
                text.setAttribute('font-size', '11px');
                text.setAttribute('text-anchor', 'middle');
                text.textContent = rel.type;
                svgLayer.appendChild(text);
            });
        }

        // 起動
        init();
    </script>
</body>
</html>
