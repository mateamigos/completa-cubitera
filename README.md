<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Completa la Cubitera</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap');

        :root {
            --ui-blue: #00AEEF;
            --ui-red: #FF4B4B;
            --ui-green: #4CAF50;
            --ice-cube-color: #f0faff;
        }

        body {
            font-family: 'Fredoka One', cursive;
            /* Fondo oscuro simple para que resalte el brillo */
            background-color: #1a1a1a; 
            margin: 0;
            padding: 0;
            height: 100vh;
            width: 100vw;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            user-select: none;
            -webkit-user-select: none;
        }

        /* --- CONTENEDOR PRINCIPAL CON BRILLO --- */
        #game-console-wrapper {
            background: url('https://lh3.googleusercontent.com/u/0/d/12qORvm8BEHhEkwOlJ24a1ypgbh1iKBWK') no-repeat center center;
            background-size: cover;
            
            width: 95%;
            max-width: 500px;
            aspect-ratio: 9 / 16;
            max-height: 95vh;
            
            /* Borde eliminado, solo esquinas redondeadas suaves */
            border-radius: 25px; 
            
            /* EFECTO DE ILUMINACIÓN BRILLANTE */
            /* Se ha quitado la sombra interior oscura y se ha potenciado el resplandor azul exterior */
            box-shadow: 
                0 0 30px rgba(0, 174, 239, 0.7), /* Brillo intenso cercano */
                0 0 80px rgba(0, 174, 239, 0.3); /* Resplandor difuso lejano */
            
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            padding: 15px;
            box-sizing: border-box;
            position: relative;
            overflow: hidden;
            /* Un borde sutil blanco brillante para definir el límite */
            border: 2px solid rgba(255,255,255,0.2);
        }

        /* Se ha eliminado el efecto de "scanlines" (líneas de TV antigua) */
        
        .header { 
            display: flex; 
            width: 100%;
            justify-content: center;
            margin-bottom: 10px;
            z-index: 20;
        }
        
        #score-container { 
            font-size: 1.5rem; 
            background: linear-gradient(180deg, #00AEEF, #0077a3);
            padding: 5px 30px; 
            border-radius: 10px; 
            border: 3px solid white;
            box-shadow: 0 5px 0 #005f82, inset 0 2px 5px rgba(255,255,255,0.4);
            text-shadow: 2px 2px 0px #004863;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        #board {
            display: grid;
            grid-template-columns: repeat(10, 1fr);
            gap: 3px;
            padding: 8px;
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(5px);
            border-radius: 12px;
            border: 3px solid rgba(255, 255, 255, 0.3);
            width: 100%;
            box-sizing: border-box;
            aspect-ratio: 1 / 1; 
            z-index: 20;
        }

        .cell {
            width: 100%;
            aspect-ratio: 1 / 1;
            background-color: rgba(0, 0, 0, 0.3);
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: inset 1px 1px 3px rgba(0,0,0,0.5);
            font-size: 1rem;
        }
        
        @media (max-width: 380px) { .cell { font-size: 0.8rem; } }

        .correct-cube {
            width: 90%;
            height: 90%;
            background: var(--ice-cube-color);
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 4px;
            font-size: 1.1em;
            box-shadow: inset 0 0 5px white, 2px 2px 4px rgba(0,0,0,0.3);
            pointer-events: none;
            border: 2px solid #badfff;
        }

        .digit-decena { color: var(--ui-red); text-shadow: 1px 1px 0 rgba(0,0,0,0.2); }
        .digit-unidad { color: var(--ui-blue); text-shadow: 1px 1px 0 rgba(0,0,0,0.2); }

        .single-pool { 
            height: auto; 
            flex-grow: 1;
            display: flex; 
            align-items: center; 
            justify-content: center; 
            width: 100%;
            z-index: 20;
            padding: 10px 0;
        }

        .number-card {
            width: 22%; 
            aspect-ratio: 1 / 1;
            background: white;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: clamp(1.5rem, 5vw, 2.5rem); 
            cursor: grab;
            box-shadow: 0 6px 0 #999, 0 8px 15px rgba(0,0,0,0.4);
            touch-action: none;
            border: 3px solid #333;
            color: #333;
            transition: transform 0.1s;
        }
        .number-card:active { transform: translateY(4px); box-shadow: 0 2px 0 #999; }

        .modal-overlay {
            position: fixed; inset: 0; background: rgba(0,0,0,0.9);
            display: flex; align-items: center; justify-content: center;
            z-index: 5000; visibility: hidden; opacity: 0; transition: opacity 0.3s;
            padding: 20px;
        }
        .modal-overlay.active { visibility: visible; opacity: 1; }
        .modal-content { 
            background: white; 
            padding: 30px; 
            border-radius: 25px; 
            text-align: center; 
            color: #333;
            max-width: 90%;
            border: 6px solid var(--ui-blue);
            box-shadow: 0 0 30px rgba(0, 174, 239, 0.5); /* Sombra brillante también en el modal */
        }
        .instrucciones {
            font-size: 1.2rem;
            line-height: 1.5;
            margin: 20px 0;
        }
        .puntos-info {
            font-weight: bold;
            display: flex; justify-content: center; gap: 15px; margin-top: 15px;
            font-size: 1.1rem;
            background: #eee; padding: 10px; border-radius: 50px;
        }
        .acierto { color: var(--ui-green); }
        .error { color: var(--ui-red); }

        .btn-main { 
            background: var(--ui-green); color: white; border: 3px solid white;
            padding: 12px 35px; border-radius: 50px; font-size: 1.4rem;
            font-weight: bold; cursor: pointer; margin-top: 15px; 
            font-family: 'Fredoka One', cursive;
            box-shadow: 0 6px 0 #2E7D32;
            text-transform: uppercase;
            transition: transform 0.1s, box-shadow 0.1s;
        }
        .btn-main:active { transform: translateY(4px); box-shadow: 0 2px 0 #2E7D32; }

    </style>
</head>
<body>

    <div id="game-console-wrapper">
        <div class="header">
            <div id="score-container">PUNTOS: <span id="score">0</span></div>
        </div>

        <div id="board"></div>

        <div class="single-pool" id="numbers-pool"></div>
    </div>

    <div id="start-modal" class="modal-overlay active">
        <div class="modal-content">
            <h1 style="margin: 0; color: var(--ui-blue); font-size: 2rem; line-height: 1.1; text-transform: uppercase; text-shadow: 2px 2px 0 rgba(0,0,0,0.1);">COMPLETA LA CUBITERA</h1>
            <div class="instrucciones">
                <p>Lleva cada cubito a su lugar correspondiente.</p>
                <div class="puntos-info">
                    <span class="acierto">Acierto +10</span>
                    <span class="error">Error -5</span>
                </div>
            </div>
            <button class="btn-main" onclick="startGame()">¡JUGAR!</button>
        </div>
    </div>

    <script>
        const board = document.getElementById('board');
        const pool = document.getElementById('numbers-pool');
        const scoreDisplay = document.getElementById('score');
        
        const soundSuccess = new Audio('https://assets.mixkit.co/active_storage/sfx/2000/2000-preview.mp3');
        const soundError = new Audio('https://assets.mixkit.co/active_storage/sfx/2003/2003-preview.mp3');

        let numbers = [];
        let score = 0;

        function startGame() {
            document.getElementById('start-modal').classList.remove('active');
            score = 0;
            scoreDisplay.textContent = score;
            initBoard();
            renderPool();
        }

        function initBoard() {
            board.innerHTML = '';
            numbers = Array.from({ length: 100 }, (_, i) => i + 1);
            numbers.sort(() => Math.random() - 0.5);
            
            for (let i = 1; i <= 100; i++) {
                const cell = document.createElement('div');
                cell.className = 'cell';
                cell.dataset.value = i;
                board.appendChild(cell);
            }
        }

        function renderPool() {
            pool.innerHTML = '';
            if (numbers.length === 0) {
                alert("¡Juego terminado! Puntuación final: " + score);
                return;
            }
            const num = numbers[0];
            const card = document.createElement('div');
            card.className = 'number-card';
            card.dataset.value = num;
            card.appendChild(formatNumber(num));
            card.addEventListener('pointerdown', startDrag);
            pool.appendChild(card);
        }

        function formatNumber(num) {
            const container = document.createElement('span');
            container.style.pointerEvents = 'none';
            if (num === 100) {
                container.innerHTML = `<span class="digit-decena">10</span><span class="digit-unidad">0</span>`;
            } else if (num < 10) {
                container.innerHTML = `<span class="digit-unidad">${num}</span>`;
            } else {
                const d = Math.floor(num / 10);
                const u = num % 10;
                container.innerHTML = `<span class="digit-decena">${d}</span><span class="digit-unidad">${u}</span>`;
            }
            return container;
        }

        function startDrag(e) {
            e.preventDefault();
            const card = e.currentTarget;
            const val = card.dataset.value;
            const rect = card.getBoundingClientRect();
            const ghost = card.cloneNode(true);
            
            ghost.style.position = 'fixed';
            ghost.style.width = rect.width + 'px';
            ghost.style.height = rect.height + 'px';
            ghost.style.zIndex = 10000;
            ghost.style.pointerEvents = 'none';
            ghost.style.opacity = '0.9';
            ghost.style.transition = 'none'; 
            document.body.appendChild(ghost);
            card.style.visibility = 'hidden';

            const onMove = (ev) => {
                ghost.style.left = ev.clientX - rect.width/2 + 'px';
                ghost.style.top = ev.clientY - rect.height/2 + 'px';
            };

            const onUp = (ev) => {
                window.getSelection().removeAllRanges();
                document.removeEventListener('pointermove', onMove);
                document.removeEventListener('pointerup', onUp);
                
                const elementUnder = document.elementFromPoint(ev.clientX, ev.clientY);
                const targetCell = elementUnder ? elementUnder.closest('.cell') : null;
                
                if (targetCell && targetCell.dataset.value == val) {
                    soundSuccess.currentTime = 0;
                    soundSuccess.play();
                    const cube = document.createElement('div');
                    cube.className = 'correct-cube';
                    cube.appendChild(formatNumber(val));
                    targetCell.appendChild(cube);
                    score += 10;
                    scoreDisplay.textContent = score;
                    numbers.shift();
                    renderPool();
                } else {
                    if (targetCell) {
                        soundError.currentTime = 0;
                        soundError.play();
                        score = Math.max(0, score - 5);
                        scoreDisplay.textContent = score;
                    }
                    card.style.visibility = 'visible';
                }
                ghost.remove();
            };

            document.addEventListener('pointermove', onMove);
            document.addEventListener('pointerup', onUp);
            ghost.style.left = e.clientX - rect.width/2 + 'px';
            ghost.style.top = e.clientY - rect.height/2 + 'px';
        }
    </script>
</body>
</html>
