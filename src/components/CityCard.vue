<template>
	<div
	class="flex py-6 px-3 bg-weather-secondary
	 rounded-md shadow-md cursor-pointer">
	 <div class="flex flex-col flex-1 gap-2">
		<h2 class="text-3xl">{{ city.city }}</h2>
		<h3>{{ city.state }}</h3>
	 </div>
	 <div class="flex flex-col gap-2">
		<p class="text-3xl self-end">
			{{ Math.round(city.weather.current.temp) }}&deg;
		</p>
		<div class="flex gap-2">
			<span class="text-xs">
				H:
				{{ Math.round(dailyInfo.temp.max) }}&deg;
			</span>
			<span class="text-xs">
				L:
				{{ Math.round(dailyInfo.temp.min) }}&deg;
			</span>
		</div>
	 </div>

	</div>
</template>

<script setup>
import { ref } from 'vue';
const props = defineProps({
	city: {
		type: Object,
		default: () => {}
	}
})

const dailyInfo = ref([]);

//get current time
const time = Math.round(new Date().getTime() / 1000);
//get temp value that matches current time
const getDailyTemp = () => {
	// console.log(props.city.weather.daily)
	props.city.weather.daily.forEach(dailyData => {
		if (time + 100000 >= dailyData.dt && time >= dailyData.sunrise) {
			dailyInfo.value = dailyData;
		}
	});
}

getDailyTemp();


</script>

