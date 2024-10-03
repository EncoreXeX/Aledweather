<script>
    const apiKey = '2bce8589e4d6e338da17cfefa1de9d53';
    const lat = 56.8667;
    const lon = 12.8917;
    const apiUrl = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=hourly,minutely&units=metric&lang=sv&appid=${apiKey}`;

    const emojiMap = {
        'Clear': '☀️',
        'Clouds': '☁️',
        'Rain': '🌧️',
        'Drizzle': '🌦️',
        'Thunderstorm': '⛈️',
        'Snow': '❄️',
        'Mist': '🌫️',
        'Fog': '🌫️'
    };

    async function fetchWeather() {
        try {
            console.log('Fetching weather data...');  // Debugging: Check if fetch is triggered
            const response = await fetch(apiUrl);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);  // Debugging: Log error status
            }
            const data = await response.json();
            console.log('API response:', data);  // Debugging: Log the full API response to see what’s coming back

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
        } catch (error) {
            console.error('Error fetching weather data:', error);  // Debugging: Log any errors to the console
            document.querySelector('#current-weather .description').textContent = 'Kunde inte hämta väderdata.';
        }
    }

    fetchWeather();
</script>
