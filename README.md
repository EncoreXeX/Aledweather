<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
        }
        .emoji {
            font-size: 100px;
        }
        .temperature {
            font-size: 48px;
            margin: 20px 0;
        }
        .forecast {
            display: flex;
            justify-content: space-between;
        }
        .forecast-day {
            text-align: center;
            margin: 10px;
        }
        .forecast-day p {
            margin: 5px 0;
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
        <div class="forecast" id="forecast">
            <!-- 7-day forecast will be displayed here -->
        </div>
    </div>

    <script>
        const apiKey = '2oASoLPd1jV7TVoLkhY0RjTmpG2HZ7Gf';  // Your Tomorrow.io API key
        const lat = 56.8667;  // Latitude for Åled, Sweden
        const lon = 12.8917;  // Longitude for Åled, Sweden
        const apiUrl = `https://api.tomorrow.io/v4/weather/forecast?location=${lat},${lon}&apikey=${apiKey}`;

        const emojiMap = {
            'clear': '☀️',
            'cloudy': '☁️',
            'rain': '🌧️',
            'snow': '❄️',
            'fog': '🌫️',
            'wind': '💨',
            'thunderstorm': '⛈️'
        };

        async function fetchWeather() {
            try {
                const response = await fetch(apiUrl);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const data = await response.json();
                console.log('API response:', data);

                // Assuming the structure of Tomorrow.io's response
                const currentWeather = data.timelines.daily[0].values;
                const weatherCode = currentWeather.weatherCode;
                const temperature = currentWeather.temperature;

                const emoji = emojiMap[weatherCode] || '❓';
                document.querySelector('#current-weather .emoji').textContent = emoji;
                document.querySelector('#current-weather .temperature').textContent = `${Math.round(temperature)}°C`;
                document.querySelector('#current-weather .description').textContent = currentWeather.weatherDescription || 'Ingen beskrivning tillgänglig';

                // Set forecast
                const forecastElement = document.getElementById('forecast');
                forecastElement.innerHTML = ''; // Clear forecast first

                data.timelines.daily.slice(0, 7).forEach(day => {
                    const forecastDay = document.createElement('div');
                    forecastDay.classList.add('forecast-day');

                    const weatherEmoji = emojiMap[day.values.weatherCode] || '❓';
                    const tempMin = Math.round(day.values.temperatureMin);
                    const tempMax = Math.round(day.values.temperatureMax);
                    const date = new Date(day.time).toLocaleDateString('sv-SE', { weekday: 'long' });

                    forecastDay.innerHTML = `
                        <p>${date}</p>
                        <p class="emoji">${weatherEmoji}</p>
                        <p>${tempMin}°C / ${tempMax}°C</p>
                    `;
                    forecastElement.appendChild(forecastDay);
                });
            } catch (error) {
                console.error('Error fetching weather data:', error);
                document.querySelector('#current-weather .description').textContent = 'Kunde inte hämta väderdata.';
            }
        }

        fetchWeather();
    </script>
</body>
</html>
