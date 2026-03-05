index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>La Bandiera Rossa</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #ffffff; /* Sfondo Bianco */
        }
        canvas {
            display: block;
        }
    </style>
</head>
<body>

<canvas id="animationCanvas"></canvas>

<script>
    const canvas = document.getElementById('animationCanvas');
    const ctx = canvas.getContext('2d');

    const words = [
        "M.A.G.A.", "ENI", "ISRAEL", "COLONIALISM", "CAPITALISM", 
        "PATRIARCHAL", "HOMOPHOBIA", "TRANSPHOBIA", "ACEPHOBIA", 
        "LESBOPHONIA", "BIPHOBIA", "PANPHONIA", "LGBTQIAPK+PHOBIA", 
        "DISCRIMINATION", "OPPRESSION", "REPRESSION", "MONEY", 
        "OIL", "FOSSIL FUEL", "ILVA", "RACISM", "NAZISM", "ZIONISM"
    ];

    function resize() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        generateHill();
    }

    let tags = [];
    function generateHill() {
        tags = [];
        const centerX = canvas.width / 2;
        const baseY = canvas.height * 0.9;
        
        words.forEach((word, i) => {
            // Distribuzione a collinetta (più larghe alla base, più strette in alto)
            const row = Math.floor(i / 4);
            const col = i % 4;
            const spread = (6 - row) * 60; 
            
            const x = centerX + (Math.random() - 0.5) * spread;
            const y = baseY - (row * 25) + (Math.random() * 10);
            const angle = (Math.random() - 0.5) * 0.3;

            // Dividiamo ogni targhetta in due pezzi rettangolari
            tags.push({ word, x: x - 25, y, angle, side: 'left' });
            tags.push({ word, x: x + 25, y: y + 2, angle: angle + 0.1, side: 'right' });
        });
    }

    let time = 0;

    function drawTag(tag) {
        ctx.save();
        ctx.translate(tag.x, tag.y);
        ctx.rotate(tag.angle);

        // Oro rettangolare
        const grd = ctx.createLinearGradient(0, 0, 0, 20);
        grd.addColorStop(0, "#D4AF37");
        grd.addColorStop(0.5, "#FFD700");
        grd.addColorStop(1, "#B8860B");
        ctx.fillStyle = grd;
        ctx.strokeStyle = "#996515";
        ctx.lineWidth = 1;

        const w = 60;
        const h = 25;

        ctx.beginPath();
        if(tag.side === 'left') {
            ctx.rect(-w/2, -h/2, w/2, h);
        } else {
            ctx.rect(0, -h/2, w/2, h);
        }
        ctx.fill();
        ctx.stroke();

        // Testo nero
        ctx.fillStyle = "black";
        ctx.font = "bold 9px Arial";
        ctx.textAlign = "center";
        let displayWord = tag.side === 'left' ? tag.word.substring(0, Math.ceil(tag.word.length/2)) : tag.word.substring(Math.ceil(tag.word.length/2));
        ctx.fillText(displayWord, tag.side === 'left' ? -w/4 : w/4, 4);

        ctx.restore();
    }

    function animate() {
        time += 0.05;
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // 1. Disegna la collinetta
        tags.forEach(drawTag);

        // 2. Stickman in CIMA alla collinetta
        const stickX = canvas.width / 2;
        const stickY = canvas.height * 0.9 - 140; 
        
        ctx.strokeStyle = "black";
        ctx.lineWidth = 4;
        ctx.lineCap = "round";

        // Testa
        ctx.beginPath();
        ctx.arc(stickX, stickY, 12, 0, Math.PI*2);
        ctx.stroke();

        // Tronco
        ctx.beginPath();
        ctx.moveTo(stickX, stickY + 12);
        ctx.lineTo(stickX, stickY + 50);
        ctx.stroke();

        // Gambe
        ctx.beginPath();
        ctx.moveTo(stickX, stickY + 50);
        ctx.lineTo(stickX - 15, stickY + 80);
        ctx.moveTo(stickX, stickY + 50);
        ctx.lineTo(stickX + 15, stickY + 80);
        ctx.stroke();

        // BRACCIA CHE SVENTOLANO
        const armWave = Math.sin(time * 1.5) * 0.5;
        ctx.beginPath();
        // Braccio sinistro fermo o bilanciamento
        ctx.moveTo(stickX, stickY + 20);
        ctx.lineTo(stickX - 20, stickY + 40);
        // Braccio destro che muove l'asta
        ctx.moveTo(stickX, stickY + 20);
        const handX = stickX + 30 + armWave * 20;
        const handY = stickY + 10 + armWave * 10;
        ctx.lineTo(handX, handY);
        ctx.stroke();

        // ASTA
        const poleTopX = handX + (armWave * 15);
        const poleTopY = handY - 100;
        const poleBottomX = handX;
        const poleBottomY = handY + 40;
        
        ctx.strokeStyle = "#442200";
        ctx.beginPath();
        ctx.moveTo(poleBottomX, poleBottomY);
        ctx.lineTo(poleTopX, poleTopY);
        ctx.stroke();

        // BANDIERA ROSSA (Effetto Vento)
        ctx.fillStyle = "red";
        ctx.beginPath();
        ctx.moveTo(poleTopX, poleTopY);
        for(let i=0; i<=80; i++) {
            const wave = Math.sin(time * 3 + i * 0.1) * (i * 0.2);
            ctx.lineTo(poleTopX + i, poleTopY + wave + (i * 0.1));
        }
        for(let i=80; i>=0; i--) {
            const wave = Math.sin(time * 3 + i * 0.1) * (i * 0.2);
            ctx.lineTo(poleTopX + i, poleTopY + 50 + wave + (i * 0.1));
        }
        ctx.closePath();
        ctx.fill();

        requestAnimationFrame(animate);
    }

    window.addEventListener('resize', resize);
    resize();
    animate();
</script>
</body>
</html>
