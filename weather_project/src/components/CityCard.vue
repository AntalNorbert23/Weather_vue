<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-column flex-1">
            <h2 class="text-3xl">{{ city.city }}</h2>
            <h3>{{ city.state }}</h3>
        </div>

        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ city.weather?.current?.temp_c?.toFixed(1) || '--' }}°C 
            </p>
        </div>

        <div class="flex flex-col gap-2 px-4">
            <span class="text-xs">
                H: {{ city.weather?.forecast?.forecastday[0]?.day?.maxtemp_c?.toFixed(1) || '--' }}°C 
            </span>
            <span class="text-xs">
                L: {{ city.weather?.forecast?.forecastday[0]?.day?.mintemp_c?.toFixed(1) || '--' }}°C 
            </span>
        </div>
    </div>
</template>

<script setup>
    defineProps({
        city:{
            type:Object,
            default:()=>{},
        }
    })
</script>