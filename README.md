<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Birthday Cake with Flowers</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            height: 100vh;
            background-color: pink;
            margin: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
        }

        h1 {
            position: absolute;
            top: 150px;
            text-align: center;
            color: deeppink;
        }

        #cake-container {
            position: relative;
            top: 80px;
        }

        #cake {
            width: 300px;
            transition: opacity 1s ease;
        }

        .candles {
            position: absolute;
            top: -50px;
            left: 55%;
            transform: translateX(-50%);
            width: 150px;
            transition: opacity 1s ease;
        }

        #bouquet {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 300px;
            transition: opacity 1s ease;
        }

        .hidden {
            opacity: 0;
        }

        .hidden-h1 {
            display: none;
        }
    </style>
</head>
<body>

<audio id="background-music" loop>
    <source src="hbd.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>

<h1 id="initial-message">Happy Birthday, Alaine! Blow the candles</h1>

<div id="cake-container">
    <img src="cake.png" alt="Cake" id="cake">
    <img src="candles.png" alt="Candles" class="candles">
    <img src="OIP.png" alt="Bouquet of Flowers" id="bouquet">
</div>

<h1 id="new-message" class="hidden-h1">Here's a Virtual bouquet for you!</h1>

<script>
    const audioElement = document.getElementById('background-music');

    // Start playing the audio after user interaction (click)
    document.addEventListener('click', function() {
        audioElement.play().catch(error => {
            console.error('Playback prevented:', error);
        });
    });

    function detectBlow() {
        navigator.mediaDevices.getUserMedia({ audio: true })
            .then(function(stream) {
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                const analyser = audioContext.createAnalyser();
                const microphone = audioContext.createMediaStreamSource(stream);
                const javascriptNode = audioContext.createScriptProcessor(2048, 1, 1);

                analyser.smoothingTimeConstant = 0.8;
                analyser.fftSize = 1024;

                microphone.connect(analyser);
                analyser.connect(javascriptNode);
                javascriptNode.connect(audioContext.destination);

                javascriptNode.onaudioprocess = function() {
                    const array = new Uint8Array(analyser.frequencyBinCount);
                    analyser.getByteFrequencyData(array);
                    const maxVolume = Math.max(...array);

                    if (maxVolume > 120) {
                        extinguishCandlesAndCake();
                    }
                };
            })
            .catch(function(err) {
                console.error('Error accessing microphone: ' + err);
            });
    }

    function extinguishCandlesAndCake() {
        const candles = document.querySelector('.candles');
        const cake = document.getElementById('cake');
        const bouquet = document.getElementById('bouquet');
        const initialMessage = document.getElementById('initial-message');
        const newMessage = document.getElementById('new-message');

        candles.classList.add('hidden');
        cake.classList.add('hidden');
        initialMessage.classList.add('hidden-h1');

        setTimeout(() => {
            bouquet.style.display = 'block';
            bouquet.style.opacity = '1';
            newMessage.classList.remove('hidden-h1');
        }, 1000);
    }

    detectBlow();
</script>

</body>
</html>
