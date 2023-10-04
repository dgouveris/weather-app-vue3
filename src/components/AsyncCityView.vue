<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>Βρίσκεστε στην προεπισκόπηση πόλης, κάντε κλικ στο εικονίδιο "+" για να 
                ξεκινήσετε να παρακολουθείτε αυτή την πόλη.</p>
        </div>
        <!-- Weather overview -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{ 
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "el-GR",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long"
                        }
                    )
                }}
                {{ 
                    new Date(weatherData.currentTime).toLocaleTimeString(
                        "el-GR",
                        {
                            timeStyle: "short"
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ tempCelcius  }}&deg;
            </p>
            <p>
                Αίσθηση
                {{ tempFeelsLikeCelcius  }}&deg;
            </p>
            <p class="capitalize"> 
                {{ weatherData.current.weather[0].description  }}
            </p>
            <img
                class="w-[150px] h-auto"
                :src="
                `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
                "
                alt=""
            />
        </div>

        <hr class="border-white border-opacity-10 border w-full" />

        <!-- Hourly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Ωριαίος Καιρός</h2>
                <div class="flex gap-10 overflow-x-scroll"></div>
            </div>
        </div>
    </div>
</template>

<script setup>
import axios from "axios";
import { useRoute } from "vue-router";

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial&lang=el`
        );

        // cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;

        // cal hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
        const utc = hour.dt * 1000 + localOffset;
        hour.currentTime =
            utc + 1000 * weatherData.data.timezone_offset;
        });

        return weatherData.data;

    } catch (err) {
        console.log(err);
    }

};
const weatherData = await getWeatherData();
console.log(weatherData);
const tempCelcius = (((Math.round(weatherData.current.temp)-32)*5)/9).toFixed(0);
const tempFeelsLikeCelcius = (((Math.round(weatherData.current.temp)-32)*5)/9).toFixed(0);
</script>