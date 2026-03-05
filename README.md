index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROSSAFLAG</title>
    <style>
        /* Centratura assoluta e pulizia */
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: #ffffff; /* Sfondo bianco come richiesto */
            overflow: hidden;
        }

        canvas {
            display: block;
            /* L'immagine non sarà infossata, ma centrata */
            max-width: 95vw;
            max-height: 95vh;
        }
    </style>
</head>
<body>

<canvas id="animationCanvas"></canvas>

<script>
    const canvas = document.getElementById('animationCanvas');
    const ctx = canvas.getContext('2d');
    const img = new Image();

    // Carica l'immagine caricata nell'ultimo turno
    img.src = 'ROSSAFLAG.jpg'; 

    let frame = 0;
    const totalFrames = 3;
    let lastUpdate = 0;
    const fps = 6; // Velocità della "GIF"

    function resize() {
        // Impostiamo il canvas per essere grande quanto l'immagine o lo schermo
        const scale = Math.min(window.innerWidth / img.width, window.innerHeight / img.height) * 0.9;
        canvas.width = img.width * scale;
        canvas.height = img.height * scale;
    }

    function animate(now) {
        if (!img.complete) {
            requestAnimationFrame(animate);
            return;
        }

        // Controllo del tempo per simulare i 3 frame della GIF
        if (!lastUpdate || now - lastUpdate > (1000 / fps)) {
            frame = (frame + 1) % totalFrames;
            lastUpdate = now;
        }

        ctx.clearRect(0, 0, canvas.width, canvas.height);

        const w = canvas.width;
        const h = canvas.height;

        // Offset per l'alzata dell'asta (Frame 0: 0px, Frame 1: -5px, Frame 2: -2px)
        const offsets = [0, -6, -3];
        const currentOffset = offsets[frame] * (w / img.width); // Scalato

        // 1. DISEGNA LA BASE (COLLINETTA STATICA)
        // Ritagliamo la parte inferiore dell'immagine (dallo stickman in giù)
        ctx.drawImage(img, 
            0, img.height * 0.45, img.width, img.height * 0.55, // Sorgente
            0, h * 0.45, w, h * 0.55 // Destinazione
        );

        // 2. DISEGNA L'ASTA E LA BANDIERA (PARTE MOBILE)
        // Ritagliamo la parte superiore e applichiamo l'offset verticale
        ctx.drawImage(img, 
            0, 0, img.width, img.height * 0.45, // Sorgente
            0, (h * 0.0) + currentOffset, w, h * 0.45 // Destinazione con movimento
        );

        requestAnimationFrame(animate);
    }

    window.addEventListener('resize', resize);
    img.onload = () => {
        resize();
        requestAnimationFrame(animate);
    };
</script>
</body>
</html>
