index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROSSAFLAG</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #ffffff;
            display: flex;
            flex-direction: column;
        }

        /* Titolo Rosso e Giallo con effetto riflesso */
        header {
            width: 100%;
            padding: 25px 0;
            text-align: center;
            background: #fff;
        }

        h1 {
            margin: 0;
            font-family: 'Arial Black', sans-serif;
            font-size: 3rem;
            text-transform: uppercase;
            letter-spacing: 4px;
            /* Effetto gradiente riflesso */
            background: linear-gradient(to bottom, 
                #ff0000 0%, 
                #ffcc00 45%, 
                #ffffff 50%, 
                #ffcc00 55%, 
                #ff0000 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            filter: drop-shadow(2px 2px 0px rgba(0,0,0,0.1));
        }

        main {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        canvas {
            display: block;
        }
    </style>
</head>
<body>

    <header>
        <h1>ROSSAFLAG</h1>
    </header>

    <main>
        <canvas id="canvas"></canvas>
    </main>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');

    const words = [
        "M.A.G.A.", "ENI", "ISRAEL", "COLONIALISM", "CAPITALISM", 
        "PATRIARCHAL", "HOMOPHOBIA", "TRANSPHOBIA", "ACEPHOBIA", 
        "LESBOPHONIA", "BIPHOBIA", "PANPHONIA", "LGBTQIAPK+PHOBIA", 
        "DISCRIMINATION", "OPPRESSION", "REPRESSION", "MONEY", 
        "OIL", "FOSSIL FUEL", "ILVA", "RACISM", "NAZISM", "ZIONISM"
    ];

    let time = 0;
    let pyramid = [];

    function init() {
        const container = canvas.parentElement;
        canvas.width = container.clientWidth;
        canvas.height = container.clientHeight;
        
        pyramid = [];
        let wordIdx = 0;
        const rows = 6;

        for (let r = 0; r < rows; r++) {
            const tagsInRow = rows - r;
            for (let c = 0; c < tagsInRow; c++) {
                if (wordIdx < words.length) {
                    pyramid.push({
                        word: words[wordIdx++],
                        row: r,
                        col: c,
                        totalInRow: tagsInRow,
                        xOff: (Math.random() - 0.5) * 10,
                        yOff: (Math.random() - 0.5) * 5,
                        rot: (Math.random() - 0.5) * 0.12
                    });
                }
            }
        }
    }

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        time += 0.2; 

        const centerX = canvas.width / 2;
        const baseY = canvas.height * 0.9;
        const tagW = 120;
        const tagH = 40;

        // Disegna Collinetta
        pyramid.forEach(tag => {
            const rowWidth = tag.totalInRow * (tagW + 4);
            const x = centerX - (rowWidth / 2) + (tag.col * (tagW + 4)) + tagW/2 + tag.xOff;
            const y = baseY - (tag.row * (tagH + 2)) + tag.yOff;

            ctx.save();
            ctx.translate(x, y);
            ctx.rotate(tag.rot);

            ctx.fillStyle = "#f2d06b";
            ctx.strokeStyle = "#000";
            ctx.lineWidth = 2.5;
            ctx.beginPath();
            ctx.rect(-tagW/2, -tagH/2, tagW, tagH);
            ctx.fill();
            ctx.stroke();

            // Crepa
            ctx.beginPath();
            ctx.moveTo(0, -tagH/2); ctx.lineTo(-4, 0); ctx.lineTo(4, tagH/2);
            ctx.stroke();

            // Testo
            ctx.fillStyle = "#000";
            ctx.font = "bold 11px Arial";
            ctx.textAlign = "center";
            const mid = Math.ceil(tag.word.length / 2);
            ctx.fillText(tag.word.substring(0, mid), -tagW/4 - 2, 5);
            ctx.fillText(tag.word.substring(mid), tagW/4 + 2, 5);
            ctx.restore();
        });

        // Stickman e Asta (3 frame jump)
        const frame = Math.floor(time % 3);
        const offsets = [0, -15, -8]; 
        const currentY = offsets[frame];

        const sX = centerX;
        const sY = baseY - (6 * 42) - 30;

        ctx.strokeStyle = "#000";
        ctx.lineWidth = 5;
        ctx.lineCap = "round";

        // Corpo Stickman
        ctx.beginPath();
        ctx.arc(sX, sY, 15, 0, Math.PI*2); // Testa
        ctx.moveTo(sX, sY + 15); ctx.lineTo(sX, sY + 65); // Busto
        ctx.moveTo(sX, sY + 65); ctx.lineTo(sX - 25, sY + 105); // Gamba L
        ctx.moveTo(sX, sY + 65); ctx.lineTo(sX + 25, sY + 105); // Gamba R
        ctx.stroke();

        // Asta e Bandiera
        ctx.save();
        ctx.translate(0, currentY);
        ctx.lineWidth = 7; ctx.strokeStyle = "#402613";
        ctx.beginPath();
        ctx.moveTo(sX + 30, sY + 85); ctx.lineTo(sX + 40, sY - 130);
        ctx.stroke();

        // Bandiera
        ctx.fillStyle = "#ff0000"; ctx.strokeStyle = "#000"; ctx.lineWidth = 2;
        ctx.beginPath();
        const fX = sX + 40; const fY = sY - 130;
        ctx.moveTo(fX, fY);
        for(let i=0; i<=120; i++) {
            const w = Math.sin(time + i * 0.1) * (i * 0.15);
            ctx.lineTo(fX + i, fY + w);
        }
        ctx.lineTo(fX + 120, fY + 70);
        for(let i=120; i>=0; i--) {
            const w = Math.sin(time + i * 0.1) * (i * 0.15);
            ctx.lineTo(fX + i, fY + 70 + w);
        }
        ctx.closePath(); ctx.fill(); ctx.stroke();
        ctx.restore();

        // Braccio
        ctx.strokeStyle = "#000"; ctx.lineWidth = 5;
        ctx.beginPath();
        ctx.moveTo(sX, sY + 30); ctx.lineTo(sX + 32, sY + 45 + currentY);
        ctx.stroke();

        requestAnimationFrame(draw);
    }

    window.addEventListener('resize', init);
    init();
    draw();
</script>
</body>
</html>
