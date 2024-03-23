<template>
    <div class="absolute w-full bg-black bg-opacity-30 h-screen top-0 left-0 flex justify-center px-8"
         v-show="modalActive"
    >

        <div class="p-4 bg-white self-start mt-32 max-w-screen-md"
             v-if="modalActive"
        >
            <slot />
            <button class="text-white mt-8 bg-weather-secondary py-2 px-6" 
                    @click="$emit('close-modal')"
            >
                Close
            </button>
        </div>
    </div>
</template>

<script setup>
    import {defineProps, defineEmits } from 'vue';
    
    defineEmits(['close-modal'])
    defineProps({
        modalActive:{
            type:Boolean,
            default:false,
        }
    })
</script>