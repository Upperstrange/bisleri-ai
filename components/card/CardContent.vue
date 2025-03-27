<script setup>


const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>

<template>
    <div :class="`p-2 md:p-6 ${className}`">
        <slot></slot>
    </div>
</template>
