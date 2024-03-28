<template>
    <div>
        <Suspense>
            <AsyncCityview/>
            <template #fallback>
               <CityViewSkeleton />
            </template>
        </Suspense>
    </div>
</template>

<script setup>
    import AsyncCityview from '../components/AsyncCityview.vue';
    import CityViewSkeleton from '../components/CityViewSkeleton.vue';
</script>