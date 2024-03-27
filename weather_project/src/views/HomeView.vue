
<template>
  <main class="container text-white">
      <div class="pt-4 mb-8 relative">
          <input type="text" 
                 placeholder="Search for a city or state"
                 class="py-2 px-1 w-full bg-transparent border-b
                      focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
                 v-model="search"
                 @input="getSearchResults"           
          >
          <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
              v-if="mapboxSearchResults"
          >
          <p v-if="searchError">Sorry something went wrong! Please try again.</p>
          <p v-if="!searchError && mapboxSearchResults.length=== 0">No results! Try a different term. </p>
              <template v-else>
                <li v-for="searchResult in mapboxSearchResults"
                    :key="searchResult.id"
                    class="py-2 cursor-pointer"
                    @click="previewCity(searchResult)"
                >
                  {{ searchResult.place_name }}
                </li>
              </template>
          </ul>
      </div>
      <div class="flex flex-column gap-4"></div>
      <Suspense>
          <CityList/>
          <template #fallback>
              <p>Loading...</p>
          </template>
      </Suspense>
  </main>
</template>

<script setup>
    import { ref } from 'vue';
    import axios from 'axios';
    import { useRouter } from 'vue-router';
    import CityList from "../components/CityList.vue";

    const mapboxAPIKey="pk.eyJ1Ijoibm9yYmVydGNzYWJhIiwiYSI6ImNsdTVoMDNxbjFxdWYya3FzYjc5ZmVyM3oifQ.63QI_M3CQfKDn6sAfduEBA";
    const search=ref("");
    const searchTimeout=ref(null);
    const mapboxSearchResults=ref(null);
    const searchError=ref(null);

    const getSearchResults=()=>{
        clearTimeout(searchTimeout.value);
        searchTimeout.value=setTimeout(async()=>{
            if(search.value !== ""){
              try{
                const result= await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${mapboxAPIKey}&types=place`);
                mapboxSearchResults.value=result.data.features;
                return;
              }catch(error){
                  searchError.value=true;
                  console.error(error.message);
              }
              }
            mapboxSearchResults.value=null;
        },300);
    }
    const router=useRouter();

    const previewCity=(searchResult)=>{
        const [city,state]=searchResult.place_name.split(",");
        router.push({
            name:"cityView",
            params:{state:state.replaceAll(" ",""),city:city},
            query:{
              lat:searchResult.geometry.coordinates[1],
              lng:searchResult.geometry.coordinates[0],
              preview:true,
            }
        })
    }

</script>
