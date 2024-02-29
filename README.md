# GreenComputeTrackerV
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <title>GreenComputeTracker</title>
    <style>
        body, html {
    height: 100%;
    margin: 0;
    padding: 0;
    background: url('https://images.pexels.com/photos/9708018/pexels-photo-9708018.jpeg') no-repeat center center fixed; 
    background-size: cover;
    display: flex;
    justify-content: center;
    align-items: center; /* Diese Eigenschaft zentriert die Inhalte vertikal */
}

.content {
    width: auto; /* Automatische Breite basierend auf Inhalt */
    max-width: 600px; /* Oder eine maximale Breite, die zu Ihrem Design passt */
    background: rgba(255, 255, 255, 0.8);
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    text-align: center;
    margin: 20px; /* Fügt einen Rand um den Kasten hinzu */
}

        #scale { 
            width: 100%; 
            height: 50px; 
            background: linear-gradient(to right, #00FF00 33%, #FFFF00 66%, #FF0000 100%);
            position: relative;
            margin-top: 20px;
        }
        #pointer { 
            height: 50px; 
            width: 2px; 
            background-color: #000; 
            position: absolute; 
        }
        #timerDisplay, #co2EmissionDisplay, #tips {
            margin-top: 20px;
        }
    </style>
</head>
<body onload="startTimer()">
    <div class="content">
        <h1>GreenComputeTracker</h1>
        <input type="number" id="power" placeholder="Netzteil Verbrauch in Watt">
        <div id="timerDisplay">Timer: 00:00:00</div>
        <div id="scale">
            <div id="pointer"></div>
        </div>
        <div id="co2EmissionDisplay"></div>
        <div id="tips"></div>
        <button onclick="stopTimer()">CO2 berechnen</button>
        <button onclick="resetTimer()">Reset</button>
    </div>

    <script>
        var startTime, timerInterval, co2PerKWh = 0.475;

        function startTimer() {
            startTime = new Date();
            timerInterval = setInterval(updateTimerDisplay, 1000);
        }

        function updateTimerDisplay() {
            var currentTime = new Date();
            var elapsed = currentTime - startTime;
            var seconds = Math.floor((elapsed / 1000) % 60);
            var minutes = Math.floor((elapsed / (1000 * 60)) % 60);
            var hours = Math.floor(elapsed / (1000 * 60 * 60));
            document.getElementById("timerDisplay").textContent = 
                (hours < 10 ? "0" + hours : hours) + ":" +
                (minutes < 10 ? "0" + minutes : minutes) + ":" +
                (seconds < 10 ? "0" + seconds : seconds);
        }

        function stopTimer() {
            clearInterval(timerInterval);
            var timeSpent = (new Date() - startTime) / (1000 * 3600); // Zeit in Stunden
            var powerUsage = document.getElementById("power").value || 0; // Leistungsaufnahme in Watt
            var co2Emission = (powerUsage * timeSpent * co2PerKWh) / 1000; // Emission in kg
            document.getElementById("co2EmissionDisplay").textContent = "CO2-Ausstoß: " + co2Emission.toFixed(2) + " kg";
            updatePointerPosition(co2Emission);
            displayComparisonMessage(co2Emission);
        }

        // Diese Funktion setzt den Timer und die Anzeigen zurück.
        function resetTimer() {
            clearInterval(timerInterval);
            startTime = new Date();
            timerInterval = setInterval(updateTimerDisplay, 1000);
            document.getElementById("timerDisplay").textContent = "Timer: 00:00:00";
            document.getElementById("co2EmissionDisplay").textContent = "";
            document.getElementById("pointer").style.left = "0%";
            document.getElementById("tips").textContent = "";
        }

        // Diese Funktion aktualisiert die Position des Zeigers auf der Skala.
        function updatePointerPosition(co2Emission) {
            var scaleWidth = document.getElementById("scale").offsetWidth;
            var maxCO2Emission = 2; // Setzen Sie hier den maximalen CO2-Ausstoß ein.
            var pointerPosition = (co2Emission / maxCO2Emission) * scaleWidth;
            pointerPosition = Math.min(pointerPosition, scaleWidth - 2); // Subtrahieren Sie die Zeigerbreite.
            document.getElementById('pointer').style.left = pointerPosition + 'px';
        }

        // Diese Funktion zeigt Vergleichsinformationen basierend auf den CO2-Emissionen an.
        function displayComparisonMessage(co2Emission) {
            var message = getComparisonMessage(co2Emission);
            document.getElementById("tips").textContent = message;
        }

        // Diese Funktion liefert eine Nachricht basierend auf den CO2-Emissionen.
        function getComparisonMessage(co2Emission) {
            if (co2Emission < 0.1) {
                return "Das ist weniger als der CO2-Ausstoß für eine 10-minütige Dusche.";
            } else if (co2Emission < 0.5) {
                return "Das entspricht dem Aufladen Ihres Smartphones für mehrere Jahre.";
            } else {
                return "Das könnte ein kleines Zimmer für einen ganzen Tag heizen.";
            }
        }
    </script>
</body>
</html>
