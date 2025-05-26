<template>
  <div class="bg-white shadow rounded-lg px-6 py-8 mb-4">
    <h2 class="text-xl font-semibold text-gray-900 mb-4">Répartition par tranches d'imposition</h2>
    <Bar :data="chartData" :options="chartOptions"/>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  netWorth: Number
})

const T1 = 11294
const T2 = 28797
const T3 = 82341
const T4 = 177106

const chartData = computed(() => {
  const rev_net = props.netWorth || 0;
  const abattement = rev_net * 0.1;
  const net_aa = rev_net - abattement;

  let tranche_un = Math.min(net_aa, T1)
  let tranche_deux = Math.max(Math.min(net_aa, T2) - T1, 0)
  let tranche_trois = Math.max(Math.min(net_aa, T3) - T2, 0)
  let tranche_quatre = Math.max(Math.min(net_aa, T4) - T3, 0)
  let tranche_cinq = net_aa > T4 ? (net_aa - T4) : 0

  // Calculate taxes for each tranche
  let impot_td = tranche_deux * 0.11
  let impot_tt = tranche_trois * 0.30
  let impot_tq = tranche_quatre * 0.41
  let impot_tc = tranche_cinq * 0.45

  return {
    labels: ['Tranche 1: 0%', 'Tranche 2: 11%', 'Tranche 3: 30%', 'Tranche 4: 41%', 'Tranche 5: 45%'],
    datasets: [
      {
        label: 'Revenue Net Imposable (€)',
        backgroundColor: '#05966900',
        borderColor: '#059669',
        borderWidth: 3,
        data: [tranche_un, tranche_deux, tranche_trois, tranche_quatre, tranche_cinq],
      },
      {
        label: 'Impôt Marginal (€)',
        backgroundColor: '#f87979',
        data: [0, impot_td, impot_tt, impot_tq, impot_tc],
      }
    ]
  }
})

let delayed;
const chartOptions = {
  responsive: true,
  scales: {
    y: {
      beginAtZero: true,
      stacked: false,
    },
    x: {
        stacked: true
    },
    yAxisID: 'y'
  },
  animation: {
    onComplete: () => {
    delayed = true;
    },
    delay: (context) => {
    let delay = 0;
    if (context.type === 'data' && context.mode === 'default' && !delayed) {
        delay = context.dataIndex * 700 + context.datasetIndex * 700;
    }
    return delay;
    },
  },
  plugins: {
    tooltip: {
      callbacks: {
        label: function (context) {
          const datasetLabel = context.dataset.label || '';
          let value = context.raw;
          return `${datasetLabel}: ${value.toFixed(2)} €`;
        }
      }
    }
  },
}
</script>