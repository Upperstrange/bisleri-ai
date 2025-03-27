<script setup>

import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';

const qualityData = ref(null);
const loading = ref(true);
const error = ref(null);

const fetchQualityData = async () => {
    try {
        const response = await fetch('http://localhost:8000/predict-quality');
        const data = await response.json();

        if (data.error) {
            throw new Error(data.error);
        }

        qualityData.value = data;
        error.value = null;
    } catch (err) {
        error.value = err.message;
    } finally {
        loading.value = false;
    }
};

onMounted(() => {
    fetchQualityData();
    const interval = setInterval(fetchQualityData, 30000);
    return () => clearInterval(interval);
});

const getGradeColor = (grade) => {
    switch (grade) {
        case 'A':
            return 'text-green-600';
        case 'B':
            return 'text-blue-600';
        case 'C':
            return 'text-yellow-600';
        case 'D':
            return 'text-red-600';
        default:
            return 'text-gray-600';
    }
};

const getGradeDescription = (grade) => {
    switch (grade) {
        case 'A':
            return 'Excellent Quality - Meets all quality standards with optimal parameters';
        case 'B':
            return 'Good Quality - Parameters within acceptable ranges';
        case 'C':
            return 'Fair Quality - Some parameters need attention';
        case 'D':
            return 'Poor Quality - Immediate attention required';
        default:
            return 'Unknown Quality';
    }
};
</script>


<template>
    <div class="space-y-8">
        <Card>
            <CardHeader class="border-b border-gray-100/20 flex items-center">
                <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Water Quality Grade
                </CardTitle>
            </CardHeader>

            <CardContent>
                <div class="flex items-center justify-between mb-6">
                    <div>
                        <h3 class="text-2xl font-bold mb-2 text-zinc-800">Current Grade</h3>
                        <p class="text-6xl font-bold" :class="getGradeColor(qualityData?.grade)">
                            {{ qualityData?.grade }}
                        </p>
                    </div>
                    <div class="text-right">
                        <p class="text-lg text-black/80">
                            {{ getGradeDescription(qualityData?.grade) }}
                        </p>
                        <p class="text-sm text-black/60 mt-2">
                            Last updated: {{ new Date(qualityData?.timestamp).toLocaleString() }}
                        </p>
                    </div>
                </div>

                <div class="space-y-4">
                    <h4 class="text-lg font-semibold text-zinc-800">Grade Probabilities</h4>
                    <div class="grid grid-cols-4 gap-4">
                        <div v-for="(probability, grade) in qualityData?.grade_probabilities" :key="grade"
                            class="bg-white/10 backdrop-blur-sm rounded-lg p-4">
                            <div :class="['text-2xl font-bold', getGradeColor(grade), 'mb-2']">
                                {{ grade }}
                            </div>
                            <div class="text-lg">
                                {{ (probability * 100).toFixed(1) }}%
                            </div>
                        </div>
                    </div>
                </div>
            </CardContent>
        </Card>

        <Card>
            <CardHeader class="border-b border-gray-100/20 flex items-center">
                <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Model Information
                </CardTitle>
            </CardHeader>

            <CardContent>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div class="bg-white/10 backdrop-blur-sm rounded-lg p-6">
                        <h3 class="text-lg font-semibold text-black mb-4">Random Forest Architecture</h3>
                        <div class="flex flex-col items-center space-y-4">
                            <div class="grid grid-cols-3 gap-4 w-full">
                                <div v-for="tree in [1, 2, 3]" :key="tree" class="relative">
                                    <div
                                        class="w-full aspect-square bg-green-100 rounded-lg border-2 border-green-500 flex items-center justify-center p-2">
                                        <div class="text-xs text-center text-black">
                                            Decision<br />Tree {{ tree }}
                                        </div>
                                    </div>
                                    <div class="absolute -bottom-4 left-1/2 transform -translate-x-1/2">
                                        <div class="w-0.5 h-4 bg-green-500"></div>
                                    </div>
                                </div>
                            </div>
                            <div class="w-full bg-blue-100 rounded-lg border-2 border-blue-500 p-4 mt-6">
                                <p class="text-xs text-center text-black">
                                    Majority Voting System<br />
                                    (100 Trees Total)
                                </p>
                            </div>
                        </div>
                    </div>

                    <div class="bg-white/10 backdrop-blur-sm rounded-lg p-6">
                        <h3 class="text-lg font-semibold text-black mb-4">Classification Process</h3>
                        <div class="flex flex-col justify-center min-h-[200px]">
                            <div class="flex items-center space-x-4">
                                <div
                                    class="w-20 h-20 bg-indigo-100 rounded-full flex items-center justify-center border-2 border-indigo-500">
                                    <span class="text-sm text-black text-center">Input<br />Parameters</span>
                                </div>
                                <div class="flex-1 h-0.5 bg-indigo-500"></div>
                                <div
                                    class="w-20 h-20 bg-purple-100 rounded-full flex items-center justify-center border-2 border-purple-500">
                                    <span class="text-sm text-black text-center">Feature<br />Analysis</span>
                                </div>
                                <div class="flex-1 h-0.5 bg-purple-500"></div>
                                <div
                                    class="w-20 h-20 bg-green-100 rounded-full flex items-center justify-center border-2 border-green-500">
                                    <span class="text-sm text-black text-center">Grade<br />Decision</span>
                                </div>
                            </div>
                            <div class="text-xs text-black/80 text-center mt-4">
                                Real-time classification every 30 seconds
                            </div>
                        </div>
                    </div>

                    <div class="bg-white/10 backdrop-blur-sm rounded-lg p-6">
                        <h3 class="text-lg font-semibold text-black mb-4">Feature Importance</h3>
                        <div class="space-y-3">
                            <div v-for="(importance, feature) in qualityData?.feature_importance" :key="feature"
                                class="relative">
                                <div class="flex items-center justify-between mb-1">
                                    <span class="text-sm font-medium text-black">{{ feature }}</span>
                                    <span class="text-sm text-black">{{ (importance * 100).toFixed(1) }}%</span>
                                </div>
                                <div class="w-full h-3 bg-gray-200 rounded-full overflow-hidden">
                                    <div class="h-full bg-blue-600 rounded-full"
                                        :style="{ width: `${importance * 100}%` }"></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="bg-white/10 backdrop-blur-sm rounded-lg p-6">
                        <h3 class="text-lg font-semibold text-black mb-4">Model Performance</h3>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="bg-white/20 rounded-lg p-4">
                                <h4 class="text-sm font-semibold text-black mb-2">Training Data</h4>
                                <div class="space-y-2">
                                    <div class="flex justify-between">
                                        <span class="text-xs text-black">Samples:</span>
                                        <span class="text-xs text-black">15,000+</span>
                                    </div>
                                    <div class="flex justify-between">
                                        <span class="text-xs text-black">Features:</span>
                                        <span class="text-xs text-black">5</span>
                                    </div>
                                </div>
                            </div>
                            <div class="bg-white/20 rounded-lg p-4">
                                <h4 class="text-sm font-semibold text-black mb-2">Model Details</h4>
                                <div class="space-y-2">
                                    <div class="flex justify-between">
                                        <span class="text-xs text-black">Trees:</span>
                                        <span class="text-xs text-black">100</span>
                                    </div>
                                    <div class="flex justify-between">
                                        <span class="text-xs text-black">Max Depth:</span>
                                        <span class="text-xs text-black">10</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </CardContent>
        </Card>

        <Card>
            <CardHeader class="border-b border-gray-100/20 flex items-center">
                <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Grade Criteria</CardTitle>
            </CardHeader>

            <CardContent>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div class="space-y-4">
                        <div class="bg-white/10 backdrop-blur-sm rounded-lg p-4">
                            <h3 class="text-xl font-bold text-green-600 mb-2">Grade A - Excellent</h3>
                            <ul class="list-disc list-inside space-y-1 text-sm text-zinc-800">
                                <li>pH: 6.8 - 7.5</li>
                                <li>Turbidity: {'<'} 1.0 NTU</li>
                                <li>TDS: {'<'} 300 ppm</li>
                                <li>Temperature: 25 - 28°C</li>
                                <li>Conductivity: {'<'} 500 μS/cm</li>
                            </ul>
                        </div>
                        <div class="bg-white/10 backdrop-blur-sm rounded-lg p-4">
                            <h3 class="text-xl font-bold text-blue-600 mb-2">Grade B - Good</h3>
                            <ul class="list-disc list-inside space-y-1 text-sm text-zinc-800">
                                <li>pH: 6.5 - 8.0</li>
                                <li>Turbidity: {'<'} 3.0 NTU</li>
                                <li>TDS: {'<'} 500 ppm</li>
                                <li>Temperature: 20 - 30°C</li>
                                <li>Conductivity: {'<'} 700 μS/cm</li>
                            </ul>
                        </div>
                    </div>
                    <div class="space-y-4">
                        <div class="bg-white/10 backdrop-blur-sm rounded-lg p-4">
                            <h3 class="text-xl font-bold text-yellow-600 mb-2">Grade C - Fair</h3>
                            <ul class="list-disc list-inside space-y-1 text-sm text-zinc-800">
                                <li>pH: 6.0 - 8.5</li>
                                <li>Turbidity: {'<'} 5.0 NTU</li>
                                <li>TDS: {'<'} 800 ppm</li>
                                <li>Temperature: 15 - 35°C</li>
                                <li>Conductivity: {'<'} 1000 μS/cm</li>
                            </ul>
                        </div>
                        <div class="bg-white/10 backdrop-blur-sm rounded-lg p-4">
                            <h3 class="text-xl font-bold text-red-600 mb-2">Grade D - Poor</h3>
                            <ul class="list-disc list-inside space-y-1 text-sm text-zinc-800">
                                <li>pH: Outside acceptable range</li>
                                <li>Turbidity: {'>='} 5.0 NTU</li>
                                <li>TDS: {'>='} 800 ppm</li>
                                <li>Temperature: Outside acceptable range</li>
                                <li>Conductivity: {'>='} 1000 μS/cm</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </CardContent>
        </Card>
    </div>
</template>
