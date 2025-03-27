<script setup>

import { ref as dbRef, get } from 'firebase/database';

import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';

const props = defineProps({
    sensorData: Array,
    database: Object
});

const downloadData = async () => {
    try {
        // Reference to the entire sensor_data node
        const sensorRef = dbRef(props.database, 'sensor_data');

        // Get all data once
        const snapshot = await get(sensorRef);
        const allData = [];

        snapshot.forEach((childSnapshot) => {
            allData.push({
                id: childSnapshot.key,
                ...childSnapshot.val()
            });
        });

        // Sort data by timestamp (newest to oldest)
        allData.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));

        // Convert to CSV format
        const headers = ['Timestamp', 'pH', 'Turbidity (NTU)', 'TDS (ppm)', 'Temperature (°C)', 'Conductivity (μS/cm)'];
        const csvData = allData.map(reading => [
            reading.timestamp,
            reading.ph.toFixed(2),
            reading.turbidity.toFixed(2),
            reading.tds.toFixed(2),
            reading.temperature.toFixed(2),
            reading.conductivity.toFixed(2)
        ]);

        // Create CSV content
        const csvContent = [
            headers.join(','),
            ...csvData.map(row => row.join(','))
        ].join('\n');

        // Create and trigger download
        const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement('a');
        const url = URL.createObjectURL(blob);
        link.setAttribute('href', url);
        link.setAttribute('download', 'complete_water_quality_data.csv');
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    } catch (error) {
        console.error('Error downloading data:', error);
        alert('Error downloading data. Please try again.');
    }
};
</script>


<template>
    <div class="mt-6 w-[90%] max-w-5xl mx-auto bg-white/20 rounded-2xl">
        <div class=" rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 border border-gray-100 px-4 pb-4">

            <CardHeader>
                <div class="flex rounded-xl border border-zinc-300 p-4 justify-between bg-white/40">
                    <CardTitle>Historical Data</CardTitle>
                    <button @click="downloadData"
                        class="px-4 py-2 bg-zinc-800 text-white font-bold rounded-md hover:bg-zinc-900 hover:scale-101 transition-colors">
                        Download Data
                    </button>
                </div>
            </CardHeader>
            <div class="overflow-x-auto ">
                <table
                    class="min-w-full border-separate border-spacing-0 rounded-xl shadow-sm overflow-hidden bg-white">
                    <thead>
                        <tr class="text-gray-700">
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-gray-100">Timestamp</th>
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-blue-50">pH</th>
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-orange-50">Turbidity (NTU)
                            </th>
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-yellow-50">TDS (ppm)</th>
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-green-50">Temperature (°C)
                            </th>
                            <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-purple-50">Conductivity
                                (μS/cm)
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="reading in sensorData" :key="reading.id"
                            class="border-t border-gray-200 hover:bg-gray-50 transition-colors">
                            <td class="p-4 text-gray-600 bg-gray-50/50 text-center">{{ reading.timestamp }}</td>
                            <td class="p-4 text-blue-600 bg-blue-50/50 text-center">{{ reading.ph.toFixed(2) }}</td>
                            <td class="p-4 text-orange-600 bg-orange-50/50 text-center">{{ reading.turbidity.toFixed(2)
                                }}
                            </td>
                            <td class="p-4 text-yellow-600 bg-yellow-50/50 text-center">{{ reading.tds.toFixed(2) }}
                            </td>
                            <td class="p-4 text-green-600 bg-green-50/50 text-center">{{ reading.temperature.toFixed(2)
                                }}
                            </td>
                            <td class="p-4 text-purple-600 bg-purple-50/50 text-center">{{
                                reading.conductivity.toFixed(2)
                                }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

        </div>
    </div>
    <!-- <Card class="w-[80%]">
        <CardHeader>
            <div class="flex rounded-xl border border-zinc-300 p-4 justify-between">
                <CardTitle>Historical Data</CardTitle>
                <button @click="downloadData"
                    class="px-4 py-2 bg-blue-500 text-white font-bold rounded-md hover:bg-blue-600 hover:scale-101 transition-colors">
                    Download Data
                </button>
            </div>
        </CardHeader>
        <CardContent class="overflow-x-auto items-center flex flex-col">

            <table class="min-w-full border-separate border-spacing-0 rounded-xl shadow-sm overflow-hidden bg-white">
                <thead>
                    <tr class="text-gray-700">
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-gray-100">Timestamp</th>
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-blue-50">pH</th>
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-orange-50">Turbidity (NTU)</th>
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-yellow-50">TDS (ppm)</th>
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-green-50">Temperature (°C)</th>
                        <th class="p-4 font-semibold text-sm uppercase tracking-wide bg-purple-50">Conductivity (μS/cm)
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="reading in sensorData" :key="reading.id"
                        class="border-t border-gray-200 hover:bg-gray-50 transition-colors">
                        <td class="p-4 text-gray-600 bg-gray-50/50 text-center">{{ reading.timestamp }}</td>
                        <td class="p-4 text-blue-600 bg-blue-50/50 text-center">{{ reading.ph.toFixed(2) }}</td>
                        <td class="p-4 text-orange-600 bg-orange-50/50 text-center">{{ reading.turbidity.toFixed(2) }}
                        </td>
                        <td class="p-4 text-yellow-600 bg-yellow-50/50 text-center">{{ reading.tds.toFixed(2) }}</td>
                        <td class="p-4 text-green-600 bg-green-50/50 text-center">{{ reading.temperature.toFixed(2) }}
                        </td>
                        <td class="p-4 text-purple-600 bg-purple-50/50 text-center">{{ reading.conductivity.toFixed(2)
                        }}</td>
                    </tr>
                </tbody>
            </table>
      

        </CardContent>
    </Card> -->
</template>
