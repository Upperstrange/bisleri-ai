<script setup>


const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>

<template>
    <p :class="`text-sm text-gray-500 font-normal ${className}`">
        <slot></slot>
    </p>
</template>
