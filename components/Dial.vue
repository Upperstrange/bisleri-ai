<script setup>

const props = defineProps({
    value: Number,
    min: Number,
    max: Number,
    optimal: Number,
    unit: String,
    title: String,
    getStatus: Function
});

const percentage = computed(() => ((props.value - props.min) / (props.max - props.min)) * 100);
const rotation = computed(() => (percentage.value * 2.2) - 110);
const status = computed(() => props.getStatus(props.value));

// Calculate color segments based on the parameter ranges
const getSegmentGradient = () => {
    switch (props.title) {
        case "pH Level":
            return `conic-gradient(
        from 180deg,
        rgba(239, 68, 68, 0.8) 20%, /* Red */
        rgba(34, 197, 94, 0.8) 55%, /* Green */
        rgba(168, 85, 247, 0.8) 80% /* Purple */
      )`;
        case "Turbidity":
            return `conic-gradient(
        from 180deg,
        rgba(34, 197, 94, 0.8) 20%, /* Green */
        rgba(59, 130, 246, 0.8) 40%, /* Blue */
        rgba(239, 68, 68, 0.8) 50% /* Red */
      )`;
        case "TDS":
            return `conic-gradient(
        from 180deg,
        rgba(34, 197, 94, 0.8) 20%, /* Green */
        rgba(59, 130, 246, 0.8) 55%, /* Blue */
        rgba(239, 68, 68, 0.8) 80% /* Red */
      )`;
        case "Temperature":
            return `conic-gradient(
        from 180deg,
        rgba(59, 130, 246, 0.8) 20%, /* Blue */
        rgba(255, 255, 103, 0.8) 55%, /* Yellow */
        rgba(239, 68, 68, 0.8) 80% /* Red */
      )`;
        case "Conductivity":
            return `conic-gradient(
        from 180deg,
        rgba(59, 130, 246, 0.8) 20%, /* Blue */
        rgba(34, 197, 94, 0.8) 55%, /* Green */
        rgba(239, 68, 68, 0.8) 80% /* Red */
      )`;
        default:
            return `conic-gradient(
        from 180deg,
        gray 0%,
        gray 100%
      )`;
    }
};
</script>

<template>
    <div
        class="flex flex-col items-center p-4 border-1 border-zinc-300 rounded-2xl shadow-lg transition-all duration-300 hover:shadow-xl hover:bg-blue-100/40 hover:scale-101 ">
        <h3 class="text-xl font-medium mb-4 text-gray-800">{{ title }}</h3>

        <!-- Dial Container -->
        <div class="relative w-48 h-32 mb-4">
            <!-- Dial Ring -->
            <div class="absolute w-48 h-48 rounded-full top-0 overflow-hidden" :style="{
                background: 'transparent',
                border: 'none',
                WebkitMask: 'radial-gradient(transparent 55%, black 55%)',
                mask: 'radial-gradient(transparent 55%, black 55%)',
                clipPath: 'polygon(0 0.1%, 100% 0.1%, 100% 70%, 0 70%)',
                transform: 'rotate(0deg)',
            }">
                <div class="w-full h-full" :style="{
                    background: getSegmentGradient(),
                }" />
            </div>

            <!-- Pointer -->
            <div class="absolute left-1/2 w-1 h-24 bg-gray-800 origin-bottom bottom-8 transition-transform duration-700 ease-in-out"
                :style="{
                    transform: `translateX(-50%) rotate(${rotation}deg)`,
                }" />
        </div>

        <!-- Value Display -->
        <div class="text-center">
            <p class="text-3xl font-light mb-2 text-gray-800">
                {{ value.toFixed(2) }} <span class="text-lg font-light">{{ unit }}</span>
            </p>
            <p :class="`text-sm font-medium ${status.color} px-3 py-1 rounded-full bg-white/50 inline-block border border-zinc-300`">
                {{ status.status }}
            </p>
        </div>
    </div>
</template>
