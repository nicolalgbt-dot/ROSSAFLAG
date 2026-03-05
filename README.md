index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>La Bandiera Rossa</title>
    <style>
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; background: #fff; }
        canvas { display: block; cursor: crosshair; }
    </style>
</head>
<body>
<canvas id="canvas"></canvas>

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

    function initPyramid() {
        pyramid = [];
        const rows = 6;
        const tagW = 120;
        const tagH = 35;
        let wordIdx = 0;

        for (let r = 0; r < rows; r++) {
            const tagsInRow = rows - r;
            for (let c = 0; c < tagsInRow; c++) {
                if (wordIdx < words.length) {
                    pyramid.push({
                        word: words[wordIdx++],
                        row: r,
                        col: c,
                        xOff: (Math.random() - 0.5) * 5,
                        yOff: (Math.random() - 0.5) * 5,
                        rot: (Math.random() - 0.5) * 0.05
                    });
                }
            }
        }
    }

    function drawHandDrawnLine(x1, y1, x2, y2) {
        ctx.beginPath();
        ctx.moveTo(x1, y1);
        const midX = (x1 + x2) / 2 + (Math.random() - 0.5) * 2;
        const midY = (y1 + y2) / 2 + (Math.random() - 0.5) * 2;
        ctx.quadraticCurveTo(midX, midY, x2, y2);
        ctx.stroke();
    }

    function drawTag(tag, centerX, baseY) {
        const tagW = 110;
        const tagH = 32;
        const x = centerX + (tag.col - (6 - tag.row) / 2) * (tagW + 4) + tag.xOff + 60;
        const y = baseY - tag.row * (tagH + 2) + tag.yOff;

        ctx.save();
        ctx.translate(x, y);
        ctx.rotate(tag.rot);

        // Colore oro "sporco"
        ctx.fillStyle = "#e0c366";
        ctx.lineWidth = 2;
        ctx.strokeStyle = "#000";
        
        // Rettangolo targhetta
        ctx.beginPath();
        ctx.rect(-tagW/2, -tagH/2, tagW, tagH);
        ctx.fill();
        ctx.stroke();

        // Crepa centrale (stile inchiostro)
        ctx.beginPath();
        ctx.moveTo(0, -tagH/2);
        ctx.lineTo(-3, 0);
        ctx.lineTo(2, tagH/2);
        ctx.stroke();

        // Testo spezzato
        ctx.fillStyle = "#000";
        ctx.font = "bold 10px Courier New";
        ctx.textAlign = "center";
        const mid = Math.ceil(tag.word.length / 2);
        ctx.fillText(tag.word.substring(0, mid), -tagW/4, 4);
        ctx.fillText(tag.word.substring(mid), tagW/4, 4);

        ctx.restore();
    }

    function render() {
        time += 0.04;
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        const centerX = canvas.width / 2;
        const baseY = canvas.height * 0.9;

        // 1. Disegna la collinetta
        pyramid.forEach(t => drawTag(t, centerX, baseY));

        // 2. Stickman in cima
        const sX = centerX;
        const sY = baseY - 210;
        ctx.strokeStyle = "#000";
        ctx.lineWidth = 3;

        // Testa
        ctx.beginPath();
        ctx.arc(sX, sY, 12, 0, Math.PI*2);
        ctx.stroke();

        // Corpo
        drawHandDrawnLine(sX, sY + 12, sX, sY + 60);

        // Gambe
        drawHandDrawnLine(sX, sY + 60, sX - 20, sY + 100);
        drawHandDrawnLine(sX, sY + 60, sX + 20, sY + 100);

        // Braccia animate (Sventolio)
        const wave = Math.sin(time * 2);
        const handX = sX + 40 + wave * 10;
        const handY = sY + 30 + wave * 5;
        
        drawHandDrawnLine(sX, sY + 25, handX, handY); // Braccio destro
        drawHandDrawnLine(sX, sY + 25, sX - 30, sY + 45); // Braccio sinistro

        // 3. Asta e Bandiera
        const poleX = handX + wave * 5;
        const poleY = handY;
        
        ctx.lineWidth = 5;
        ctx.strokeStyle = "#4b2e1a"; // Legno
        drawHandDrawnLine(poleX, poleY + 40, poleX - 10, poleY - 120);

        // Bandiera Rossa
        const flagX = poleX - 10;
        const flagY = poleY - 120;
        
        ctx.fillStyle = "#d32f2f";
        ctx.beginPath();
        ctx.moveTo(flagX, flagY);
        
        // Sventolio dinamico
        for(let i=0; i<=110; i++) {
            const wind = Math.sin(time * 4 + i * 0.06) * (i * 0.2);
            ctx.lineTo(flagX + i, flagY + wind);
        }
        for(let i=110; i>=0; i--) {
            const wind = Math.sin(time * 4 + i * 0.06) * (i * 0.2);
            ctx.lineTo(flagX + i, flagY + 60 + wind);
        }
        ctx.closePath();
        ctx.fill();
        ctx.stroke();

        requestAnimationFrame(render);
    }

    window.addEventListener('resize', () => {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        initPyramid();
    });

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    initPyramid();
    render();
</script>
</body>
</html>
