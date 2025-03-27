<script setup>


const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>

<template>
    <h3 :class="`text-3xl font-bold leading-tight tracking-tight text-zinc-600 ${className}`">
        <slot></slot>
    </h3>
</template>
