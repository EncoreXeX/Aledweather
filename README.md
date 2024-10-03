<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Väder i Åled</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .weather-widget {
            text-align: center;
            border: 2px solid #ccc;
            padding: 20px;
            border-radius: 10px;
            background-color: #fff;
            width: 1177px;
            height: 504px;
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }
        .emoji {
            font-size: 80px;
        }
        .temperature {
            font-size: 100px;
            margin: 0;
        }
        .description {
            font-size: 24px;
        }
        .forecast {
            font-size: 24px;
            margin: 0;
        }
    </style>
</head>
<body>
    <div class="weather-widget">
        <div id="current-weather">
            <div class="emoji">⏳</div>
            <div class="temperature">--°C</div>
            <div class="description">Väntar på väderdata...</div>
        </div>
        <div id="forecast" class="forecast">
            <!-- Tomorrow's forecast will be displayed here -->
        </div>
    </div>

    <script>
        const apiKey = '2oASoLPd1jV7TVoLkhY0RjTmpG2HZ7Gf';  // Your Tomorrow.io API key
        const lat = 56.8667;  // Latitude for Åled, Sweden
        const lon = 12.8917;  // Longitude for Åled, Sweden
        const apiUrl = `https://api.tomorrow.io/v4/weather/forecast?location=${lat},${lon}&apikey=${apiKey}`;

        const emojiMap = {
            '1000': '☀️',  // Clear
            '1001': '☁️',  // Cloudy
            '1100': '🌤️',  // Mostly Clear
            '1101': '🌥️',  // Partly Cloudy
            '1102': '☁️',  // Cloudy
            '4000': '🌧️',  // Drizzle
            '4001': '🌧️',  // Rain
            '4200': '🌦️',  // Light Rain
            '4201': '🌧️',  // Heavy Rain
            '5000': '❄️',  // Snow
            '5001': '❄️',  // Flurries
            '5100': '🌨️',  // Light Snow
            '5101': '❄️',  // Heavy Snow
            '6000': '🌫️',  // Freezing Drizzle
            '6001': '🌧️',  // Freezing Rain
            '6200': '🌧️',  // Light Freezing Rain
            '6201': '🌧️',  // Heavy Freezing Rain
            '7000': '🌫️',  // Ice Pellets
            '7101': '🌨️',  // Heavy Ice Pellets
            '7102': '🌨️',  // Light Ice Pellets
            '8000': '⛈️'   // Thunderstorm
        };

        async function fetchWeather() {
            try {
                const response = await fetch(apiUrl);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const data = await response.json();
                console.log('API response:', data);

                // Set current weather
                const currentWeather = data.timelines.daily[0].values;  // Today
                const temperature = Math.round(currentWeather.temperatureMax);  // Current temperature
                let weatherEmoji = emojiMap[currentWeather.weatherCodeMax] || '❓';

                // Check for cloud cover to adjust the emoji
                const cloudCover = currentWeather.cloudCoverAvg;
                if (currentWeather.weatherCodeMax === 1000 && cloudCover > 15) {
                    weatherEmoji = emojiMap['1100'];  // Mostly Clear if cloud cover > 15%
                } else if (currentWeather.weatherCodeMax === 1000 && cloudCover > 50) {
                    weatherEmoji = emojiMap['1101'];  // Partly Cloudy if cloud cover > 50%
                }

                document.querySelector('#current-weather .temperature').textContent = `${temperature}°C`;
                document.querySelector('#current-weather .emoji').textContent = weatherEmoji;
                document.querySelector('#current-weather .description').textContent = 'Nuvarande väder i Åled';

                // Set forecast for tomorrow
                const tomorrowWeather = data.timelines.daily[1].values;  // Tomorrow
                const tomorrowEmoji = emojiMap[tomorrowWeather.weatherCodeMax] || '❓';
                const tomorrowDate = new Date(data.timelines.daily[1].time).toLocaleDateString('sv-SE', { weekday: 'long' });

                document.querySelector('#forecast').innerHTML = `
                    <p>Imorgon (${tomorrowDate}): ${tomorrowEmoji}</p>
                `;
            } catch (error) {
                console.error('Error fetching weather data:', error);
                document.querySelector('#current-weather .description').textContent = 'Kunde inte hämta väderdata.';
            }
        }

        fetchWeather();
    </script>
</body>
</html>
