<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>妖怪ウォッチ - しずおかニュータウン大捜索！</title>
    <style>
        :root {
            --yw-blue: #0072CE;
            --yw-dark-blue: #0A2540;
            --yw-yellow: #FFCC00;
            --yw-red: #E60012;
            --yw-panel-bg: #1B4980;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'M PLUS Rounded 1c', 'Hiragino Kaku Gothic ProN', sans-serif;
            user-select: none;
        }

        body {
            background-color: #000;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        /* スマホ用コンテナ */
        .game-container {
            width: 100%;
            max-width: 430px;
            height: 100vh;
            max-height: 932px;
            position: relative;
            background: radial-gradient(circle, #3ba9f8 0%, #005bb5 100%);
            overflow: hidden;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 20px rgba(255,255,255,0.2);
        }

        /* ウィスパー柄の背景パターン風 */
        .bg-pattern {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            opacity: 0.15;
            background-image: radial-gradient(#fff 20%, transparent 20%);
            background-size: 30px 30px;
            pointer-events: none;
        }

        /* ヘッダー */
        header {
            background: linear-gradient(180deg, #1b68bd 0%, #093c75 100%);
            color: #fff;
            padding: 12px;
            text-align: center;
            font-size: 1.1rem;
            font-weight: bold;
            border-bottom: 3px solid var(--yw-yellow);
            text-shadow: 2px 2px 0 #000;
            z-index: 10;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .screen {
            flex: 1;
            display: none;
            flex-direction: column;
            position: relative;
            z-index: 2;
        }

        .screen.active {
            display: flex;
        }

        /* --- ① トップ画面 --- */
        #screen-top {
            justify-content: center;
            align-items: center;
            padding: 20px;
            text-align: center;
        }

        .logo-box {
            background: rgba(255,255,255,0.9);
            border: 4px solid var(--yw-red);
            border-radius: 20px;
            padding: 20px 15px;
            box-shadow: 0 8px 0 var(--yw-dark-blue);
            margin-bottom: 30px;
        }

        .logo-title {
            color: var(--yw-red);
            font-size: 1.8rem;
            font-weight: 900;
            text-shadow: 2px 2px 0 var(--yw-yellow);
            letter-spacing: 1px;
        }

        .logo-sub {
            color: #333;
            font-size: 0.95rem;
            font-weight: bold;
            margin-top: 10px;
        }

        .btn-yw {
            background: linear-gradient(180deg, #ffde00 0%, #ff9900 100%);
            color: #000;
            font-size: 1.3rem;
            font-weight: 900;
            padding: 15px 40px;
            border: 3px solid #fff;
            border-radius: 50px;
            box-shadow: 0 6px 0 #b36b00, 0 8px 10px rgba(0,0,0,0.4);
            cursor: pointer;
            transition: 0.1s;
            text-shadow: 1px 1px 0 #fff;
        }

        .btn-yw:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 #b36b00;
        }

        /* --- ② マップ画面 --- */
        .map-wrapper {
            flex: 1;
            position: relative;
            background-color: #d1e8a2;
            /* 原作マップ風背景イラストの模倣グリッド */
            background-image: 
                linear-gradient(#aac973 2px, transparent 2px),
                linear-gradient(90deg, #aac973 2px, transparent 2px);
            background-size: 40px 40px;
            margin: 10px;
            border: 4px solid #fff;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: inset 0 0 10px rgba(0,0,0,0.2);
        }

        /* 吹き出しピン（💬） */
        .map-pin {
            position: absolute;
            width: 70px;
            height: 70px;
            background: #fff;
            border: 3px solid var(--yw-blue);
            border-radius: 50% 50% 50% 0;
            transform: rotate(-45deg);
            display: flex;
            justify-content: center;
            align-items: center;
            box-shadow: 2px 4px 8px rgba(0,0,0,0.3);
            cursor: pointer;
            animation: bounce 1.5s infinite alternate;
        }

        .map-pin-inner {
            transform: rotate(45deg);
            text-align: center;
            font-size: 0.75rem;
            font-weight: bold;
            color: #333;
        }

        .map-pin.cleared {
            background: #e1ffc7;
            border-color: #4CAF50;
        }

        .map-pin.cleared::after {
            content: "トモダチ";
            position: absolute;
            transform: rotate(45deg);
            background: #4CAF50;
            color: #fff;
            font-size: 0.6rem;
            padding: 2px 4px;
            border-radius: 4px;
            bottom: -5px;
        }

        @keyframes bounce {
            0% { transform: rotate(-45deg) translateY(0); }
            100% { transform: rotate(-45deg) translateY(-8px); }
        }

        #pin-1 { top: 20%; left: 15%; }
        #pin-2 { top: 45%; right: 15%; }
        #pin-3 { bottom: 15%; left: 30%; }

        /* --- ③ 原作風ボルト留めパネル（クエストダイアログ） --- */
        .modal-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.75);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 100;
            padding: 15px;
        }

        .yw-panel {
            background: linear-gradient(135deg, #1b4980 0%, #0a2540 100%);
            border: 4px solid #4a90e2;
            border-radius: 12px;
            width: 100%;
            padding: 15px;
            position: relative;
            box-shadow: 0 0 15px rgba(0,0,0,0.8), inset 0 0 10px rgba(255,255,255,0.2);
            color: #fff;
            clip-path: polygon(
                0 15px, 15px 0,
                calc(100% - 15px) 0, 100% 15px,
                100% calc(100% - 15px), calc(100% - 15px) 100%,
                15px 100%, 0 calc(100% - 15px)
            );
        }

        /* ビス（ボルト）アイコン */
        .yw-panel::before, .yw-panel::after {
            content: "⚙️";
            position: absolute;
            font-size: 0.8rem;
            opacity: 0.7;
        }
        .yw-panel::before { top: 5px; left: 5px; }
        .yw-panel::after { bottom: 5px; right: 5px; }

        .yw-header-badge {
            background: var(--yw-yellow);
            color: #000;
            display: inline-block;
            padding: 3px 12px;
            border-radius: 15px;
            font-weight: bold;
            font-size: 0.85rem;
            margin-bottom: 8px;
            border: 2px solid #fff;
        }

        .yw-title {
            font-size: 1.4rem;
            color: #fff;
            text-shadow: 2px 2px 0 #000;
            margin-bottom: 10px;
            border-bottom: 2px solid #336699;
            padding-bottom: 5px;
        }

        .yw-content-box {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            align-items: center;
        }

        .yw-avatar {
            width: 80px;
            height: 80px;
            background: rgba(255,255,255,0.1);
            border: 2px solid #fff;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2.5rem;
            flex-shrink: 0;
        }

        .yw-text {
            font-size: 0.9rem;
            line-height: 1.5;
            background: rgba(0,0,0,0.3);
            padding: 10px;
            border-radius: 8px;
            border: 1px solid #336699;
            flex: 1;
        }

        ruby {
            ruby-position: over;
        }
        rt {
            font-size: 0.55rem;
            color: var(--yw-yellow);
        }

        /* --- ④ 原作風トモダチウィンドウ --- */
        .friend-window {
            background: rgba(255, 255, 255, 0.95);
            border: 4px solid #b38bff;
            border-radius: 15px;
            padding: 12px 15px;
            color: #000;
            display: flex;
            align-items: center;
            gap: 12px;
            box-shadow: 0 6px 12px rgba(0,0,0,0.3);
            margin-top: 10px;
        }

        .friend-medal {
            width: 45px;
            height: 45px;
            background: linear-gradient(135deg, #ffcc00, #ff9900);
            border: 3px solid #fff;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.2rem;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
            flex-shrink: 0;
        }

        .friend-text {
            font-size: 1rem;
            font-weight: bold;
            line-height: 1.4;
        }

        /* --- ⑤ エンディング画面 --- */
        #screen-end {
            justify-content: center;
            align-items: center;
            padding: 20px;
            text-align: center;
        }

        .end-card {
            background: #fff;
            border: 5px solid var(--yw-yellow);
            border-radius: 20px;
            padding: 25px 15px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.5);
            width: 100%;
        }

        .end-card h2 {
            color: var(--yw-red);
            font-size: 1.5rem;
            margin-bottom: 15px;
            text-shadow: 1px 1px 0 #ffcc00;
        }

        .end-card p {
            font-size: 0.95rem;
            line-height: 1.7;
            color: #333;
            margin-bottom: 20px;
            font-weight: bold;
        }

        .msg-highlight {
            background: #fff0b3;
            padding: 8px;
            border-radius: 8px;
            border: 2px dashed #ff9900;
            color: #d93800;
            margin: 10px 0;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@700;900&display=swap" rel="stylesheet">
</head>
<body>

    <div class="game-container">
        <div class="bg-pattern"></div>

        <!-- ① トップ画面 -->
        <div id="screen-top" class="screen active">
            <div class="logo-box">
                <div class="logo-title">妖怪ウォッチ</div>
                <div class="logo-sub">～しずおかニュータウン大捜索！～</div>
            </div>
            <p style="color:#fff; font-weight:bold; margin-bottom:30px; text-shadow:1px 1px 2px #000;">
                街に妖怪が現れた！<br>妖怪Padを起動して反応を確かめろ！
            </p>
            <button class="btn-yw" onclick="startGame()">妖怪Pad起動！</button>
        </div>

        <!-- ② マップ画面 -->
        <div id="screen-map" class="screen">
            <header>
                <span>🗺️ しずおかニュータウン</span>
                <span style="font-size:0.85rem; background:rgba(0,0,0,0.3); padding:2px 8px; border-radius:10px;">
                    トモダチ: <span id="clear-count" style="color:var(--yw-yellow);">0</span> / 3
                </span>
            </header>

            <div class="map-wrapper">
                <div class="map-pin" id="pin-1" onclick="openQuest(1)">
                    <div class="map-pin-inner">♨️<br>温泉</div>
                </div>
                <div class="map-pin" id="pin-2" onclick="openQuest(2)">
                    <div class="map-pin-inner">🎂<br>ケーキ</div>
                </div>
                <div class="map-pin" id="pin-3" onclick="openQuest(3)">
                    <div class="map-pin-inner">🏺<br>陶芸</div>
                </div>
            </div>
        </div>

        <!-- ⑤ エンディング画面 -->
        <div id="screen-end" class="screen">
            <div class="end-card">
                <h2>🎉 全員とトモダチになった！</h2>
                <p>
                    しずおかニュータウンの平和は守られた！<br>
                    お誕生日おめでとう！！
                </p>
                <div class="msg-highlight">
                    【最後の指令】<br>
                    全クリアしたことを<br>
                    <span style="font-size:1.3rem; color:var(--yw-red);">きょうかちゃんに伝えてね！</span>
                </div>
                <button class="btn-yw" style="font-size:1rem; padding:10px 25px; margin-top:15px;" onclick="resetGame()">もう一度あそぶ</button>
            </div>
        </div>
    </div>

    <!-- ③ & ④ クエスト・トモダチダイアログ -->
    <div class="modal-overlay" id="quest-modal">
        <div class="yw-panel">
            <div class="yw-header-badge" id="modal-badge">妖怪出現</div>
            <div class="yw-title" id="modal-title">妖怪名</div>

            <div class="yw-content-box">
                <div class="yw-avatar" id="modal-icon">👻</div>
                <div class="yw-text" id="modal-text">
                    説明テキスト
                </div>
            </div>

            <!-- トモダチ演出表示エリア -->
            <div class="friend-window" id="friend-window" style="display:none;">
                <div class="friend-medal">🏅</div>
                <div class="friend-text" id="friend-text">
                    〜と ともだちになった！
                </div>
            </div>

            <div style="text-align: right; margin-top: 15px;">
                <button class="btn-yw" id="btn-clear" style="font-size:0.9rem; padding:8px 20px;" onclick="clearQuest()">倒す！（トモダチになる）</button>
                <button class="btn-yw" style="font-size:0.9rem; padding:8px 15px; background:linear-gradient(180deg, #ccc, #888); box-shadow:none;" onclick="closeModal()">閉じる</button>
            </div>
        </div>
    </div>

    <script>
        // 妖怪・クエストデータ（ルビ・説明文付き）
        const quests = {
            1: {
                badge: "銭湯での戦闘…！？",
                name: "のぼせトンマン",
                icon: "🐷",
                desc: "<ruby>夜<rt>よ</rt></ruby>な<ruby>夜<rt>よ</rt></ruby>な<ruby>閉店後<rt>へいてんご</rt></ruby>の<ruby>銭湯<rt>せんとう</rt></ruby>に<ruby>忍<rt>しの</rt></ruby>び込んでお<ruby>風呂<rt>ふろ</rt></ruby>につかるのが<ruby>生<rt>い</rt></ruby>きがい。<br><br><b>【クエスト】</b>温泉でしっかり温まって、のぼせトンマンを満足させよう！"
            },
            2: {
                badge: "お腹空かせ妖怪",
                name: "ひも爺（ひもじい）",
                icon: "👴",
                desc: "<ruby>人<rt>ひと</rt></ruby>のお<ruby>腹<rt>なか</rt></ruby>を<ruby>空<rt>す</rt></ruby>かせる<ruby>力<rt>ちから</rt></ruby>も<ruby>持<rt>も</rt></ruby>っているじいさん<ruby>妖怪<rt>ようかい</rt></ruby>。<br><br><b>【クエスト】</b>美味しいケーキを食べて、ひも爺のお腹を満たしてあげよう！"
            },
            3: {
                badge: "胸のときめき",
                name: "ドキドキ土器（ドキ）",
                icon: "🏺",
                desc: "<ruby>取<rt>と</rt></ruby>り<ruby>憑<rt>つ</rt></ruby>かれると<ruby>胸<rt>むね</rt></ruby>がドキドキして止まらなくなる<ruby>土器<rt>どき</rt></ruby>の<ruby>妖怪<rt>ようかい</rt></ruby>。<br><br><b>【クエスト】</b>陶芸体験で胸のドキドキを形にして、作品を作り上げよう！"
            }
        };

        let clearedQuests = JSON.parse(localStorage.getItem('clearedQuests_yw')) || [];

        function startGame() {
            document.getElementById('screen-top').classList.remove('active');
            document.getElementById('screen-map').classList.add('active');
            updateMapUI();
        }

        let currentQuestId = null;

        function openQuest(id) {
            currentQuestId = id;
            const q = quests[id];
            
            document.getElementById('modal-badge').innerText = q.badge;
            document.getElementById('modal-title').innerText = q.name;
            document.getElementById('modal-icon').innerText = q.icon;
            document.getElementById('modal-text').innerHTML = q.desc;

            const clearBtn = document.getElementById('btn-clear');
            const friendWin = document.getElementById('friend-window');

            if (clearedQuests.includes(id)) {
                clearBtn.style.display = 'none';
                friendWin.style.display = 'flex';
                document.getElementById('friend-text').innerText = `${q.name} と\nともだちになった！`;
            } else {
                clearBtn.style.display = 'inline-block';
                friendWin.style.display = 'none';
            }

            document.getElementById('quest-modal').style.display = 'flex';
        }

        function closeModal() {
            document.getElementById('quest-modal').style.display = 'none';
        }

        function clearQuest() {
            if (!clearedQuests.includes(currentQuestId)) {
                clearedQuests.push(currentQuestId);
                localStorage.setItem('clearedQuests_yw', JSON.stringify(clearedQuests));
            }

            const q = quests[currentQuestId];
            document.getElementById('btn-clear').style.display = 'none';
            const friendWin = document.getElementById('friend-window');
            document.getElementById('friend-text').innerText = `${q.name} と\nともだちになった！`;
            friendWin.style.display = 'flex';

            updateMapUI();

            // 全クリア判定
            if (clearedQuests.length === 3) {
                setTimeout(() => {
                    closeModal();
                    document.getElementById('screen-map').classList.remove('active');
                    document.getElementById('screen-end').classList.add('active');
                }, 1800);
            }
        }

        function updateMapUI() {
            clearedQuests.forEach(id => {
                const pin = document.getElementById(`pin-${id}`);
                if (pin) pin.classList.add('cleared');
            });
            document.getElementById('clear-count').innerText = clearedQuests.length;

            if (clearedQuests.length === 3 && document.getElementById('screen-map').classList.contains('active')) {
                document.getElementById('screen-map').classList.remove('active');
                document.getElementById('screen-end').classList.add('active');
            }
        }

        updateMapUI();

        function resetGame() {
            localStorage.removeItem('clearedQuests_yw');
            clearedQuests = [];
            location.reload();
        }
    </script>
</body>
</html>