<script setup>

import { initializeApp } from 'firebase/app';
import { getDatabase, ref as dbRef, onValue, query, orderByChild, limitToLast } from 'firebase/database';

// Components
import Map from '~/components/Map.vue';
import CorrosionRiskAssessment from '~/components/CorrosionRiskAssessment.vue';
import WaterQualityClassification from '~/components/WaterQualityClassification.vue';

const configs = useRuntimeConfig()

// Firebase configuration
const firebaseConfig = {
    apiKey: configs.public.firebaseApiKey,
    databaseURL: configs.public.firebaseDatabaseUrl,
    projectId: configs.public.firebaseProjectId,
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const database = getDatabase(app);

// State
const activeTab = ref('realtime');
const sensorData = ref([]);
const latestData = ref(null);
const predictions = ref(null);

let unsubscribe; // Declare unsubscribe in a higher scope

// Fetch realtime data
onMounted(() => {
    const sensorRef = dbRef(database, 'sensor_data');
    const recentDataQuery = query(sensorRef, orderByChild('timestamp'), limitToLast(100));

    unsubscribe = onValue(recentDataQuery, (snapshot) => {
        const data = [];
        snapshot.forEach((childSnapshot) => {
            data.push({
                id: childSnapshot.key,
                ...childSnapshot.val()
            });
        });

        // Sort data by timestamp
        data.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));

        sensorData.value = data;
        latestData.value = data[0];
    });

    // Fetch predictions when sensor data is available
    watch(sensorData, async (newData) => {
        if (newData.length > 0) {
            try {
                const response = await fetch('https://water-quality-ml-service.onrender.com/predict');
                const data = await response.json();
                predictions.value = data;
            } catch (error) {
                console.error('Error fetching predictions:', error);
            }
        }
    });
});

// Cleanup
onUnmounted(() => {
    if (unsubscribe) {
        unsubscribe();
    }
});
</script>

<template>
        <!-- Gradient background -->
        <div class="min-h-screen bg-zinc-100 " style="background-image: url('/back2.jpg')">

        
         <!-- Column -->
        <div class="container mx-auto p-6 flex flex-col items-center ">
             <!-- Title -->
            <h1 class="text-5xl md:text-7xl font-semibold mb-8 md:mb-12 text-zinc-800 tracking-tight">
                Water Quality <span class="font-light">Monitoring</span>
            </h1>
             <!-- Navbar-->
             <nav class="bg-white/80 mb-8 rounded-full border-2 border-zinc-300 w-full max-w-5xl mx-auto">
    <div class="flex justify-center space-x-1 p-1 sm:p-2">
        <!-- Real-time-data tab -->
        <button
            class="px-3 sm:px-5 md:px-8 py-2 sm:py-4 text-xs sm:text-sm md:text-base font-medium transition-all duration-300 rounded-full"
            :class="activeTab === 'realtime'
                ? 'text-white bg-zinc-800 m-1'
                : 'text-gray-700 hover:bg-white/30'"
            @click="activeTab = 'realtime'">
            Real-time Data
        </button>
        <!-- Data table tab -->
        <button
            class="px-3 sm:px-5 md:px-8 py-2 sm:py-4 text-xs sm:text-sm md:text-base font-medium transition-all duration-300 rounded-full"
            :class="activeTab === 'table'
                ? 'text-white bg-zinc-800 m-1'
                : 'text-gray-700 hover:bg-white/30'"
            @click="activeTab = 'table'">
            Data Table
        </button>
        <!-- Visualizations tab -->
        <button
            class="px-3 sm:px-5 md:px-8 py-2 sm:py-4 text-xs sm:text-sm md:text-base font-medium transition-all duration-300 rounded-full"
            :class="activeTab === 'visualizations'
                ? 'text-white bg-zinc-800 m-1'
                : 'text-gray-700 hover:bg-white/30'"
            @click="activeTab = 'visualizations'">
            Visualizations
        </button>
        <!-- Corrosion risk tab -->
        <button
            class="px-3 sm:px-5 md:px-8 py-2 sm:py-4 text-xs sm:text-sm md:text-base font-medium transition-all duration-300 rounded-full"
            :class="activeTab === 'corrosion'
                ? 'text-white bg-zinc-800 m-1'
                : 'text-gray-700 hover:bg-white/30'"
            @click="activeTab = 'corrosion'">
            Corrosion Risk
        </button>
        <!-- Quality classification tab -->
        <button
            class="px-3 sm:px-5 md:px-8 py-2 sm:py-4 text-xs sm:text-sm md:text-base font-medium transition-all duration-300 rounded-full"
            :class="activeTab === 'quality'
                ? 'text-white bg-zinc-800 m-1'
                : 'text-gray-700 hover:bg-white/30'"
            @click="activeTab = 'quality'">
            Quality Grade
        </button>
    </div>
</nav>

            <RealtimeData v-if="activeTab === 'realtime'" :latestData="latestData" />
            <Map v-if="activeTab === 'realtime'" />
            <DataTable v-if="activeTab === 'table'" :sensorData="sensorData" :database="database" />
            <DataVisualizations v-if="activeTab === 'visualizations'" :sensorData="sensorData"
                :predictions="predictions" />
            <CorrosionRiskAssessment v-if="activeTab === 'corrosion'" />
            <WaterQualityClassification v-if="activeTab === 'quality'" />

            <Footer class="mt-10" />

        </div>
    </div>
</template>

<style>

</style>