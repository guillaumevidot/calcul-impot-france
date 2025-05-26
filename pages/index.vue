
<template>
  <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
    <form @submit.prevent="computeTaxes" class="bg-white shadow rounded-lg px-6 py-8 mb-4">
      <div class="mb-4">
        <label for="rev_net" class="block text-sm font-medium text-gray-700">Revenu annuel net imposable (€)</label>
        <input
          v-model.number="netWorth"
          type="number"
          id="rev_net"
          name="rev_net"
          class="mt-1 block w-full shadow-sm sm:text-sm focus:ring-emerald-500 focus:border-emerald-500 border-gray-300 rounded-md"
        />
      </div>
      <div class="mb-4">
        <label for="impot_a_la_source" class="block text-sm font-medium text-gray-700">Prélèvement à la source déjà payé (€)</label>
        <input
          v-model.number="taxesPaid"
          type="number"
          id="impot_a_la_source"
          name="impot_a_la_source"
          class="mt-1 block w-full shadow-sm sm:text-sm focus:ring-emerald-500 focus:border-emerald-500 border-gray-300 rounded-md"
        />
      </div>
      <button
        type="submit"
        class="w-full bg-emerald-600 text-white py-2 px-4 rounded-md hover:bg-emerald-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-emerald-500"
      >
        Calculer
      </button>
    </form>

     <!-- Chart Component -->
    <TaxBracketChart v-if="result" :netWorth="netWorth"/>

    <!-- Results Section (with animations) -->
    <div v-if="result" class="bg-white shadow rounded-lg px-6 py-8">
      <h2 class="text-xl font-semibold text-gray-900 mb-4">Détails du calcul de l'impôt sur le revenu</h2>
      <div class="grid grid-flow-col lg:auto-cols-max gap-4 place-content-center items-center">
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Net Imposable (après abattement)</h3>
            </template>
            <p class="md:text-2xl font-bold text-slate-600">{{ result.net_aa }} €</p>
        </Card>
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Abattement</h3>
            </template>
            <p class="md:text-2xl font-bold text-emerald-600">{{ result.abattement }} €</p>
        </Card>
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Taux Marginal d'Imposition</h3>
            </template>
            <p class="md:text-2xl font-bold text-yellow-600">{{ result.tmi }} %</p>
        </Card>
      </div>

      <div class="mt-4">
        <h4 class="text-lg font-semibold text-gray-900">Impôt marginal</h4>
        <div class="grid grid-cols-4 gap-4">
          <div class="col-start-2 font-bold">Part du revenu</div> <div class="font-bold">Taux d’utilisation de la tranche marginale</div> <div class="font-bold">Montant de l'impôt</div>
          
          <div class="font-bold">Tranche 1 (jusqu'à {{ T1 }} €)</div>
          <div>{{ result.tranche_un }} €</div>
          <div>{{ result.t1_pct.toFixed(2) }}%</div>
          <div>0 €</div>

          <div class="font-bold">Tranche 2 (jusqu'à {{ T2 }} €)</div>
          <div>{{ result.tranche_deux }} €</div> 
          <div>{{ result.t2_pct.toFixed(2) }}%</div>
          <div>{{ result.impot_td }} €</div>

          <div class="font-bold">Tranche 3 (jusqu'à {{ T3 }} €)</div>
          <div>{{ result.tranche_trois }} €</div>
          <div>{{ result.t3_pct.toFixed(2) }}%</div>
          <div>{{ result.impot_tt }} €</div>
          
          <div class="font-bold">Tranche 4 (jusqu'à {{ T4 }} €)</div> 
          <div>{{ result.tranche_quatre }} €</div>
          <div>{{ result.t4_pct.toFixed(2) }}%</div>
          <div>{{ result.impot_tq }} €</div>

          <div class="font-bold">Tranche 5 </div>
          <div>{{ result.tranche_cinq }} €</div>
          <div>{{ result.t5_pct.toFixed(2) }}%</div>
          <div>{{ result.impot_tc }} €</div>
        </div>
      </div>
      <div class="grid grid-flow-col lg:auto-cols-max gap-4 place-content-center items-center">
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Impôt estimé</h3>
            </template>
            <p class="md:text-2xl font-bold text-slate-600">{{ result.total_impot }} €</p>
        </Card>
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Déjà payé</h3>
            </template>
            <p class="md:text-2xl font-bold text-emerald-600"> {{ result.impot_a_la_source }} €</p>
        </Card>
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Reste à payer</h3>
            </template>
            <p class="md:text-2xl font-bold text-red-600"> {{ result.reste_a_payer }} €</p>
        </Card>
        <Card>
            <template #header>
                <h3 class="text-xl font-semibold text-gray-900">Taux moyen</h3>
            </template>
            <p class="md:text-2xl font-bold text-yellow-600">{{ result.taux_a_la_source_conseille.toFixed(2) }}%</p>
        </Card>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Card from './../components/Card.vue'
import TaxBracketChart from './../components/TaxBracketChart.vue'

const netWorth = ref(null)
const taxesPaid = ref(null)

// 2025 Taxes thresholds
const T1 = 11497
const T2 = 29315
const T3 = 83823
const T4 = 180294

const result = ref(null)

function computeTaxes() {
  let rev_net = netWorth.value
  let impot_a_la_source = taxesPaid.value

  // Compute tax brackets similarly to your Django code
  const abattement = rev_net * 0.1;
  const net_aa = rev_net - abattement;

  let tranche_un = Math.min(net_aa, T1)
  let tranche_deux = Math.max(Math.min(net_aa, T2) - T1, 0)
  let tranche_trois = Math.max(Math.min(net_aa, T3) - T2, 0)
  let tranche_quatre = Math.max(Math.min(net_aa, T4) - T3, 0)
  let tranche_cinq = net_aa > T4 ? (net_aa - T4) : 0

  let impot_td = tranche_deux * 0.11
  let impot_tt = tranche_trois * 0.30
  let impot_tq = tranche_quatre * 0.41
  let impot_tc = tranche_cinq * 0.45

  let tmi = 0;
  if (tranche_cinq > 0) {
    tmi = 45;
  } else if (tranche_quatre > 0) {
    tmi = 41;
  } else if (tranche_trois > 0) {
    tmi = 30;
  } else if (tranche_deux > 0) {
    tmi = 11;
  } else {
    tmi = 0;
  }

  const totalImpot = impot_td + impot_tt + impot_tq + impot_tc;
  const reste_a_payer = totalImpot - impot_a_la_source;

  result.value = {
    rev_net,
    impot_a_la_source,
    net_aa,
    abattement,
    tranche_un,
    t1_pct: (tranche_un / T1) * 100,
    tranche_deux,
    impot_td,
    t2_pct: (tranche_deux / (T2 - T1)) * 100,
    tranche_trois,
    impot_tt,
    t3_pct: (tranche_trois / (T3 - T2)) * 100,
    tranche_quatre,
    impot_tq,
    t4_pct: (tranche_quatre / (T4 - T3)) * 100,
    tranche_cinq,
    impot_tc,
    t5_pct: (tranche_cinq / 200000) * 100,
    tmi: tmi,
    total_impot: totalImpot,
    impot_a_la_source: impot_a_la_source,
    reste_a_payer: reste_a_payer,
    taux_a_la_source_conseille: Math.round((totalImpot / rev_net) * 100)
  }
}
</script>
<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}
</style>