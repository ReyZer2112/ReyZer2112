<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>2048 O‘yini</title>
<style>
    body {
        font-family: Arial, sans-serif;
        text-align: center;
        background: #faf8ef;
        margin: 0;
        padding: 20px;
    }
    h1 {
        margin-bottom: 10px;
    }
    #scoreboard, #stats {
        display: flex;
        justify-content: center;
        gap: 20px;
        font-size: 18px;
        margin-bottom: 10px;
        flex-wrap: wrap;
    }
    #stats {
        font-size: 16px;
        margin-top: 0;
    }
    #board {
        max-width: 400px;
        margin: 0 auto;
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        grid-gap: 5px;
        background: #bbada0;
        padding: 5px;
        border-radius: 10px;
        user-select: none;
    }
    .tile {
        width: 90px;
        height: 90px;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 24px;
        font-weight: bold;
        background: #cdc1b4;
        color: #776e65;
        border-radius: 5px;
        transition: background 0.3s, color 0.3s, transform 0.2s;
    }
    .tile[data-val="2"]    { background: #eee4da; }
    .tile[data-val="4"]    { background: #ede0c8; }
    .tile[data-val="8"]    { background: #f2b179; color: #fff; }
    .tile[data-val="16"]   { background: #f59563; color: #fff; }
    .tile[data-val="32"]   { background: #f67c5f; color: #fff; }
    .tile[data-val="64"]   { background: #f65e3b; color: #fff; }
    .tile[data-val="128"]  { background: #edcf72; color: #fff; }
    .tile[data-val="256"]  { background: #edcc61; color: #fff; }
    .tile[data-val="512"]  { background: #edc850; color: #fff; }
    .tile[data-val="1024"] { background: #edc53f; color: #fff; }
    .tile[data-val="2048"] { background: #edc22e; color: #fff; }

    #status {
        font-size: 18px;
        margin-top: 10px;
        min-height: 24px;
    }

    button {
        padding: 10px 20px;
        font-size: 16px;
        margin: 10px 5px 0 5px;
        cursor: pointer;
        border-radius: 5px;
        border: none;
        background: #8f7a66;
        color: white;
        transition: background 0.3s;
    }
    button:hover {
        background: #a89078;
    }

    /* Responsive */
    @media (max-width: 480px) {
        #board {
            max-width: 320px;
            grid-gap: 4px;
            padding: 4px;
        }
        .tile {
            width: 70px;
            height: 70px;
            font-size: 20px;
        }
        #scoreboard, #stats {
            font-size: 14px;
            gap: 10px;
        }
    }

    /* Modal styles */
    #modal, #creatorModal {
        display: none;
        position: fixed;
        z-index: 10;
        left: 0; top: 0;
        width: 100%; height: 100%;
        background: rgba(0,0,0,0.5);
        justify-content: center;
        align-items: center;
    }
    #modalContent, #creatorContent {
        background: white;
        padding: 20px;
        border-radius: 10px;
        max-width: 90%;
        max-height: 80%;
        overflow-y: auto;
        box-shadow: 0 0 15px rgba(0,0,0,0.3);
        text-align: left;
        font-size: 16px;
        line-height: 1.5;
        color: #333;
    }
    #modalContent h2, #creatorContent h2 {
        margin-top: 0;
    }
    .closeModalBtn {
        margin-top: 15px;
        background: #d9534f;
    }
</style>
</head>
<body>

<h1>2048 O‘yini</h1>
<div id="scoreboard">
    <div>Ball: <span id="score">0</span></div>
    <div>Harakatlar: <span id="moves">0</span></div>
</div>
<div id="stats">
  <div>Ball rekord: <span id="bestScore">0</span></div>
  <div>G‘alaba soni: <span id="winsCount">0</span></div>
</div>
<div id="board" role="grid" aria-label="2048 o‘yini taxtasi"></div>
<div id="status" role="alert" aria-live="polite"></div>

<button id="restart">🔁 Qaytadan boshlash</button>
<button id="aboutBtn">ℹ️ O‘yin haqida</button>
<button id="creatorBtn">👤 Yaratuvchi haqida</button>

<!-- O'yin haqida Modal -->
<div id="modal" role="dialog" aria-modal="true" aria-labelledby="modalTitle" aria-describedby="modalDesc">
    <div id="modalContent">
        <h2 id="modalTitle">2048 O‘yini haqida</h2>
        <p id="modalDesc">
            2048 o‘yini - bu raqamlarni birlashtirib, 2048 soniga yetishga harakat qiladigan qiziqarli va o‘ylab o‘ynash talab qiladigan o‘yin.<br><br>
            O‘yinni boshqarish uchun klaviaturadagi o‘q tugmalaridan foydalaning: ←, →, ↑, ↓.<br>
            Harakatlar soni va ball yuqorida ko‘rsatilgan.<br><br>
            O‘yin davomida raqamlar birlashib, kattaroq qiymatga ega bo‘ladi.<br>
            2048 ga yetganingizda g‘alaba xabari chiqadi.<br><br>
            Yana boshlash uchun “Qaytadan boshlash” tugmasini bosing.<br><br>
            O‘zingizga omad tilaymiz!
        </p>
        <button id="closeModal" class="closeModalBtn">Yopish</button>
    </div>
</div>

<!-- Yaratuvchi haqida Modal -->
<div id="creatorModal" role="dialog" aria-modal="true" aria-labelledby="creatorTitle" aria-describedby="creatorDesc">
    <div id="creatorContent">
        <h2 id="creatorTitle">Yaratuvchi haqida</h2>
        <pre style="font-family: monospace; white-space: pre-wrap; line-height: 1.4; font-size: 14px;">
╔═══════ஜ۩۞۩ஜ═══════╗  

MENI QIDIRISHING SHART EMAS,  
     MANA LINKLARIM:

🌐Instagram: @reyzer2112
https://www.instagram.com/reyzer2112?igsh=a2Q2aTg2eDVibWJi

🌐YouTube: @ReyZer-2112
https://www.youtube.com/@ReyZer-2112

🙋🏻 Snapchat: reyzer2112
https://www.snapchat.com/add/reyzer2112?share_id=Ha5ktm0sdkc&locale=ru-RU

🎮Roblox: Zoh_1rshox020315
https://www.roblox.com/users/4669071068/profile

🌐Telegram: @ReyZer2112

╚═══════ஜ۩۞۩ஜ═══════╝

Har doim yangilikda bo‘l!
        </pre>
        <button id="closeCreatorModal" class="closeModalBtn">Yopish</button>
    </div>
</div>

<script>
    const size = 4;
    let board = [];
    let score = 0;
    let moves = 0;

    // LocalStorage'dan o'qib olish yoki default qiymat berish
    let bestScore = parseInt(localStorage.getItem('bestScore')) || 0;
    let winsCount = parseInt(localStorage.getItem('winsCount')) || 0;

    // Ovozlar (ogoh bo‘ling: har doim ovoz chiqishi uchun brauzer sozlamalariga ruxsat kerak)
    const sounds = {
        move: new Audio("https://actions.google.com/sounds/v1/cartoon/slide_whistle_to_drum_hit.ogg"),
        merge: new Audio("https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg"),
        win: new Audio("https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg"),
        lose: new Audio("https://actions.google.com/sounds/v1/cartoon/cartoon_boing.ogg")
    };

    function startGame() {
        board = Array(size).fill().map(() => Array(size).fill(0));
        score = 0;
        moves = 0;
        document.getElementById("score").textContent = score;
        document.getElementById("moves").textContent = moves;
        document.getElementById("status").textContent = "";
        updateStats();
        addTile();
        addTile();
        drawBoard();
        document.addEventListener("keydown", keyHandler);
    }

    function addTile() {
        let empty = [];
        for (let i = 0; i < size; i++) {
            for (let j = 0; j < size; j++) {
                if (board[i][j] === 0) empty.push([i, j]);
            }
        }
        if (empty.length === 0) return;
        let [x, y] = empty[Math.floor(Math.random() * empty.length)];
        board[x][y] = Math.random() < 0.9 ? 2 : 4;
    }

    function drawBoard() {
        const boardDiv = document.getElementById("board");
        boardDiv.innerHTML = "";
        for (let i = 0; i < size; i++) {
            for (let j = 0; j < size; j++) {
                const tile = document.createElement("div");
                tile.className = "tile";
                let val = board[i][j];
                tile.textContent = val === 0 ? "" : val;
                tile.setAttribute("data-val", val);
                tile.setAttribute("role", "gridcell");
                boardDiv.appendChild(tile);
            }
        }
    }

    function slide(row) {
        let arr = row.filter(v => v);
        for (let i = 0; i < arr.length - 1; i++) {
            if (arr[i] === arr[i + 1]) {
                arr[i] *= 2;
                score += arr[i]; // ball qo‘shamiz
                arr[i + 1] = 0;
                sounds.merge.play();
            }
        }
        arr = arr.filter(v => v);
        while (arr.length < size) arr.push(0);
        return arr;
    }

    function rotateLeft(mat) {
        return mat[0].map((_, i) => mat.map(row => row[i])).reverse();
    }

    function rotateRight(mat) {
        return mat[0].map((_, i) => mat.map(row => row[i]).reverse());
    }

    function move(dir) {
        let old = JSON.stringify(board);

        if (dir === "left") {
            board = board.map(slide);
        } else if (dir === "right") {
            board = board.map(row => slide(row.reverse()).reverse());
        } else if (dir === "up") {
            board = rotateLeft(board);
            board = board.map(slide);
            board = rotateRight(board);
        } else if (dir === "down") {
            board = rotateLeft(board);
            board = board.map(row => slide(row.reverse()).reverse());
            board = rotateRight(board);
        }

        if (JSON.stringify(board) !== old) {
            addTile();
            drawBoard();
            document.getElementById("score").textContent = score;
            moves++;
            document.getElementById("moves").textContent = moves;
            sounds.move.play();
            checkStatus();
        }
    }

    function checkStatus() {
        if (board.flat().includes(2048)) {
            document.getElementById("status").textContent = "🎉 Siz 2048 ga yetdingiz!";
            sounds.win.play();
            document.removeEventListener("keydown", keyHandler);
            winsCount++; // g'alabani oshiramiz
            if (score > bestScore) {
                bestScore = score; // rekord yangilandi
                localStorage.setItem('bestScore', bestScore);
            }
            localStorage.setItem('winsCount', winsCount);
            updateStats();
        } else if (!canMove()) {
            document.getElementById("status").textContent = "❌ O‘yin tugadi!";
            sounds.lose.play();
            document.removeEventListener("keydown", keyHandler);
        }
    }

    function canMove() {
        for (let i = 0; i < size; i++) {
            for (let j = 0; j < size; j++) {
                if (board[i][j] === 0) return true;
                if (j < size - 1 && board[i][j] === board[i][j + 1]) return true;
                if (i < size - 1 && board[i][j] === board[i + 1][j]) return true;
            }
        }
        return false;
    }

    function keyHandler(e) {
        if (e.key === "ArrowLeft") move("left");
        else if (e.key === "ArrowRight") move("right");
        else if (e.key === "ArrowUp") move("up");
        else if (e.key === "ArrowDown") move("down");
    }

    document.getElementById("restart").addEventListener("click", () => {
        startGame();
    });

    // O'yin haqida Modal tugmalari
    const modal = document.getElementById("modal");
    const aboutBtn = document.getElementById("aboutBtn");
    const closeModal = document.getElementById("closeModal");

    aboutBtn.addEventListener("click", () => {
        modal.style.display = "flex";
    });

    closeModal.addEventListener("click", () => {
        modal.style.display = "none";
    });

    modal.addEventListener("click", (e) => {
        if (e.target === modal) {
            modal.style.display = "none";
        }
    });

    // Yaratuvchi haqida Modal tugmalari
    const creatorModal = document.getElementById("creatorModal");
    const creatorBtn = document.getElementById("creatorBtn");
    const closeCreatorModal = document.getElementById("closeCreatorModal");

    creatorBtn.addEventListener("click", () => {
        creatorModal.style.display = "flex";
    });

    closeCreatorModal.addEventListener("click", () => {
        creatorModal.style.display = "none";
    });

    creatorModal.addEventListener("click", (e) => {
        if (e.target === creatorModal) {
            creatorModal.style.display = "none";
        }
    });

    // Statistika yangilash funksiyasi
    function updateStats() {
        document.getElementById('bestScore').textContent = bestScore;
        document.getElementById('winsCount').textContent = winsCount;
    }

    // O'yin boshlanadi
    startGame();
</script>

</body>
</html>
