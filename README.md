<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Väder i Åled</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            width: 1177px;
            height: 504px;
            background-color: #f0f0f0;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .weather-widget {
            text-align: center;
            border: 2px solid #ccc;
            padding: 20px;
            border-radius: 10px;
            background-color: #fff;
            width: 100%;
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
            <div class="emoji">🌤️</div>
            <div class="temperature">--°C</div>
            <div class="description">Väntar på väderdata...</div>
        </div>
        <div class="forecast" id="forecast">
            <!-- 7-day forecast will be displayed here -->
        </div>
    </div>

    <script>
        const apiKey = '2bce8589e4d6e338da17cfefa1de9d53';
        const city = 'Åled';
        const apiUrl = `https://api.openweathermap.org/data/2.5/onecall?lat=56.8667&lon=12.8917&exclude=hourly,minutely&units=metric&lang=sv&appid=${apiKey}`;
        
        const emojiMap = {
            'Clear': '☀️',
            'Clouds': '☁️',
            'Rain': '🌧️',
            'Drizzle': '🌦️',
            'Thunderstorm': '⛈️',
            'Snow': '❄️',
            'Mist': '🌫️'
        };

        async function fetchWeather() {
            const response = await fetch(apiUrl);
            const data = await response.json();

            // Set current weather
            const currentWeather = data.current;
            const currentWeatherEmoji = emojiMap[currentWeather.weather[0].main] || '❓';
            document.querySelector('#current-weather .emoji').textContent = currentWeatherEmoji;
            document.querySelector('#current-weather .temperature').textContent = `${Math.round(currentWeather.temp)}°C`;
            document.querySelector('#current-weather .description').textContent = currentWeather.weather[0].description;

            // Set forecast
            const forecastElement = document.getElementById('forecast');
            forecastElement.innerHTML = ''; // Clear forecast first

            data.daily.slice(0, 7).forEach(day => {
                const forecastDay = document.createElement('div');
                forecastDay.classList.add('forecast-day');
                
                const weatherEmoji = emojiMap[day.weather[0].main] || '❓';
                const tempMin = Math.round(day.temp.min);
                const tempMax = Math.round(day.temp.max);
                const date = new Date(day.dt * 1000).toLocaleDateString('sv-SE', { weekday: 'long' });

                forecastDay.innerHTML = `
                    <p>${date}</p>
                    <p class="emoji">${weatherEmoji}</p>
                    <p>${tempMin}°C / ${tempMax}°C</p>
                `;
                forecastElement.appendChild(forecastDay);
            });
        }

        fetchWeather();
    </script>
</body>
</html>
