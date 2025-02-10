<script lang="ts">
    import { onMount } from 'svelte';
    import Chart from 'chart.js/auto'; // auto-imports necessary components from Chart.js
  
    // Props: labels (languages) and data (their counts)
    export let labels: string[] = [];
    export let data: number[] = [];
  
    let canvasEl: HTMLCanvasElement;
    let chart: Chart;
  
    // Helper: Generate a consistent color based on the label text.
    function generateColor(label: string): string {
      let hash = 0;
      for (let i = 0; i < label.length; i++) {
        hash = label.charCodeAt(i) + ((hash << 5) - hash);
      }
      let color = '#';
      for (let i = 0; i < 3; i++) {
        const value = (hash >> (i * 8)) & 0xff;
        color += ('00' + value.toString(16)).substr(-2);
      }
      return color;
    }
  
    // Create the chart when the component mounts.
    onMount(() => {
      chart = new Chart(canvasEl, {
        type: 'pie',
        data: {
          labels,
          datasets: [
            {
              label: 'Language Distribution',
              data,
              backgroundColor: labels.map((label) => generateColor(label)),
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              labels: {
                font: {
                  family: 'JetBrains Mono',
                  size: 14,
                },
                color: '#fff'
              }
            },
            tooltip: {
              bodyFont: {
                family: 'JetBrains Mono',
                size: 13,
              },
              titleFont: {
                family: 'JetBrains Mono',
                size: 14,
                weight: 'bold'
              },
              backgroundColor: '#333',
              titleColor: '#fff',
              bodyColor: '#fff'
            }
          }
        },
      });
  
      // Clean up on unmount.
      return () => {
        chart.destroy();
      };
    });
  
    // When labels or data change, update the chart.
    $: if (chart) {
      chart.data.labels = labels;
      if (chart.data.datasets[0]) {
        chart.data.datasets[0].data = data;
        chart.data.datasets[0].backgroundColor = labels.map((label) =>
          generateColor(label)
        );
      }
      chart.update();
    }
  </script>
  
  <div class="chart-container">
    <canvas bind:this={canvasEl}></canvas>
  </div>
  
  <style>
    .chart-container {
      width: 100%;
      max-width: 500px;
      height: 400px;
      margin: 0 auto;
    }
  </style>
  