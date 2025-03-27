<!-- DataVisualizations.vue -->
<script setup>
import { Line } from 'vue-chartjs';
import { Chart as ChartJS, LineController, LineElement, PointElement, LinearScale, CategoryScale, Tooltip, Legend } from 'chart.js';
import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';
import { ref, computed, onMounted, watch } from 'vue';

// Register Chart.js components
ChartJS.register(LineController, LineElement, PointElement, LinearScale, CategoryScale, Tooltip, Legend);

const props = defineProps({
  sensorData: { type: Array, default: () => [] },
  predictions: { type: Object, default: () => null }
});

// Clean predictions (adapted from React)
const cleanPredictions = computed(() => {
  if (!props.predictions) return null;
  return {
    ...props.predictions,
    predictions: Object.fromEntries(
      Object.entries(props.predictions.predictions).map(([key, values]) => [
        key,
        values.map(v => (v === null ? undefined : v))
      ])
    )
  };
});

// Real and predicted data
const realData = computed(() => {
  return props.sensorData.sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp));
});

const predictedData = computed(() => {
  if (!cleanPredictions.value || !cleanPredictions.value.timestamps) return [];
  return cleanPredictions.value.timestamps
    .map((timestamp, i) => ({
      timestamp,
      ph: cleanPredictions.value.predictions.ph[i],
      temperature: cleanPredictions.value.predictions.temperature[i],
      tds: cleanPredictions.value.predictions.tds[i],
      turbidity: cleanPredictions.value.predictions.turbidity[i],
      conductivity: cleanPredictions.value.predictions.conductivity[i]
    }))
    .sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp));
});

// Chart data
const phRealChartData = computed(() => ({
  labels: realData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'pH', data: realData.value.map(d => d.ph), borderColor: '#4338ca', borderWidth: 3, fill: false, tension: 0.4 }]
}));

const phPredictedChartData = computed(() => ({
  labels: predictedData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'pH Prediction', data: predictedData.value.map(d => d.ph), borderColor: '#ff0000', borderWidth: 2, fill: false, tension: 0.4 }]
}));

const temperatureRealChartData = computed(() => ({
  labels: realData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Temperature', data: realData.value.map(d => d.temperature), borderColor: '#15803d', borderWidth: 3, fill: false, tension: 0.4 }]
}));

const temperaturePredictedChartData = computed(() => ({
  labels: predictedData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Temperature Prediction', data: predictedData.value.map(d => d.temperature), borderColor: '#ff0000', borderWidth: 2, fill: false, tension: 0.4 }]
}));

const tdsRealChartData = computed(() => ({
  labels: realData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'TDS', data: realData.value.map(d => d.tds), borderColor: '#ffc658', borderWidth: 3, fill: false, tension: 0.4 }]
}));

const tdsPredictedChartData = computed(() => ({
  labels: predictedData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'TDS Prediction', data: predictedData.value.map(d => d.tds), borderColor: '#ff0000', borderWidth: 2, fill: false, tension: 0.4 }]
}));

const turbidityRealChartData = computed(() => ({
  labels: realData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Turbidity', data: realData.value.map(d => d.turbidity), borderColor: '#ff7300', borderWidth: 3, fill: false, tension: 0.4 }]
}));

const turbidityPredictedChartData = computed(() => ({
  labels: predictedData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Turbidity Prediction', data: predictedData.value.map(d => d.turbidity), borderColor: '#ff0000', borderWidth: 2, fill: false, tension: 0.4 }]
}));

const conductivityRealChartData = computed(() => ({
  labels: realData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Conductivity', data: realData.value.map(d => d.conductivity), borderColor: '#9c27b0', borderWidth: 3, fill: false, tension: 0.4 }]
}));

const conductivityPredictedChartData = computed(() => ({
  labels: predictedData.value.map(d => new Date(d.timestamp).toLocaleTimeString()),
  datasets: [{ label: 'Conductivity Prediction', data: predictedData.value.map(d => d.conductivity), borderColor: '#ff0000', borderWidth: 2, fill: false, tension: 0.4 }]
}));

// Chart options
const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  layout: {
    padding: { left: 10, right: 10, top: 10, bottom: 10 }
  },
  scales: {
    x: {
      grid: { display: false },
      ticks: {
        maxRotation: 45,
        minRotation: 45,
        maxTicksLimit: 8,
        font: { size: 12, family: "'Inter', sans-serif" },
        color: '#000000',
      },
      border: { color: '#000000' },
    },
    y: {
      grid: { color: '#000000', borderDash: [3, 3] },
      ticks: {
        color: '#000000',
        font: { size: 12, family: "'Inter', sans-serif" },
      },
      border: { color: '#000000' },
    },
  },
  plugins: {
    tooltip: {
      backgroundColor: 'rgba(255, 255, 255, 0.8)',
      titleColor: '#000000',
      bodyColor: '#000000',
      borderRadius: 8,
      padding: 10,
      titleFont: { size: 14, family: "'Inter', sans-serif", weight: '600' },
      bodyFont: { size: 12, family: "'Inter', sans-serif" },
    },
    legend: {
      position: 'top',
      labels: {
        color: '#000000',
        font: { size: 13, family: "'Inter', sans-serif", weight: '500' },
        padding: 15,
        usePointStyle: true,
      },
    },
  },
  animation: { duration: 1000, easing: 'easeOutCubic' },
  elements: {
    line: { tension: 0.4, borderWidth: 2 },
    point: { radius: 4, hoverRadius: 6, backgroundColor: 'rgba(0, 0, 0, 0.8)', borderColor: '#ffffff', borderWidth: 1 },
  },
});

// Watch for data changes
watch(
  () => [realData.value, predictedData.value],
  () => {
    chartOptions.value = { ...chartOptions.value };
  },
  { deep: true }
);

onMounted(() => {
  chartOptions.value = { ...chartOptions.value };
});
</script>

<template>


  <div class="mt-6 w-[90%] mx-auto bg-white/40 rounded-2xl">
    <div class=" rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 border border-gray-100 p-8">

      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <!-- Column Headers -->
        <h2
          class="hidden md:flex md:justify-center md:rounded-xl md:p-4 md:border-2 md:border-green-500 md:bg-green-500/20 text-xl font-semibold text-zinc-900 ">
          Live Metrics</h2>
        <h2
          class="hidden md:flex md:justify-center md:rounded-xl md:p-4 md:border-2 md:border-orange-500 md:bg-orange-500/20 text-xl font-semibold text-zinc-900 ">
          Future Predictions</h2>

        <!-- pH Charts -->
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">pH Trends</h3> -->
          <Line :data="phRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">pH Predictions</h3> -->
          <Line :data="phPredictedChartData" :options="chartOptions" />
        </div>

        <!-- Temperature Charts -->
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">Temperature Trends</h3> -->
          <Line :data="temperatureRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">Temperature Predictions</h3> -->
          <Line :data="temperaturePredictedChartData" :options="chartOptions" />
        </div>

        <!-- TDS Charts -->
        <div class="h-[250px] md:h-[300px]">
          <!-- <h3 class="text-lg font-semibold mb-4">TDS Trends</h3> -->
          <Line :data="tdsRealChartData" :options="chartOptions" />
        </div>

        <div class="h-[250px] md:h-[300px]">
          <!-- <h3 class="text-lg font-semibold mb-4">TDS Predictions</h3> -->
          <Line :data="tdsPredictedChartData" :options="chartOptions" />
        </div>

        <!-- Turbidity Charts -->
        <div class="h-[250px] md:h-[300px]">
          <!-- <h3 class="text-lg font-semibold mb-4">Turbidity Trends</h3> -->
          <Line :data="turbidityRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">Turbidity Predictions</h3> -->
          <Line :data="turbidityPredictedChartData" :options="chartOptions" />
        </div>

        <!-- Conductivity Charts -->
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">Conductivity Trends</h3> -->
          <Line :data="conductivityRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <!-- <h3 class="text-lg font-semibold mb-4">Conductivity Predictions</h3> -->
          <Line :data="conductivityPredictedChartData" :options="chartOptions" />
        </div>
      </div>

    </div>
  </div>









  <!-- <Card class="w-[94%] overflow-hidden">
    <CardHeader class="border-b border-gray-100/20 flex items-center">
      <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Environmental Insights</CardTitle>
    </CardHeader>
    <CardContent class="p-2 md:p-6">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
       
        <h2
          class="hidden md:flex md:justify-center md:rounded-xl md:p-4 md:border-2 md:border-green-500 md:bg-green-500/20 text-xl font-semibold text-zinc-900 ">
          Live Metrics</h2>
        <h2
          class="hidden md:flex md:justify-center md:rounded-xl md:p-4 md:border-2 md:border-orange-500 md:bg-orange-500/20 text-xl font-semibold text-zinc-900 ">
          Future Projections</h2>

     
        <div class="h-[250px] md:h-[300px] mb-4">
  
          <Line :data="phRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
  
          <Line :data="phPredictedChartData" :options="chartOptions" />
        </div>

        <div class="h-[250px] md:h-[300px] mb-4">

          <Line :data="temperatureRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">

          <Line :data="temperaturePredictedChartData" :options="chartOptions" />
        </div>

        <div class="h-[250px] md:h-[300px]">

          <Line :data="tdsRealChartData" :options="chartOptions" />
        </div>

        <div class="h-[250px] md:h-[300px]">

          <Line :data="tdsPredictedChartData" :options="chartOptions" />
        </div>

      
        <div class="h-[250px] md:h-[300px]">

          <Line :data="turbidityRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <Line :data="turbidityPredictedChartData" :options="chartOptions" />
        </div>

 
        <div class="h-[250px] md:h-[300px] mb-4">
      
          <Line :data="conductivityRealChartData" :options="chartOptions" />
        </div>
        <div class="h-[250px] md:h-[300px] mb-4">
          <Line :data="conductivityPredictedChartData" :options="chartOptions" />
        </div>
      </div>
    </CardContent>
  </Card> -->
</template>