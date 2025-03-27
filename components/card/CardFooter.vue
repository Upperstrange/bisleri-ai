<script setup>
const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>

<template>
    <div :class="`flex items-center p-6 pt-0 ${className}`">
        <slot></slot>
    </div>
</template>
