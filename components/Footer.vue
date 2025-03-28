<template>
    <footer class="bg-zinc-900 rounded-lg border-t border-zinc-300 mt-8 p-8 w-full">
        <div class="container mx-auto px-4">
            <div class="text-center space-y-6">
                <!-- Created By Section -->
                <div>
                    <h3 class="text-base font-medium text-zinc-200">Created By</h3>
                    <p class="text-sm md:text-base text-zinc-300 mt-2">
                        Nitish Gogoi, Abhijit Das, Arnall Saikia, Rajarshi Dutta
                    </p>
                </div>

                <!-- Guidance Section -->
                <div class="mt-8 mb-8">
                    <h3 class="text-base font-medium text-zinc-200">Under the Guidance of</h3>
                    <p class="text-sm md:text-base text-zinc-300 mt-2">
                        Prof. Dinesh Shankar Pegu
                    </p>
                </div>

                <!-- Divider -->
                <div class="h-px bg-zinc-600 w-3/4 mx-auto"></div>

                <!-- Back to Top Button -->
                <div class="mt-4">
                    <button 
                        class="text-sm text-zinc-200 font-bold bg-blue-500 px-4 py-2 rounded-xl border border-zinc-300"
                        @click="scrollToTop"
                    >
                        Back to Top
                    </button>
                </div>

                <!-- Copyright Section -->
                
            </div>
        </div>
    </footer>
</template>

<script>
export default {
    methods: {
        scrollToTop() {
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }
    }
};
</script>

<style>
/* Add any additional styling here if needed */
</style>
