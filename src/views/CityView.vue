<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <p>Φόρτωση...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
</script>