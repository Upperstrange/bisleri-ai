<script setup>


const props = defineProps({
    className: {
        type: String,
        default: ''
    }
});
</script>


<template>
    <div
        :class="`rounded-2xl bg-zinc-50 shadow-sm transition-all duration-300 border-1 border-zinc-300 hover:shadow-md ${className}`">
        <slot/>
    </div>
</template>
