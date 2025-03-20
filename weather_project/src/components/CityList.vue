<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"/> 
    </div>

    <p v-if="savedCities.length === 0">
        No locations added. To start viewing a location search for a location in the field above.
    </p>
</template>

<script setup>
    import axios from "axios";
    import { ref } from "vue";
    import CityCard from "./CityCard.vue";
    import { useRouter } from "vue-router";
    const apikey = import.meta.env.VITE_API_KEY;

    const savedCities = ref([]);
    
    const getCities = async () => {
        if (localStorage.getItem("savedCities")) {
            savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
            
            const requests = [];
            savedCities.value.forEach((city) => {
                requests.push(
                    axios.get(`http://api.weatherapi.com/v1/forecast.json?key=${apikey}&q=${city.coords.lat},${city.coords.lng}&days=7&aqi=no&alerts=no`)
                );
            });

            const weatherData = await Promise.all(requests);


            await new Promise((res) => setTimeout(res, 1000));
            weatherData.forEach((value, index) => {
                savedCities.value[index].weather = value.data;
            });
        }
    };

    await getCities();

    const router = useRouter();
    const goToCityView = (city) => {
        router.push({
            name: "cityView",
            params: { state: city.state, city: city.city },
            query: { id: city.id, lat: city.coords.lat, lng: city.coords.lng },
        });
    };
</script>
