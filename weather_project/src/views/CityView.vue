<template>
    <div>
        <Suspense>
            <AsyncCityview/>
            <template #fallback>
                <p>Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
    import AsyncCityview from '../components/AsyncCityview.vue';
</script>