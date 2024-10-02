<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Väderprognos Åled</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }

        #weather-widget {
            width: 1177px;
            height: 504px;
            background-color: #f0f0f0;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
        }

        #current-weather {
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 100%;
        }

        .weather-icon {
            width: 100px;
            height: 100px;
        }

        .temperature {
            font-size: 48px;
            font-weight: bold;
        }

        #forecast {
            display: flex;
            justify-content: space-between;
            width: 100%;
        }

        .day {
            text-align: center;
            flex: 1;
        }

        .day-icon {
            width: 50px;
            height: 50px;
        }

        .day-name {
            font-weight: bold;
        }

        .day-temp {
            font-size: 24px;
        }
    </style>
</head>
<body>

<div id="weather-widget">
    <div id="current-weather">
        <div>
            <img id="current-icon" class="weather-icon" src="" alt="Weather Icon">
        </div>
        <div class="temperature" id="current-temp">-- °C</div>
        <div id="current-description"></div>
    </div>
    <div id="forecast"></div>
</div>

<script>
    const apiKey = '2bce8589e4d6e338da17cfefa1de9d53';
    const location = 'Åled,SE';
    const apiUrl = `https://api.openweathermap.org/data/2.5/onecall?lat=56.8790&lon=12.9917&exclude=minutely,hourly&units=metric&lang=sv&appid=${apiKey}`;

    async function fetchWeather() {
        try {
            const response = await fetch(apiUrl);
            const data = await response.json();

            // Update current weather
            const currentIcon = document.getElementById('current-icon');
            const currentTemp = document.getElementById('current-temp');
            const currentDescription = document.getElementById('current-description');

            currentIcon.src = `http://openweathermap.org/img/wn/${data.current.weather[0].icon}@2x.png`;
            currentTemp.textContent = `${Math.round(data.current.temp)} °C`;
            currentDescription.textContent = data.current.weather[0].description.charAt(0).toUpperCase() + data.current.weather[0].description.slice(1);

            // Update forecast
            const forecast = document.getElementById('forecast');
            forecast.innerHTML = ''; // Clear previous content

            data.daily.slice(0, 7).forEach(day => {
                const dayElement = document.createElement('div');
                dayElement.classList.add('day');

                const date = new Date(day.dt * 1000);
                const dayName = date.toLocaleDateString('sv-SE', { weekday: 'long' });

                dayElement.innerHTML = `
                    <div class="day-name">${dayName}</div>
                    <img class="day-icon" src="http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png" alt="Icon">
                    <div class="day-temp">${Math.round(day.temp.day)} °C</div>
                `;

                forecast.appendChild(dayElement);
            });
        } catch (error) {
            console.error('Error fetching weather data:', error);
        }
    }

    fetchWeather();
</script>

</body>
</html>
