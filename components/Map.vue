<!-- <script setup>

import Card from '~/components/card/Card.vue';
import CardHeader from '~/components/card/CardHeader.vue';
import CardTitle from '~/components/card/CardTitle.vue';
import CardContent from '~/components/card/CardContent.vue';

const mapRef = ref(null);
const aecLocation = { lat: 26.142091, lng: 91.661836 };
const runtimeConfig = useRuntimeConfig();

onMounted(() => {
    const initMap = () => {
        const map = new window.google.maps.Map(mapRef.value, {
            center: aecLocation,
            zoom: 20,
            mapTypeId: 'satellite',
            mapTypeControl: true,
            mapTypeControlOptions: {
                style: window.google.maps.MapTypeControlStyle.HORIZONTAL_BAR,
                position: window.google.maps.ControlPosition.TOP_RIGHT,
            },
        });

        // Add marker
        new window.google.maps.Marker({
            position: aecLocation,
            map: map,
            title: 'Boat Live Location',
        });
    };

    // Load Google Maps script if not already loaded
    if (!window.google) {
        const script = document.createElement('script');
        script.src = `https://maps.googleapis.com/maps/api/js?key=${runtimeConfig.public.googleMapsApiKey}`;
        script.async = true;
        script.defer = true;
        script.onload = initMap;
        document.head.appendChild(script);
    } else {
        initMap();
    }
});
</script>

<template>
    <Card class="mt-2 w-[90%]">
        <CardHeader class="flex items-center">
            <CardTitle>Location Tracker</CardTitle>
        </CardHeader>
        <CardContent>
            <div ref="mapRef" style="width: 100%; height: 400px; border-radius: 0.5rem;" />
        </CardContent>
    </Card>
</template> -->


<script setup>

const mapRef = ref(null);
const aecLocation = { lat: 26.142091, lng: 91.661836 };
const runtimeConfig = useRuntimeConfig();

onMounted(() => {
    const initMap = () => {
        const map = new window.google.maps.Map(mapRef.value, {
            center: aecLocation,
            zoom: 20,
            mapTypeId: 'satellite',
            mapTypeControl: true,
            mapTypeControlOptions: {
                style: window.google.maps.MapTypeControlStyle.HORIZONTAL_BAR,
                position: window.google.maps.ControlPosition.TOP_RIGHT,
            },
        });

        new window.google.maps.Marker({
            position: aecLocation,
            map: map,
            title: 'Boat Live Location',
        });
    };

    if (!window.google) {
        const script = document.createElement('script');
        script.src = `https://maps.googleapis.com/maps/api/js?key=${runtimeConfig.public.googleMapsApiKey}`;
        script.async = true;
        script.defer = true;
        script.onload = initMap;
        document.head.appendChild(script);
    } else {
        initMap();
    }
});
</script>

<template>
    <div class="mt-6 w-[90%] max-w-5xl mx-auto bg-white/40">
        <div class=" rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 border border-gray-100">
           
            <!-- Map Content -->
            <div class="p-4">
                <div ref="mapRef" class="w-full h-[400px] rounded-lg border border-gray-200"></div>
            </div>
             <!-- Header -->
             <div class="pb-4 border-b flex items-center justify-center">
                <h2 class="text-3xl font-bold text-zinc-700 tracking-tight">Location Tracker</h2>
            </div>
        </div>
    </div>
</template>