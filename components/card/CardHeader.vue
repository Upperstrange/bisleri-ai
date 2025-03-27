<script setup>

const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>


<template>
    <div :class="`flex flex-col space-y-1.5 p-6 border-b border-gray-100/20 ${className}`">
        <slot></slot>
    </div>
</template>
