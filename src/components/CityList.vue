<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"></CityCard>
    </div>
    <p v-if="savedCities.length === 0">
        Δεν έχουν προστεθεί τοποθεσίες. Για να παρακολουθείτε μια τοποθεσία,
        ψάξτε στο απο πάνω πεδίο.
    </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";

const savedCities = ref([]);

const getCities = async () => {
    if(localStorage.getItem('savedCities'))
    {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }

    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
        )
      );
    });

    const weatherData = await Promise.all(requests);

    // Flicker Delay
    await new Promise((res) => setTimeout(res,1000));

    weatherData.forEach((value,index) => {
        savedCities.value[index].weather = value.data;
    });
};

await getCities();

const router = useRouter();
const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: { state: city.state, city: city.city },
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng,
        }
    });
};
</script>