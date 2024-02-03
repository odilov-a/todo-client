<template>
  <div>
    <nav class="bg-gray-800 p-4 text-white">
      <div class="container mx-auto flex justify-between items-center">
        <h1 class="text-4xl font-extrabold tracking-wider">Graphs</h1>
        <NuxtLink to="/" class="text-xl font-semibold">Main Page</NuxtLink>
      </div>
    </nav>
    <canvas ref="myChartCanvas" style="width: 100%; max-width: 700px; max-height: 600px"></canvas>
  </div>
</template>

<script setup>
import Chart from "chart.js/auto";

const myChartCanvas = ref(null);

const data = {
  labels: [
    "January", "February", "March",
    "April", "May", "June", "July", 
    "August", "September", "October", 
    "November", "December",
  ],
  datasets: [
    {
      label: "To do Graph",
      data: [10, 20, 30, 40, 50],
      backgroundColor: ["red", "orange", "green", "blue", "pink"],
    },
  ],
};

const options = {
  // Customize your options here
};

// Wait for the component to be mounted and the canvas element to be available
onMounted(() => {
  const ctx = myChartCanvas.value.getContext("2d");
  const myChart = new Chart(ctx, {
    type: "bar",
    data,
    options,
  });
});
</script>
