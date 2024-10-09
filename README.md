<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>😎 PANCHO mata al PANCHO 😎</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            height: 100vh;
            background-color: #282c34;
            color: white;
        }
        #game {
            width: 800px;
            height: 600px;
            background-color: #484;
            position: relative;
            overflow: hidden;
        }
        #player {
            width: 50px; /* Ajusta el tamaño según la imagen */
            height: 50px; /* Ajusta el tamaño según la imagen */
            position: absolute;
            bottom: 20px;
            left: calc(50% - 25px);
            background-size: cover;
        }
        .enemy {
            width: 50px; /* Ajusta el tamaño según la imagen */
            height: 50px; /* Ajusta el tamaño según la imagen */
            position: absolute;
            background-size: cover;
        }
        .bullet {
            width: 5px;
            height: 15px;
            background-color: #fff;
            position: absolute;
        }
        #score {
            font-size: 24px;
            margin-bottom: 10px;
        }
        #gameOver {
            position: absolute;
            font-size: 48px;
            color: red;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: none;
        }
    </style>
</head>
<body>

    <h1>😎 PANCHO mata al PANCHO 😎</h1>
  
    <div id="game">
        <div id="player"></div>
        <div id="gameOver">¡Se Acabó Causa!</div>
    </div>

    <script>
        const game = document.getElementById('game');
        const player = document.getElementById('player');
        const gameOverText = document.getElementById('gameOver');
        const scoreDisplay = document.createElement('div');
        scoreDisplay.id = 'score';
        scoreDisplay.innerText = 'Puntuación: 10';
        document.body.insertBefore(scoreDisplay, game);

        let score = 10; // Puntuación inicial
        let gameRunning = false;
        let enemies = [];
        let bullets = [];
        let enemySpeed = 1;
        let enemySpawnRate = 2000; // Milisegundos
        let bulletSpeed = 5;

        // URL de las imágenes (personaliza estas URLs)
        const playerImageURL = 'data:image/jpeg;base64,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'; // Cambia a tu URL de imagen del jugador
        const enemyImageURL = 'https://upload.wikimedia.org/wikipedia/commons/thumb/2/23/Pancho_Villa_bandolier_%28cropped%29.jpg/640px-Pancho_Villa_bandolier_%28cropped%29.jpg'; // Cambia a tu URL de imagen del enemigo

        // Función para iniciar el juego
        function startGame() {
            gameRunning = true;
            player.style.backgroundImage = `url(${playerImageURL})`; // Imagen del jugador
            player.style.left = '375px'; // Centra el jugador
            player.style.bottom = '20px'; // Posición inicial del jugador
            gameOverText.style.display = 'none';
            enemies = [];
            bullets = [];
            score = 10; // Reinicia la puntuación
            scoreDisplay.textContent = `Puntuación: ${score}`;
            spawnEnemies();
            update();
        }

        // Generar enemigos
        function spawnEnemies() {
            const enemy = document.createElement('div');
            enemy.classList.add('enemy');
            enemy.style.backgroundImage = `url(${enemyImageURL}`; // Imagen del enemigo
            enemy.style.left = Math.random() * (game.clientWidth - 50) + 'px'; // Posición aleatoria
            enemy.style.top = '0px'; // Comienza desde la parte superior
            enemy.health = 3; // Salud del enemigo
            game.appendChild(enemy);
            enemies.push(enemy);
            setTimeout(spawnEnemies, enemySpawnRate); // Genera enemigos en intervalos
        }

        // Mover enemigos
        function moveEnemies() {
            enemies.forEach((enemy, index) => {
                const enemyTop = parseInt(enemy.style.top);
                enemy.style.top = enemyTop + enemySpeed + 'px';

                // Verifica colisión con el jugador
                if (isColliding(player, enemy)) {
                    gameRunning = false;
                    gameOverText.style.display = 'block';
                }

                // Elimina enemigos que salieron de la pantalla
                if (enemyTop > game.clientHeight) {
                    enemy.remove();
                    enemies.splice(index, 1);
                    score -= 1; // Resta un punto si se escapa
                    scoreDisplay.textContent = `Puntuación: ${score}`;

                    // Verifica si la puntuación llega a 0
                    if (score <= 0) {
                        gameRunning = false;
                        gameOverText.style.display = 'block';
                    }
                }
            });
        }

        // Disparar balas
        function shootBullet() {
            const bullet = document.createElement('div');
            bullet.classList.add('bullet');
            bullet.style.left = parseInt(player.style.left) + 22 + 'px'; // Centra la bala
            bullet.style.bottom = '70px'; // Posición inicial de la bala
            game.appendChild(bullet);
            bullets.push(bullet);
            moveBullet(bullet);
        }

        // Mover balas
        function moveBullet(bullet) {
            const bulletInterval = setInterval(() => {
                const bulletBottom = parseInt(bullet.style.bottom);
                bullet.style.bottom = bulletBottom + bulletSpeed + 'px';

                // Verifica si la bala sale de la pantalla
                if (bulletBottom > game.clientHeight) {
                    bullet.remove();
                    bullets.splice(bullets.indexOf(bullet), 1);
                    clearInterval(bulletInterval);
                }

                // Verifica colisión con enemigos
                enemies.forEach((enemy, enemyIndex) => {
                    if (isColliding(bullet, enemy)) {
                        enemy.health -= 1; // Resta salud al enemigo
                        bullet.remove();
                        bullets.splice(bullets.indexOf(bullet), 1);
                        clearInterval(bulletInterval);

                        // Si la salud del enemigo llega a 0, lo elimina
                        if (enemy.health <= 0) {
                            enemy.remove();
                            enemies.splice(enemyIndex, 1);
                        }
                    }
                });
            }, 30); // Ajusta la velocidad de la bala
        }

        // Verifica colisión entre dos elementos
        function isColliding(player, enemy) {
            const playerRect = player.getBoundingClientRect();
            const enemyRect = enemy.getBoundingClientRect();

            return !(playerRect.right < enemyRect.left ||
                     playerRect.left > enemyRect.right ||
                     playerRect.bottom < enemyRect.top ||
                     playerRect.top > enemyRect.bottom);
        }

        // Actualizar el juego
        function update() {
            if (!gameRunning) return;

            moveEnemies();
            requestAnimationFrame(update);
        }

        // Control del jugador
        window.addEventListener('keydown', (e) => {
            const playerLeft = parseInt(player.style.left);

            if (e.key === 'ArrowLeft' && playerLeft > 0) {
                player.style.left = playerLeft - 10 + 'px'; // Mueve a la izquierda
            }
            if (e.key === 'ArrowRight' && playerLeft < game.clientWidth - 50) {
                player.style.left = playerLeft + 10 + 'px'; // Mueve a la derecha
            }
            if (e.key === ' ') {
                shootBullet(); // Dispara
            }
        });

        // Iniciar el juego al cargar la página
        startGame();

    </script>

</body>
</html>
