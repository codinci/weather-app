<template>
	<div>

	</div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';

const route = useRoute();
const WEATHER_API_KEY = import.meta.env.VITE_WEATHER_API_KEY;
const getWeatherData = async () => {
	try {
		const weatherData = await axios.get(
			`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}
			&lon=${route.query.lng}&exclude={part}&appid=${WEATHER_API_KEY}&
			units=metric`
		);

		//calculate current date and time
		const localOffset = new Date().getTimezoneOffset() * 60000;
		const utc = weatherData.data.current.dt * 1000 + localOffset;
		weatherData.data.currentTime =
			utc + 1000 * weatherData.data.timezone_offset;

		//calculate hourly weather offset
		weatherData.data.hourly.forEach((hour) => {
			const utc = hour.dt * 1000 + localOffset;
			hour.currentTime =
				utc + 1000 * weatherData.data.timezone_offset;
		})
		return weatherData;
	} catch (err) {
		console.error(err);
	}
}
const weatherData = await getWeatherData();
console.log(weatherData);
</script>
