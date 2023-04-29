<template>
	<div class="flex flex-col flex-1 items-center">
		<div
		 v-if="route.query.preview"
		 class="text-white p-4 bg-weather-secondary w-full text-center">
		 <p>You are currently previewing this city, click the '+' icon
			to start tracking this city.
		 </p>
		</div>

	</div>
	<div class="flex flex-col items-center text-white py-12">
		<h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
		<p text-sm mb-12>
			{{
				new Date(weatherData.currentTime).toLocaleDateString(
					'en-GB',
					{
						weekday: 'short',
						day: '2-digit',
						month: 'long'
					})
			}}
			{{
				new Date(weatherData.currentTime).toLocaleTimeString(
					'en-GB',
					{
						hour: '2-digit',
						minute: '2-digit'
					}
				)
			}}
		</p>
		<p class="text-8xl mb-8">
			{{ Math.round(weatherData.current.temp) }}&deg;
		</p>
		<div class="text-center">
				<p>Feels like :
					{{ Math.round(weatherData.current.feels_like) }}&deg;
				</p>
				<p class="capitalize">
					{{ weatherData.current.weather[0].description }}
				</p>
				<img
				 class="w-[150px] h-auto"
				 :src="
				    `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
				 "
				 alt="weatherData.current.weather[0].description"
				/>
		</div>

	</div>

	<hr class="border-white border-opacity-10 border w-full"/>

	<div class="max-w-screen-md w-full py-12">
		<div class="mx-8 text-white">
			<h2 class="mb-4">Hourly Weather</h2>
			<div class="flex gap-10 overflow-x-scroll">
				<div
				 v-for="hourData in weatherData.hourly"
				 :key="hourData.dt"
				 class="flex flex-col gap-4 items-center">
				 <p class="whitespace-nowrap text-md">
					{{
						new Date(
							hourData.currentTime
						).toLocaleTimeString('en-GB', {
							hour: 'numeric'
						})
					}}
				 </p>
				 <img
				  class="w-auto h-[50px] object-cover"
				  :src="
				  `http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`
				  "
				  alt="hourData.weather[0].description"
				  />
				  <p class="text-xl">
					{{ Math.round(hourData.temp) }}&deg;
				  </p>
				</div>
			</div>
		</div>

	</div>

	<hr class="border-white border-opacity-10 border w-full"/>

	<div class="max-w-screen-md w-full py-12">
		<div class="mx-8 text-white">
			<h1 class="mb-4">7 Day Forecast</h1>
			<div
			 v-for="dayData in weatherData.daily"
			 :key="dayData.dt"
			 class="flex items-center"
			 >
			 <p class="flex-1">
				{{
					new Date(dayData.dt * 1000).toLocaleDateString(
						'en-GB',
						{
							weekday: 'long'
						}
					)
			    }}
			 </p>
			 <img
			 	class="w-[50px] h-[50px] object-cover"
				:src="
				`http://openweathermap.org/img/wn/${dayData.weather[0].icon}@2x.png`
				"
				alt="dayData.weather[0].description"
			 />
			</div>
		</div>
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
		return weatherData.data;
	} catch (err) {
		console.error(err);
	}
}
const weatherData = await getWeatherData();
</script>

