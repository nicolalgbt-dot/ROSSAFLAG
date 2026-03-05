index.html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROSSAFLAG</title>
    <style>
        body, html {
            margin: 0; padding: 0; width: 100%; height: 100%;
            background-color: #ffffff; display: flex; flex-direction: column;
            font-family: 'Arial Black', sans-serif;
            position: relative; overflow: hidden;
        }

        /* Titolo ROSSAFLAG con riflesso */
        header { width: 100%; padding: 20px 0; text-align: center; z-index: 10; }

        h1 {
            margin: 0; font-size: clamp(2rem, 8vw, 3.5rem); text-transform: uppercase;
            letter-spacing: 5px; font-weight: 900;
            background: linear-gradient(to bottom, #d00000 0%, #ffcc00 40%, #ffffff 50%, #ffcc00 60%, #d00000 100%);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            filter: drop-shadow(3px 3px 2px rgba(0,0,0,0.15));
        }

        /* Singolo Pulsante Pugno in alto a sinistra */
        .audio-controls {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 100;
        }

        .fist-btn {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            border: 4px solid #ffcc00;
            background: radial-gradient(circle, #ff0000 0%, #a00000 100%);
            font-size: 35px;
            cursor: pointer;
            display: flex;
            justify-content: center; align-items: center;
            transition: all 0.2s ease;
            box-shadow: 0 4px 0 #600000, 0 8px 15px rgba(0,0,0,0.2);
            user-select: none;
        }

        /* Colore del pugno (✊) */
        .fist-btn span {
            filter: sepia(1) saturate(10) hue-rotate(10deg) brightness(1.2);
        }

        /* Stato Attivo (quando la musica suona) */
        .fist-btn.active {
            background: radial-gradient(circle, #ffcc00 0%, #ffaa00 100%);
            border-color: #ff0000;
            box-shadow: 0 0 20px #ffcc00;
        }
        
        .fist-btn.active span { filter: none; }

        /* Video Centrale */
        main { flex-grow: 1; display: flex; justify-content: center; align-items: center; }
        video { max-width: 90%; max-height: 70vh; outline: none; }
    </style>
</head>
<body>

    <div class="audio-controls">
        <div id="masterBtn" class="fist-btn" onclick="nextCanto()"><span>✊</span></div>
    </div>

    <header>
        <h1>ROSSAFLAG</h1>
    </header>

    <main>
        <video id="mainVideo" autoplay loop muted playsinline preload="auto">
            <source src="data:video/mp4;base64,INCOLLA_IL_TUO_VIDEO_QUI" type="video/mp4">
        </video>
    </main>

    <audio id="canto1" loop><source src="data:audio/mpeg;base64,INCOLLA_BRANO_1"></audio>
    <audio id="canto2" loop><source src="data:audio/mpeg;base64,//uQbAADMAAATQAAAAAIwEmuAAAAQUwJJoAEAAAbASOgAAAAAIAAAAAAAAAwBpuQQYEUsZecygMEqLu6p5oCQFgAA4kgx33C+4KpCgAztB13ioARAEm/MvCIoCEp2qk..."></audio>
    <audio id="canto3" loop><source src="data:audio/mpeg;base64,SUQzAwAAAAAPdlRBTEIAAAANAAAAc2VuemEgdGl0b2xvVElUMgAAABEAAABUcmFjY2lhIEF1ZGlvIDE4VFJDSwAAAAYAAAAxOC8xOFRDT04AAAAMAAAAU2Nvbm9zY2l1dG9QUklWAAAADgAAUGVha1ZhbHVlAHxkAABQUklWAAAAEQAAQXZlcmFnZUxldmVsAGkPAABUUEUxAAAADwAAAG5lc3N1biBhcnRpc3Rh..."></audio>

    <script>
        const v = document.getElementById('mainVideo');
        const canti = [
            document.getElementById('canto1'),
            document.getElementById('canto2'),
            document.getElementById('canto3')
        ];
        const btn = document.getElementById('masterBtn');
        
        let currentCanto = -1; // -1 significa silenzio

        function nextCanto() {
            // Fermiamo quello che sta suonando ora (se c'è)
            if (currentCanto !== -1) {
                canti[currentCanto].pause();
                canti[currentCanto].currentTime = 0;
            }

            // Passiamo al brano successivo
            currentCanto++;

            // Se abbiamo superato l'ultimo brano, torniamo al silenzio
            if (currentCanto >= canti.length) {
                currentCanto = -1;
                btn.classList.remove('active');
            } else {
                // Facciamo partire il nuovo brano
                canti[currentCanto].play();
                btn.classList.add('active');
                v.play();
            }
        }

        // Forza loop video
        setInterval(() => { if (v.paused) v.play(); }, 500);
    </script>
</body>
</html>
