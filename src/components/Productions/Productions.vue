<template>
    <div class="productions pad-sm">
        <SectionTitle title="Produções em Números">
            Seção destinada aos resultados numéricos de todos os produtos
            que foram ou estão
            <br />sendo produzidos para distribuição para população
        </SectionTitle>
        <!-- <Carrousel> -->
          <div style="display: flex; flex-direction: row; margin-bottom: 4rem;">
            <ProductionCard v-for="(chart, index) in charts" :key="index" :chartProps="chart" />
          </div>
        <!-- </Carrousel> -->
    </div>
</template>

<script>
import SectionTitle from '../utils/SectionTitle.vue'
// import Carrousel from '../Carrousel/Carrousel.vue'
import ProductionCard from './ProductionCard.vue'

export default {
    name: 'Productions',
    components: { SectionTitle, ProductionCard },
    data() {
        return {
            charts: [{
                title: 'Produção recorrente',
                series: [{
                    name: 'Máscaras Face Shield',
                    color: '#66288E',
                    data: [5]
                }, {
                    name: 'Álcool em gel 70%',
                    color: '#1FE850',
                    data: [2]
                }]
            }, {
                title: 'Local de distribuição de máscaras',
                series: [{
                    name: 'Hospital Regional do Sertão Central',
                    color: '#EB5757',
                    data: [5]
                }, {
                    name: 'Unidades de Pronto Atendimento',
                    color: '#FFDE00',
                    data: [2]
                }, {
                    showInLegend: false,
                    color: '#A3E1B3',
                    data: [4]
                }]
            }, {
                title: 'Local de distribuição de álcool em gel',
                series: [{
                    name: 'Hospital Geral do Exército',
                    color: '#00CDCE',
                    data: [5]
                }, {
                    name: 'Instituições e abrigos vínculados',
                    color: '#EB5757',
                    data: [2]
                }, {
                    showInLegend: false,
                    color: '#A3E1B3',
                    data: [4]
                }, {
                    showInLegend: false,
                    color: '#8E69FE',
                    data: [1]
                }]
            }, {
                title: 'Local de distribuição de álcool em gel',
                series: [{
                    name: 'Hospital Geral do Exército',
                    color: '#00CDCE',
                    data: [5]
                }, {
                    name: 'Instituições e abrigos vínculados',
                    color: '#EB5757',
                    data: [2]
                }, {
                    showInLegend: false,
                    color: '#A3E1B3',
                    data: [4]
                }, {
                    showInLegend: false,
                    color: '#8E69FE',
                    data: [1]
                }]
            }]
        }
    }
}
</script>

<style></style>
