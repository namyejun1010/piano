<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Music Program</title>
    <script>
        function playTone(octave, note, duration, startTime) {
            if (octave === 0 && note === 0) {
                // Do nothing for silence, just wait for the duration
                return setTimeout(() => {}, duration * 1000);
            }

            const audioContext = new (window.AudioContext || window.webkitAudioContext)();
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();

            const noteFrequencies = {
                1: 261.63,
                2: 277.18,
                3: 293.66,
                4: 311.13,
                5: 329.63,
                6: 349.23,
                7: 369.99,
                8: 392.00,
                9: 415.30,
                10: 440.00,
                11: 466.16,
                12: 493.88,
                13: 523.25
            };

            const frequency = noteFrequencies[note] * Math.pow(2, octave - 3);
            oscillator.frequency.value = frequency;

            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);

            oscillator.start(audioContext.currentTime + startTime);
            oscillator.stop(audioContext.currentTime + startTime + duration);
        }

        function playMusic() {
            const input = document.getElementById("musicInput").value;
            const lines = input.split('\n');
            let currentTime = 0;

            for (const line of lines) {
                const [tone, duration] = line.split(',');
                const octave = parseInt(tone.slice(0, tone.length - 2), 10) || 0;
                const note = parseInt(tone.slice(-2), 10) || 0;
                const time = parseFloat(duration);

                if (octave === 0 && note === 0) {
                    // Silence handling (e.g., "xxx,a")
                    currentTime += time;
                } else {
                    playTone(octave, note, time, currentTime);
                    currentTime += time;
                }
            }
        }

        function help() {
            const usageInstructions = `
                <h1>How to Use the Music Program</h1>
                <p>Follow these instructions to play music:</p>
                <ol>
                    <li>Input a sequence of tones and durations in the text area. Each line should have the format:</li>
                    <pre>OCTAVENOTE,DURATION</pre>
                    <p>For example:</p>
                    <pre>302,1&#10;511,2&#10;408,0.5&#10;713,1</pre>
                    <li>Click the "Play" button to hear the music you composed.</li>
                    <li>Available Notes:
                        <ul>
                            <li>1: Do (C)</li>
                            <li>2: Do# (C#)</li>
                            <li>3: Re (D)</li>
                            <li>... (refer to the full list in the main program)</li>
                        </ul>
                    </li>
                </ol>
                <p>Enjoy creating your own melodies!</p>
                <p>this is a tip for sound...</p>
                <p>Do: 1, Do#: 2, Re: 3, Re#: 4, Mi: 5, Mi#: 6</p>
                <p>Pa: 7, Sol: 8, Sol#: 9, Ra: 10, Ra#: 11, Si: 12, Si#: 13</p>
                <p>no more.</P>
                    <li>To add silence, type <strong>xxx,a</strong>, where "a" is the duration in seconds.</li>
                </ol>
                <p>Enjoy creating your own melodies!</p>
            `;
            const helpWindow = window.open("", "_blank", "width=600,height=400");
            helpWindow.document.write(usageInstructions);
            helpWindow.document.close();
        }
    </script>
</head>
<body>
    <h1>Music Program</h1>
    <textarea id="musicInput" rows="10" cols="30" placeholder="302,1&#10;511,2&#10;408,0.5&#10;xxx,1"></textarea>
    <br>
    <button onclick="playMusic()">Play</button>
    <button onclick="help()">Help</button>
</body>
</html>
