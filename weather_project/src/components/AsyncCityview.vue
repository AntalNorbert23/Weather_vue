<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview"
             class="text-white p-4 bg-weather-secondary w-full text-center"
        >
            <p>
                You are currently previewing this city press "+" to add it to your list.
            </p>
        </div>
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{  new Date(weatherData.currentTime).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{ new Date(weatherData.currentTime).toLocaleTimeString(
                        "en-us",
                        {
                           timeStyle:"short",
                        }
                    )    
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ ((Math.round(weatherData.current.temp)-32)*5/9).toFixed(1) }}°C
            </p>
                <p>
                    Feels like
                    {{ ((Math.round(weatherData.current.feels_like)-32)*5/9).toFixed(1) }}°C
                </p>
                <p class="capitalize">
                    {{ weatherData.current.weather[0].description }}
                </p>
                <img class="w-[150px] h-auto"
                     :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
                      alt="weather icon"
                >
        </div>

        <hr class="border-white border-opacity-10 border w-full">

        <div class="max-w-screen-md w-full py-12">
                <div class="mx-8 text-white">
                    <h2 class="mb-4">Hourly weather</h2>   
                </div>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.hourly" 
                         :key="hourData.dt"
                         class="flex flex-col gap-4 items-center"
                    >
                        <p class="whitespace-nowrap text-md">
                            {{ new Date(hourData.currentTime).toLocaleTimeString(
                                'en-us',{
                                    hour:'numeric',
                                })
                             }}
                        </p>
                        <img class="w-auto h-[50px] object-cover"
                             :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
                              alt="hourly weather"
                        >
                        <p class="text-xl">
                            {{ ((Math.round(hourData.temp)-32)*5/9).toFixed(1) }}°C
                        </p>
                    </div>
                </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full">
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 day forecast</h2>
                <div class="flex items-center"
                     v-for="day in weatherData.daily"
                     :key="day.dt"
                >
                     
                      <p class="flex-1">
                            {{ 
                                new Date(day.dt*1000).toLocaleDateString(
                                    "en-us",
                                    {
                                       weekday:"long", 
                                    }
                                )

                            }}
                      </p>
                      <img class="w-[50px] h-[50px] object-cover"
                           :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
                           alt="weather icon"
                      >
                      <div class="flex gap-2 flex-1 justify-end">
                            <p>H: {{ ((Math.round(day.temp.max)-32)*5/9).toFixed(1) }}°C</p>
                            <p>L: {{ ((Math.round(day.temp.min)-32)*5/9).toFixed(1) }}°C</p>
                      </div>

                </div>
            </div>
        </div>

        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500">
                <i class="fa-solid fa-trash"
                    @click="removeCity"></i>
                <p @click="removeCity">Remove city</p>
        </div>
    </div>
</template>

<script setup>
    import axios from 'axios';
    import {useRoute ,useRouter} from 'vue-router';

    const route=useRoute();

    const getWeatherData=async ()=>{
        try{
            const weatherData= await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`);

            //calculate current date and time 
            const localOffset=new Date().getTimezoneOffset() *60000;
            const utc =weatherData.data.current.dt*1000+localOffset;
            weatherData.data.currentTime= utc+1000*weatherData.data.timezone_offset;

            //hourly weather offset
            weatherData.data.hourly.forEach((hour)=>{
                const utc=hour.dt*1000+localOffset;
                hour.currentTime=utc+1000*weatherData.data.timezone_offset;
            });

        await new Promise((res)=>setTimeout(res,1000))
            return weatherData.data;
        }catch(error){
            console.error(error.message);
        }
    }
    
    const weatherData=await getWeatherData();

    const router=useRouter();
    const removeCity=()=>{
        const cities=JSON.parse(localStorage.getItem("savedCities"));
        const updatedCities=cities.filter((city)=>
            city.id!==route.query.id
        );
    localStorage.setItem('savedCities',JSON.stringify(updatedCities));
    router.push({
        name:"home",
    })
    }
</script>