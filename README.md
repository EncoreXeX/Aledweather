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
        const apiUrl = `https://www.metaweather.com/api/location/search/?query=Aled`;

        const emojiMap = {
            'sn': '❄️', // Snow
            'sl': '🌨️', // Sleet
            'h': '🌧️',  // Hail
            't': '⛈️',  // Thunderstorm
            'hr': '🌧️', // Heavy Rain
            'lr': '🌦️', // Light Rain
            's': '🌦️',  // Showers
            'hc': '☁️', // Heavy Cloud
            'lc': '🌤️', // Light Cloud
            'c': '☀️'   // Clear
        };

        async function fetchLocation() {
            try {
                const response = await fetch(apiUrl);
                const locations = await response.json();
                if (locations.length === 0) throw new Error('No location found');
                
                const locationId = locations[0].woeid;
                fetchWeather(locationId);
            } catch (error) {
                console.error('Error fetching location data:', error);
                document.querySelector('#current-weather .description').textContent = 'Kunde inte hämta platsdata.';
            }
        }

        async function fetchWeather(locationId) {
            try {
                const weatherUrl = `https://www.metaweather.com/api/location/${locationId}/`;
                const response = await fetch(weatherUrl);
                const data = await response.json();
                console.log('API response:', data);

                const currentWeather = data.consolidated_weather[0];
                document.querySelector('#current-weather .emoji').textContent = emojiMap[currentWeather.weather_state_abbr] || '❓';
                document.querySelector('#current-weather .temperature').textContent = `${Math.round(currentWeather.the_temp)}°C`;
                document.querySelector('#current-weather .description').textContent = currentWeather.weather_state_name;

                const forecastElement = document.getElementById('forecast');
                forecastElement.innerHTML = ''; // Clear forecast first

                data.consolidated_weather.slice(1, 7).forEach(day => {
                    const forecastDay = document.createElement('div');
                    forecastDay.classList.add('forecast-day');

                    const weatherEmoji = emojiMap[day.weather_state_abbr] || '❓';
                    const tempMin = Math.round(day.min_temp);
                    const tempMax = Math.round(day.max_temp);
                    const date = new Date(day.applicable_date).toLocaleDateString('sv-SE', { weekday: 'long' });

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

        fetchLocation();
    </script>
</body>
</html>
