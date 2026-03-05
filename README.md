index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Stickman con Bandiera Rossa e Targhette</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #1a1a1a; /* Sfondo scuro per contrasto */
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

    // Dimensioni canvas a schermo intero
    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    // Configurazione e Parole per le targhette
    const words = [
        "M.A.G.A.", "ENI", "ISRAEL", "COLONIALISM", "CAPITALISM", 
        "PATRIARCHAL", "HOMOPHOBIA", "TRANSPHOBIA", "ACEPHOBIA", 
        "LESBOPHONIA", "BIPHOBIA", "PANPHONIA", "LGBTQIAPK+PHOBIA", 
        "DISCRIMINATION", "OPPRESSION", "REPRESSION", "MONEY", 
        "OIL", "FOSSIL FUEL", "ILVA", "RACISM", "NAZISM", "ZIONISM"
    ];

    // Parametri Fisici e di Animazione
    let time = 0;
    const windForce = 0.15; // Intensità del vento
    const waveFrequency = 0.1; // Frequenza delle onde sulla bandiera

    // Classe per le Targhette Spezzate
    class GoldTag {
        constructor(word, x, y, width, height, angle, isLeftPart) {
            this.word = word;
            this.x = x;
            this.y = y;
            this.width = width;
            this.height = height;
            this.angle = angle;
            this.isLeftPart = isLeftPart; // Se true è la parte sinistra, false la destra
        }

        draw(ctx) {
            ctx.save();
            ctx.translate(this.x, this.y);
            ctx.rotate(this.angle);

            // Gradiente Oro
            let gradient = ctx.createLinearGradient(0, 0, 0, this.height);
            gradient.addColorStop(0, '#BF953F'); // Oro scuro
            gradient.addColorStop(0.5, '#FCF6BA'); // Oro chiaro/lucido
            gradient.addColorStop(1, '#AA771C'); // Oro scuro

            ctx.fillStyle = gradient;
            ctx.strokeStyle = '#d4af37';
            ctx.lineWidth = 1;

            // Disegna la forma della targhetta spezzata
            ctx.beginPath();
            if (this.isLeftPart) {
                // Parte sinistra spezzata a destra
                ctx.moveTo(0, 0);
                ctx.lineTo(this.width * 0.8, 0);
                ctx.lineTo(this.width, this.height * 0.5); // Punta di rottura
                ctx.lineTo(this.width * 0.8, this.height);
                ctx.lineTo(0, this.height);
            } else {
                // Parte destra spezzata a sinistra
                ctx.moveTo(this.width, 0);
                ctx.lineTo(this.width * 0.2, 0);
                ctx.lineTo(0, this.height * 0.5); // Punta di rottura
                ctx.lineTo(this.width * 0.2, this.height);
                ctx.lineTo(this.width, this.height);
            }
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Testo sulla targhetta
            ctx.fillStyle = '#333'; // Testo scuro per leggibilità sull'oro
            ctx.font = 'bold 12px Arial';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            
            // Ritaglia il testo se troppo lungo o se spezzato
            let textToDisplay = this.word;
            if (this.isLeftPart && this.word.length > 8) textToDisplay = this.word.substring(0, this.word.length/2) + "-";
            if (!this.isLeftPart && this.word.length > 8) textToDisplay = "-" + this.word.substring(this.word.length/2);

            ctx.fillText(textToDisplay, this.width / 2, this.height / 2, this.width * 0.8);

            ctx.restore();
        }
    }

    // Genera la collinetta di targhette
    const tags = [];
    function generatePile() {
        tags.length = 0;
        const pileCenterX = canvas.width / 2;
        const pileBaseY = canvas.height * 0.85;
        const tagWidth = 100;
        const tagHeight = 30;

        for (let i = 0; i < words.length; i++) {
            // Crea due parti per ogni parola
            for (let j = 0; j < 2; j++) {
                const isLeftPart = j === 0;
                
                // Distribuzione casuale per formare una collinetta
                const offsetX = (Math.random() - 0.5) * 300; 
                const offsetY = -Math.random() * 80 - (i * 2); // Accumulo verticale casuale
                
                const x = pileCenterX + offsetX;
                const y = pileBaseY + offsetY;
                const angle = (Math.random() - 0.5) * 0.5; // Leggera rotazione casuale

                tags.push(new GoldTag(words[i], x, y, tagWidth, tagHeight, angle, isLeftPart));
            }
        }
    }

    // Disegna lo Stickman
    function drawStickman(x, y, scale) {
        ctx.save();
        ctx.translate(x, y);
        ctx.scale(scale, scale);
        ctx.strokeStyle = '#fff';
        ctx.lineWidth = 3;
        ctx.lineCap = 'round';

        // Testa
        ctx.beginPath();
        ctx.arc(0, -50, 10, 0, Math.PI * 2);
        ctx.stroke();

        // Corpo
        ctx.beginPath();
        ctx.moveTo(0, -40);
        ctx.lineTo(0, 0);
        ctx.stroke();

        // Gambe
        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.lineTo(-15, 25);
        ctx.moveTo(0, 0);
        ctx.lineTo(15, 25);
        ctx.stroke();

        // Braccio che tiene l'asta (animato leggermente)
        const armAngle = -Math.PI / 4 + Math.sin(time * 0.5) * 0.05;
        ctx.beginPath();
        ctx.moveTo(0, -35);
        ctx.lineTo(
            Math.cos(armAngle) * 30,
            -35 + Math.sin(armAngle) * 30
        );
        ctx.stroke();

        ctx.restore();
    }

    // Disegna l'Asta e la Bandiera Rossa sventolante
    function drawFlag(x, y, scale) {
        ctx.save();
        ctx.translate(x, y);
        ctx.scale(scale, scale);

        const poleHeight = 120;
        const flagWidth = 90;
        const flagHeight = 60;

        // 1. Disegna l'Asta
        ctx.strokeStyle = '#8b4513'; // Marrone legno
        ctx.lineWidth = 4;
        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.lineTo(0, -poleHeight);
        ctx.stroke();

        // 2. Disegna la Bandiera Rossa sventolante
        const flagStartX = 0;
        const flagStartY = -poleHeight;

        ctx.fillStyle = '#ed1c24'; // Rosso vivido
        ctx.beginPath();
        ctx.moveTo(flagStartX, flagStartY);

        // Curva superiore sventolante (simulazione vento con onda sinusoidale)
        for (let i = 0; i <= flagWidth; i++) {
            const wave = Math.sin(time + i * waveFrequency) * (i * windForce);
            ctx.lineTo(flagStartX + i, flagStartY + wave);
        }

        // Curva inferiore sventolante
        for (let i = flagWidth; i >= 0; i--) {
            const wave = Math.sin(time + i * waveFrequency) * (i * windForce);
            ctx.lineTo(flagStartX + i, flagStartY + flagHeight + wave);
        }

        ctx.closePath();
        ctx.fill();

        // Opzionale: Aggiunge linee di piega scure per dare profondità allo sventolio
        ctx.strokeStyle = 'rgba(0,0,0,0.1)';
        ctx.lineWidth = 1;
        ctx.beginPath();
        for (let i = 10; i < flagWidth; i += 15) {
             const wave = Math.sin(time + i * waveFrequency) * (i * windForce);
             ctx.moveTo(flagStartX + i, flagStartY + wave);
             ctx.lineTo(flagStartX + i, flagStartY + flagHeight + wave);
        }
        ctx.stroke();

        ctx.restore();
    }

    // Loop principale di animazione
    function animate() {
        requestAnimationFrame(animate);
        time += 0.05; // Incrementa il tempo per le onde

        // Pulisce il canvas
        ctx.fillStyle = '#1a1a1a';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Rigenera la pila se le dimensioni del canvas cambiano drasticamente
        if (tags.length === 0) generatePile();

        // 1. Disegna la collinetta di targhette d'oro
        tags.forEach(tag => tag.draw(ctx));

        // 2. Posiziona e disegna lo Stickman e la Bandiera sopra la collinetta
        const charX = canvas.width / 2;
        const charY = canvas.height * 0.85 - 25; // Appena sopra la base delle targhette
        const globalScale = 1.0;

        drawStickman(charX, charY, globalScale);
        
        // L'asta inizia dove finisce il braccio dello stickman
        const armAngle = -Math.PI / 4 + Math.sin(time * 0.5) * 0.05;
        const handX = charX + Math.cos(armAngle) * 30 * globalScale;
        const handY = charY + (-35 + Math.sin(armAngle) * 30) * globalScale;

        drawFlag(handX, handY + 120 * globalScale, globalScale); // Regola l'altezza dell'asta rispetto alla mano
    }

    // Inizia l'animazione
    generatePile();
    animate();

</script>
</body>
</html>
