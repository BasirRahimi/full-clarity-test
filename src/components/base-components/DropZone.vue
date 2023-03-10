<template>
    <div @drop.prevent="onDrop">
        <slot></slot>
    </div>
</template>
<script setup>
import { onMounted, onUnmounted } from 'vue';

const emit = defineEmits(['update:modelValue']);

const props = defineProps({
    modelValue: {
        default: null
    }
})

const events = ['dragenter', 'dragover', 'dragleave', 'drop'];

const onDrop = (e) => {
    if(e.dataTransfer.files.length > 1) return;
    
    emit('update:modelValue', e.dataTransfer.files[0]);
}

const preventDefaults = (e) => {
    e.preventDefault();
}

onMounted(() => {
    //Prevent the browser opening the files.
    events.forEach((event) => {
        document.body.addEventListener(event, preventDefaults);
    });
});

onUnmounted(() => {
    events.forEach((event) => {
        document.body.removeEventListener(event, preventDefaults);
    });
});

</script>