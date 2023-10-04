<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav 
            class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{name: 'home'}">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">Ο καιρός</p>
                </div>
            </RouterLink>

            <div class="flex gap-3 flex-1 justify-end">
                <i
                    class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="toggeModal">
                </i>
                <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                @click="addCity" v-if="route.query.preview"></i>
            </div>

            <BaseModal :modalActive="modalActive" @close-modal="toggeModal">
                <div class="text-black">
                <h1 class="text-2xl mb-1">Σχετικά:</h1>
                <p class="mb-4">
                    Η εφαρμογή του καιρού σας προσφέρει τη δυνατότητα να 
                    δείτε τον τωρινό και μελλοντικό καιρό σε πόλεις της 
                    επιλογής σας.
                    <!-- The Local Weather allows you to track the current and
                    future weather of cities of your choosing. -->
                </p>
                <h2 class="text-2xl">Πως λειτουργεί:</h2>
                <ol class="list-decimal list-inside mb-4">
                    <li>
                    Ψάξτε για μια πόλη πληκτρολογόντας το όνομά της στη
                    μπάρα αναζήτησης.
                    <!-- Search for your city by entering the name into the
                    search bar. -->
                    </li>
                    <li>
                    Διαλέξτε την πόλη απο το αποτελέσματα και θα
                    μεταφερθείτε στη σελίδα του καιρού για την πόλη
                    της επιλογής σας.
                    <!-- Select a city within the results, this will take
                    you to the current weather for your selection. -->
                    </li>
                    <li>
                    Παρακολουθήστε την πόλη κάνοντας κλικ στο εικονίδιο
                    "+" στην πάνω δεξιά μεριά της οθόνης. Με αυτό τον
                    τρόπο θα αποθηκεύσετε την πόλη ώστε να την δείτε
                    αργότερα απο την κεντρική σελίδα.
                    <!-- Track the city by clicking on the "+" icon in the
                    top right. This will save the city to view at a
                    later time on the home page. -->
                    </li>
                </ol>

                <h2 class="text-2xl">Διαγραφή Πόλης</h2>
                <p>
                    Αν δεν θέλετε να παρακολουθείτε πλέον κάποια πόλη,
                    απλά διαλέξτε την πόλη στην κεντρική σελίδα. Στο 
                    κάτω μέρος της σελίδας υπάρχη επιλογή για διαγραφή
                    της πόλης.
                    <!-- If you no longer wish to track a city, simply select
                    the city within the home page. At the bottom of the
                    page, there will be am option to delete the city. -->
                </p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from 'vue';
import { uid } from 'uid';
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from './BaseModal.vue';

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
    if(localStorage.getItem('savedCities'))
    {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }

    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng
        }
    };

    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query});
};

const modalActive = ref(null);
const toggeModal = () => {
    modalActive.value = !modalActive.value;
}
</script>

