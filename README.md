<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Game of Life – iPad</title>
    <link rel="manifest" href="manifest.webmanifest" />
    <style>
        html, body {
            margin: 0;
            height: 100%;
            overflow: hidden;
            background: black;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        canvas {
            background: black;
            image-rendering: pixelated;
        }
        .controls {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 1rem;
            z-index: 10;
        }
        button {
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid white;
            color: white;
            padding: 10px 20px;
            font-size: 1rem;
            border-radius: 10px;
            cursor: pointer;
        }
        button:hover {
            background: rgba(255, 255, 255, 0.25);
        }
    </style>
</head>
<body>
    <canvas id="life"></canvas>
    <div class="controls">
        <button id="run">▶ Run</button>
        <button id="paint">🎨 Live Paint</button>
    </div>

    <script>
        const canvas = document.getElementById("life");
        const ctx = canvas.getContext("2d");
        const size = 100;
        const cellSize = 7;
        canvas.width = size * cellSize;
        canvas.height = size * cellSize;

        let grid = Array(size * size).fill(0);
        let running = false;
        let painting = false;

        function draw() {
            ctx.fillStyle = "black";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "lime";
            grid.forEach((v, i) => {
                if (v) {
                    const x = (i % size) * cellSize;
                    const y = Math.floor(i / size) * cellSize;
                    ctx.fillRect(x, y, cellSize - 1, cellSize - 1);
                }
            });
        }

        function step() {
            const next = grid.slice();
            for (let i = 0; i < grid.length; i++) {
                const x = i % size;
                const y = Math.floor(i / size);
                let n = 0;
                for (let dy = -1; dy <= 1; dy++)
                    for (let dx = -1; dx <= 1; dx++) {
                        if (dx === 0 && dy === 0) continue;
                        const nx = x + dx;
                        const ny = y + dy;
                        if (nx >= 0 && nx < size && ny >= 0 && ny < size)
                            n += grid[ny * size + nx];
                    }
                next[i] = n === 3 || (grid[i] && n === 2) ? 1 : 0;
            }
            grid = next;
            draw();
        }

        let interval;
        document.getElementById("run").onclick = () => {
            running = !running;
            if (running) {
                interval = setInterval(step, 80);
            } else {
                clearInterval(interval);
            }
        };

        document.getElementById("paint").onclick = () => {
            painting = !painting;
            document.getElementById("paint").style.borderColor = painting ? "lime" : "white";
        };

        canvas.addEventListener("click", e => {
            const rect = canvas.getBoundingClientRect();
            const x = Math.floor((e.clientX - rect.left) / cellSize);
            const y = Math.floor((e.clientY - rect.top) / cellSize);
            const i = y * size + x;
            grid[i] = painting ? 1 : grid[i] ? 0 : 1;
            draw();
        });

        draw();
    </script>
</body>
</html>
