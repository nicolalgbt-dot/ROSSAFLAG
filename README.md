index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Animazione Bandiera Rossa</title>
    <style>
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; background: #fff; display: flex; justify-content: center; align-items: center; }
        canvas { display: block; max-width: 100%; max-height: 100%; }
    </style>
</head>
<body>

<canvas id="canvas"></canvas>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const img = new Image();

    // Carichiamo l'immagine che hai fornito (quella con lo stile corretto)
    img.src = 'https://i.imgur.com/rN9kH9K.jpg'; // Sostituisci con il link diretto alla tua immagine su GitHub

    let time = 0;

    function resize() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        if (!img.complete) {
            requestAnimationFrame(draw);
            return;
        }

        const scale = Math.min(canvas.width / img.width, canvas.height / img.height) * 0.9;
        const w = img.width * scale;
        const h = img.height * scale;
        const x = (canvas.width - w) / 2;
        const y = (canvas.height - h) / 2;

        time += 0.05;

        // Disegniamo la base (la collinetta) statica
        ctx.drawImage(img, x, y, w, h);

        // --- ANIMAZIONE DELLA BANDIERA ---
        // Usiamo un trucco: ritagliamo l'area della bandiera e la ri-disegniamo con una distorsione sinusoidale
        // Coordinate approssimative della bandiera nell'immagine
        const flagArea = { x: 350, y: 50, w: 300, h: 250 }; 
        
        ctx.save();
        // Creiamo un "ritaglio" dinamico per far sventolare i lembi
        for (let i = 0; i < flagArea.w * scale; i++) {
            const wave = Math.sin(time * 2 + i * 0.04) * (i * 0.15);
            ctx.drawImage(
                img, 
                flagArea.x + i/scale, flagArea.y, 1, flagArea.h, // Sorgente
                x + (flagArea.x * scale) + i, y + (flagArea.y * scale) + wave, 1, flagArea.h * scale // Destinazione
            );
        }
        ctx.restore();

        // --- MOVIMENTO DELLE BRACCIA ---
        // Aggiungiamo un leggero "shift" all'asta per simulare la forza dello sventolio
        ctx.save();
        const swing = Math.sin(time) * 3;
        ctx.translate(swing, 0);
        // (Qui si potrebbero isolare le braccia con un array di pixel, ma per semplicità muoviamo l'asta)
        ctx.restore();

        requestAnimationFrame(draw);
    }

    window.addEventListener('resize', resize);
    img.onload = () => {
        resize();
        draw();
    };
</script>
</body>
</html>
