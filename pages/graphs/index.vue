<template>
  <div>
    <nav class="bg-gray-800 p-4 text-white">
      <div class="container mx-auto flex justify-between items-center">
        <h1 class="text-4xl font-extrabold tracking-wider">Graphs</h1>
        <NuxtLink to="/" class="text-xl font-semibold">Main Page</NuxtLink>
      </div>
    </nav>
    <canvas
      ref="myChartCanvas"
      style="width: 100%; max-width: 700px; max-height: 600px"
    ></canvas>
  </div>
</template>

<script setup>
import Chart from "chart.js/auto";

const myChartCanvas = ref(null);

const fetchData = async () => {
  try {
    const response = await fetch("http://localhost:8080/api/todos");
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    const todos = await response.json();

    const monthlyCounts = todos.reduce((acc, todo) => {
      const month = new Date(todo.createdAt).toLocaleString("en-US", {
        month: "long",
      });
      const statusKey = todo.completed ? "Completed" : "Not Completed";
      acc[month] = acc[month] || { Completed: 0, "Not Completed": 0 };
      acc[month][statusKey]++;
      return acc;
    }, {});

    const labels = Object.keys(monthlyCounts);
    const datasets = Object.keys(monthlyCounts[labels[0]]).map((status) => ({
      label: `${status} Tasks per Month`,
      data: labels.map((month) => monthlyCounts[month][status]),
      backgroundColor:
        status === "Completed"
          ? "rgba(75, 192, 192, 0.2)"
          : "rgba(255, 99, 132, 0.2)",
      borderColor:
        status === "Completed"
          ? "rgba(75, 192, 192, 1)"
          : "rgba(255, 99, 132, 1)",
      borderWidth: 1,
    }));

    if (labels.length === 0) {
      console.warn("No months found in the dataset.");
      return;
    }
    createChart(labels, datasets);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

const createChart = (labels, datasets) => {
  const ctx = myChartCanvas.value.getContext("2d");
  new Chart(ctx, {
    type: "bar",
    data: {
      labels: labels,
      datasets: datasets,
    },
    options: {
      scales: {
        x: {
          type: "category",
          labels: labels, // Ensure that labels are strings
          rotation: 45,
        },
        y: {
          beginAtZero: true,
        },
      },
      plugins: {
        legend: {
          display: true,
          position: "top",
        },
        title: {
          display: true,
          text: "Tasks Status per Month",
          font: {
            size: 16,
          },
        },
      },
    },
  });
};

onMounted(() => {
  fetchData();
});
</script>
