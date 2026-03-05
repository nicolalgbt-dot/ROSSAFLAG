index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>La Bandiera Rossa - Progetto GitHub</title>
    <style>
        /* Sfondo bianco pulito e reset */
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #ffffff; 
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

<canvas id="animationCanvas"></canvas>

<script>
    const canvas = document.getElementById('animationCanvas');
    const ctx = canvas.getContext('2d');

    // Parole fornite dall'utente
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
        generatePyramid();
    }

    // Struttura per le targhette
    let pyramidTags = [];

    function generatePyramid() {
        pyramidTags = [];
        const centerX = canvas.width / 2;
        const baseY = canvas.height * 0.95; // Base quasi in fondo allo schermo
        const tagW = 110;  // Larghezza targhetta
        const tagH = 35;   // Altezza targhetta
        const gap = 3;     // Spazio tra targhette

        // Definiamo una struttura a piramide (righe, targhette per riga)
        const pyramidStructure = [
            1,      // Cima
            2, 3, 4, 5, 6 // Base più larga
        ];

        let wordIndex = 0;

        for (let r = 0; r < pyramidStructure.length; r++) {
            const numTags = pyramidStructure[r];
            const rowWidth = numTags * tagW + (numTags - 1) * gap;
            const rowStartX = centerX - rowWidth / 2;
            const rowY = baseY - (r * (tagH + gap));

            for (let c = 0; c < numTags; c++) {
                if (wordIndex < words.length) {
                    const x = rowStartX + c * (tagW + gap);
                    pyramidTags.push({ 
                        word: words[wordIndex], 
                        x, 
                        y: rowY, 
                        w: tagW, 
                        h: tagH,
                        // Piccola variazione casuale per simulare pietre non perfette
                        tilt: (Math.random() - 0.5) * 0.05 
                    });
                    wordIndex++;
                }
            }
        }
    }

    let time = 0;

    // Disegna la singola targhetta/pietra
    function drawTag(tag) {
        ctx.save();
        ctx.translate(tag.x + tag.w / 2, tag.y + tag.h / 2);
        ctx.rotate(tag.tilt);

        // Colore Oro/Pietra
        ctx.fillStyle = "#e6c96e"; // Oro opaco, più simile al disegno
        ctx.strokeStyle = "#444";  // Bordo scuro
        ctx.lineWidth = 1;

        // Disegna il rettangolo
        ctx.beginPath();
        ctx.rect(-tag.w / 2, -tag.h / 2, tag.w, tag.h);
        ctx.fill();
        ctx.stroke();

        // Disegna la crepa a metà (stile inchiostro)
        ctx.strokeStyle = "#444";
        ctx.lineWidth = 1.5;
        ctx.beginPath();
        ctx.moveTo(0, -tag.h / 2); // Inizio crepa in alto a metà
        ctx.lineTo(-2, -5);
        ctx.lineTo(3, 5);
        ctx.lineTo(0, tag.h / 2);  // Fine crepa in basso a metà
        ctx.stroke();

        // Testo (stile inchiostro, nero)
        ctx.fillStyle = "#111";
        ctx.font = "bold 11px 'Courier New', monospace"; // Font monospace per stile macchina da scrivere/inchiostro
        ctx.textAlign = "center";
        ctx.textBaseline = "middle";
        
        // Ritaglia il testo se troppo lungo
        let displayWord = tag.word;
        if(displayWord.length > 12) displayWord = displayWord.substring(0, 10) + "..";
        
        ctx.fillText(displayWord.toUpperCase(), 0, 0, tag.w * 0.9);

        ctx.restore();
    }

    function animate() {
        time += 0.03;
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // 1. Disegna la Piramide solida
        pyramidTags.forEach(drawTag);

        // 2. Stickman (Posizionato sulla CIMA)
        const stickX = canvas.width / 2;
        // Calcola l'altezza della piramide per mettere lo stickman sopra
        const pyramidHeight = (pyramidStructure.length + 1) * 38; 
        const stickY = canvas.height * 0.95 - pyramidHeight;
        
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

        // Gambe (una piegata per dare movimento)
        ctx.beginPath();
        ctx.moveTo(stickX, stickY + 50);
        ctx.lineTo(stickX - 15, stickY + 80); // Gamba sinistra dritta
        ctx.moveTo(stickX, stickY + 50);
        ctx.lineTo(stickX + 15, stickY + 70); // Gamba destra piegata
        ctx.lineTo(stickX + 5, stickY + 80);
        ctx.stroke();

        // BRACCIA E MOVIMENTO ASTA
        const armWave = Math.sin(time) * 0.3; // Oscillazione braccio
        ctx.beginPath();
        // Braccio sinistro
        ctx.moveTo(stickX, stickY + 20);
        ctx.lineTo(stickX - 25 + armWave * 5, stickY + 40);
        // Braccio destro che muove l'asta
        ctx.moveTo(stickX, stickY + 20);
        const handX = stickX + 30 + armWave * 15;
        const handY = stickY + 15 + armWave * 10;
        ctx.lineTo(handX, handY);
        ctx.stroke();

        // ASTA (Legno scuro)
        const poleTopX = handX + (armWave * 10);
        const poleTopY = handY - 110;
        const poleBottomX = handX - 10;
        const poleBottomY = handY + 30;
        
        ctx.strokeStyle = "#5a3a22";
        ctx.lineWidth = 5;
        ctx.beginPath();
        ctx.moveTo(poleBottomX, poleBottomY);
        ctx.lineTo(poleTopX, poleTopY);
        ctx.stroke();

        // BANDIERA ROSSA (Effetto Vento realistico)
        ctx.fillStyle = "#d32f2f"; // Rosso bandiera
        ctx.strokeStyle = "#800"; // Bordo scuro
        ctx.lineWidth = 1;
        ctx.beginPath();
        ctx.moveTo(poleTopX, poleTopY);
        
        const flagWidth = 100;
        const flagHeight = 60;
        
        // Curva superiore sventolante
        for(let i=0; i<=flagWidth; i++) {
            const wave = Math.sin(time * 2 + i * 0.08) * (i * 0.25);
            ctx.lineTo(poleTopX + i, poleTopY + wave);
        }
        // Lato destro
        ctx.lineTo(poleTopX + flagWidth, poleTopY + flagHeight + Math.sin(time * 2 + flagWidth * 0.08) * (flagWidth * 0.25));
        
        // Curva inferiore sventolante
        for(let i=flagWidth; i>=0; i--) {
            const wave = Math.sin(time * 2 + i * 0.08) * (i * 0.25);
            ctx.lineTo(poleTopX + i, poleTopY + flagHeight + wave);
        }
        ctx.closePath();
        ctx.fill();
        ctx.stroke(); // Aggiunge bordo per stile inchiostro

        // Linee d'azione dello sventolio (come nel disegno)
        ctx.strokeStyle = "rgba(0,0,0,0.2)";
        ctx.lineWidth = 1;
        ctx.beginPath();
        for(let i=10; i<flagWidth; i+=20) {
            const wave = Math.sin(time * 2 + i * 0.08) * (i * 0.25);
            ctx.moveTo(poleTopX + i, poleTopY + wave + 5);
            ctx.lineTo(poleTopX + i, poleTopY + flagHeight + wave - 5);
        }
        ctx.stroke();

        requestAnimationFrame(animate);
    }

    // Struttura piramidale definita per il calcolo altezza
    const pyramidStructure = [1, 2, 3, 4, 5, 6];

    window.addEventListener('resize', resize);
    resize();
    animate();
</script>
</body>
</html>
