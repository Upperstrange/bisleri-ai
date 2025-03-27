<script setup>

const props = defineProps({
    latestData: Object
});

const currentDateTime = ref(new Date());

const formattedDate = computed(() => {
    return currentDateTime.value.toLocaleDateString('en-US', {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
    });
});

const formattedTime = computed(() => {
    return currentDateTime.value.toLocaleTimeString('en-US', {
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
    });
});

const formattedLastUpdated = computed(() => {
    if (!props.latestData) return '';

    const lastUpdatedTime = new Date(props.latestData.timestamp).toLocaleTimeString('en-US', {
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
    });

    const lastUpdatedDate = new Date(props.latestData.timestamp).toLocaleDateString('en-US', {
        month: 'short',
        day: 'numeric',
        year: 'numeric'
    });

    return `${lastUpdatedTime}, ${lastUpdatedDate}`;
});

let timer;

onMounted(() => {
    timer = setInterval(() => {
        currentDateTime.value = new Date();
    }, 1000);
});

onUnmounted(() => {
    clearInterval(timer);
});
</script>


<!-- <template>
    <ClientOnly>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
            <div class="p-6 flex flex-col items-center rounded-2xl border-1 border-zinc-300 shadow-md transition-all duration-300 hover:shadow-lg">
                <h3 class="text-xl font-medium mb-2 text-zinc-700">Date</h3>
                <p class="text-2xl font-light text-zinc-800">
                    {{ formattedDate }}
                </p>
            </div>
            <div class="p-6 flex flex-col items-center rounded-2xl border-1 border-zinc-300 shadow-md transition-all duration-300 hover:shadow-lg">
                <h3 class="text-xl font-medium mb-2 text-zinc-700">Time</h3>
                <p class="text-2xl font-light text-zinc-800">
                    {{ formattedTime }}
                </p>
            </div>
            <div class="p-6 flex flex-col items-center rounded-2xl border-1 border-zinc-300 shadow-md transition-all duration-300 hover:shadow-lg">
                <h3 class="text-xl font-medium mb-2 text-zinc-700">Last Updated</h3>
                <p class="text-2xl font-light text-zinc-800">
                    <template v-if="latestData">
                        {{ formattedLastUpdated }}
                    </template>
                    <template v-else>No data</template>
                </p>
            </div>
        </div>
    </ClientOnly>
</template> -->
<template>
    <ClientOnly>
        <div class="grid grid-cols-1 gap-4">
            <div class="bg-white/40 rounded-2xl p-4 shadow-md hover:shadow-lg transition-shadow flex flex-col items-center">
                <h3 class="text-lg font-medium text-gray-700">Date</h3>
                <p class="text-xl font-light text-gray-800">{{ formattedDate }}</p>
            </div>
            <div class="bg-white/40 rounded-2xl p-4 shadow-md hover:shadow-lg transition-shadow flex flex-col items-center">
                <h3 class="text-lg font-medium text-gray-700">Time</h3>
                <p class="text-xl font-light text-gray-800">{{ formattedTime }}</p>
            </div>
            <div class="bg-white/40 rounded-2xl p-4 shadow-md hover:shadow-lg transition-shadow flex flex-col items-center">
                <h3 class="text-lg font-medium text-gray-700">Last Updated</h3>
                <p class="text-xl font-light text-gray-800">
                    <template v-if="latestData">{{ formattedLastUpdated }}</template>
                    <template v-else>No data</template>
                </p>
            </div>
        </div>
    </ClientOnly>
</template>