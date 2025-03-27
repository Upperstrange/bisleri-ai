<script setup>

import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';

const props = defineProps({
    latestData: Object
});

// Status functions
const getPhStatus = (ph) => {
    if (ph < 5) return { status: 'Acidic', color: 'text-red-600' };
    if (ph < 6.5 && ph > 5) return { status: 'Slightly Acidic', color: 'text-orange-500' };
    if (ph > 6.5 && ph < 7.5) return { status: 'Neutral', color: 'text-green-600' };
    if (ph < 9.0 && ph > 7.5) return { status: 'Slightly Basic', color: 'text-blue-500' };
    return { status: 'Basic', color: 'text-purple-600' };
};

const getTurbidityStatus = (turbidity) => {
    if (turbidity < 1) return { status: 'Excellent', color: 'text-green-600' };
    if (turbidity < 5) return { status: 'Good', color: 'text-blue-500' };
    if (turbidity < 10) return { status: 'Fair', color: 'text-orange-500' };
    return { status: 'Poor', color: 'text-red-600' };
};

const getTdsStatus = (tds) => {
    if (tds < 300) return { status: 'Excellent', color: 'text-green-600' };
    if (tds < 600) return { status: 'Good', color: 'text-blue-500' };
    if (tds < 900) return { status: 'Fair', color: 'text-orange-500' };
    return { status: 'Poor', color: 'text-red-600' };
};

const getTemperatureStatus = (temp) => {
    if (temp < 20) return { status: 'Cold', color: 'text-blue-600' };
    if (temp < 25) return { status: 'Cool', color: 'text-blue-600' };
    if (temp > 25 && temp < 30) return { status: 'Normal', color: 'text-green-600' };
    if (temp > 30 && temp < 35) return { status: 'Warm', color: 'text-orange-500' };
    return { status: 'Hot', color: 'text-red-600' };
};

const getConductivityStatus = (conductivity) => {
    if (conductivity < 200) return { status: 'Low', color: 'text-blue-500' };
    if (conductivity < 800) return { status: 'Normal', color: 'text-green-600' };
    return { status: 'High', color: 'text-red-600' };
};
</script>


<template>
    <!-- <Card>
        <CardHeader class="border-b border-gray-100/20 flex items-center">
            <CardTitle class="flex justify-center rounded-xl border border-zinc-300 p-4">Real-time Sensor Data</CardTitle>
        </CardHeader>
        <CardContent class="p-6">
            <DateTime :latestData="latestData" />

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-6">
                <Dial v-if="latestData" :value="latestData.ph" :min="0" :max="14" :optimal="7" unit="pH"
                    title="pH Level" :getStatus="getPhStatus" />
                <Dial v-if="latestData" :value="latestData.turbidity" :min="0" :max="20" :optimal="2.5" unit="NTU"
                    title="Turbidity" :getStatus="getTurbidityStatus" />
                <Dial v-if="latestData" :value="latestData.tds" :min="0" :max="1200" :optimal="450" unit="ppm"
                    title="TDS" :getStatus="getTdsStatus" />
                <Dial v-if="latestData" :value="latestData.temperature" :min="0" :max="50" :optimal="25" unit="°C"
                    title="Temperature" :getStatus="getTemperatureStatus" />
                <Dial v-if="latestData" :value="latestData.conductivity" :min="0" :max="1500" :optimal="500"
                    unit="μS/cm" title="Conductivity" :getStatus="getConductivityStatus" />
            </div>
        </CardContent>
    </Card> -->
    <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-6 max-w-5xl mx-auto p-6">

    <!-- DateTime Section -->

        <DateTime class="" :latestData="latestData" />
 

    <!-- pH Dial -->
   
        <Dial v-if="latestData" :value="latestData.ph" :min="0" :max="14" :optimal="7" unit="pH"
            title="pH Level" :getStatus="getPhStatus" />

    

    <!-- Turbidity Dial -->
  
        <Dial v-if="latestData" :value="latestData.turbidity" :min="0" :max="20" :optimal="2.5" unit="NTU"
            title="Turbidity" :getStatus="getTurbidityStatus" />
        
    <!-- TDS Dial -->
    
        <Dial v-if="latestData" :value="latestData.tds" :min="0" :max="1200" :optimal="450" unit="ppm"
            title="TDS" :getStatus="getTdsStatus" />
       
    <!-- Temperature Dial -->
    
        <Dial v-if="latestData" :value="latestData.temperature" :min="0" :max="50" :optimal="25" unit="°C"
            title="Temperature" :getStatus="getTemperatureStatus" />
       

    <!-- Conductivity Dial -->
   
        <Dial v-if="latestData" :value="latestData.conductivity" :min="0" :max="1500" :optimal="500" unit="μS/cm"
            title="Conductivity" :getStatus="getConductivityStatus" />
    
</div>
</template>
