<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Widget</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }

        #weather-widget {
            width: 1177px;
            height: 504px;
            background-color: #f0f0f0;
            border: 1px solid #ddd;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            padding: 20px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            border-radius: 10px;
        }

        #weather-widget h2 {
            margin: 0;
            font-size: 2em;
        }

        #weather-widget p {
            margin: 5px 0;
            font-size: 1.2em;
        }

        .temperature {
            font-size: 3em;
            color: #ff8c00;
        }
    </style>
</head>
<body>

<div id="weather-widget">
    <h2>Weather in Your Location</h2>
    <p id="description">Loading...</p>
    <p class="temperature" id="temp">-- °C</p>
    <p>Latitude: 56.743407 | Longitude: 12.950762</p>
</div>

<script>
    const lat = 56.743407;
    const lon = 12.950762;
    const apiKey = 'YOUR_API_KEY_HERE';
    
    fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`)
        .then(response => response.json())
        .then(data => {
            const description = data.weather[0].description;
            const temp = data.main.temp;

            document.getElementById('description').textContent = description.charAt(0).toUpperCase() + description.slice(1);
            document.getElementById('temp').textContent = `${temp} °C`;
        })
        .catch(error => {
            console.error('Error fetching the weather data:', error);
        });
</script>

</body>
</html>
