<script>
    const apiKey = '10c978fd968c0e15519e04f026e2d054';  // Ensure this API key is correct and has permissions
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
            console.log('Fetching weather data...');
            const response = await fetch(apiUrl);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);  // Log error status
            }
            const data = await response.json();
            console.log('API response:', data);

            // Only proceed if data is valid
            if (data && data.current) {
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
            } else {
                throw new Error('Invalid data received from API');
            }
        } catch (error) {
            console.error('Error fetching weather data:', error);
            document.querySelector('#current-weather .description').textContent = 'Kunde inte hämta väderdata.';
        }
    }

    fetchWeather();
</script>
