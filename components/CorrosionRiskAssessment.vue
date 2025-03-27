<script setup>

import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';

const riskData = ref(null);
const loading = ref(true);
const error = ref(null);

const riskPercentage = computed(() => riskData.value ? riskData.value.risk_probability * 100 : 0);

const fetchCorrosionRisk = async () => {
    try {
        const response = await fetch('http://localhost:8000/predict-corrosion');
        const data = await response.json();

        if (data.error) {
            throw new Error(data.error);
        }

        riskData.value = data;
        error.value = null;
    } catch (err) {
        error.value = err.message;
    } finally {
        loading.value = false;
    }
};

const getRiskColor = (level) => {
    switch (level) {
        case 'Low':
            return 'text-green-600';
        case 'Medium':
            return 'text-yellow-600';
        case 'High':
            return 'text-red-600';
        default:
            return 'text-gray-600';
    }
};

const getRiskIcon = (level) => {
    switch (level) {
        case 'Low':
            return '✓';
        case 'Medium':
            return '⚠';
        case 'High':
            return '⚡';
        default:
            return null;
    }
};

let interval;

onMounted(() => {
    fetchCorrosionRisk();
    interval = setInterval(fetchCorrosionRisk, 30000);
});

onUnmounted(() => {
    clearInterval(interval);
});
</script>


<template>
    <Card class="mb-8 bg-white/40 border border-black/20 rounded-lg backdrop-blur-sm">
        <CardHeader class="border-b border-gray-100/20 flex items-center">
            <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Current Risk Status</CardTitle>
        </CardHeader>

        <CardContent class="p-2 md:p-6">
            <div v-if="loading" class="flex justify-center items-center min-h-[400px]">
                <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-black"></div>
            </div>

            <div v-else-if="error" class="flex justify-center items-center min-h-[400px]">
                <p class="text-red-600 font-semibold">{{ error }}</p>
            </div>

            <div v-else class="bg-white/10 backdrop-blur-sm p-6 rounded-lg">
                <div class="flex items-center gap-4 mb-6">
                    <span class="text-4xl">{{ getRiskIcon(riskData?.risk_level) }}</span>
                    <div>
                        <h3 class="text-xl font-semibold text-black">Risk Level</h3>
                        <p :class="`text-2xl font-bold ${getRiskColor(riskData?.risk_level)}`">
                            {{ riskData?.risk_level }}
                        </p>
                    </div>
                </div>

                <!-- Risk Scale -->
                <div class="mb-4">
                    <div class="relative w-full h-4 bg-gray-200 rounded-full overflow-hidden">
                        <div class="absolute top-0 left-0 h-full transition-all duration-500 rounded-full" :style="{
                            width: `${riskPercentage}%`,
                            background: `${riskPercentage > 70 ? '#ef4444' :
                                    riskPercentage > 30 ? '#eab308' :
                                        '#22c55e'
                                }`
                        }" />
                    </div>
                    <div class="flex justify-between mt-1">
                        <span class="text-sm text-black">0%</span>
                        <span class="text-sm text-black">50%</span>
                        <span class="text-sm text-black">100%</span>
                    </div>
                </div>

                <p class="text-sm text-black/80 mt-4">
                    Last updated: {{ new Date(riskData?.timestamp).toLocaleString() }}
                </p>
            </div>
        </CardContent>
    </Card>
</template>
