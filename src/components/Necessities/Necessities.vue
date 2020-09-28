<template>
    <div class="necessities pad-sm">
        <SectionTitle title="Nossas Necessidades">
            Seção destinada aos materiais, produtos, serviços ou insumos
            necessários
            <br />para que as ações sejam feitas.
        </SectionTitle>
        <Carrousel
            :itemsQuantity="needs.length"
            :windowSize="2"
            :paginationFactor="412"
        >
            <NecessityCard v-for="(need, index) in needs" :key="index" :need="need" />
        </Carrousel>
    </div>
</template>

<script>
import axios from 'axios'
import { baseApiUrl } from '@/global'

import Carrousel from '../Carrousel/Carrousel.vue'
import SectionTitle from '../utils/SectionTitle.vue'
import NecessityCard from './NecessityCard.vue'

export default {
    components: { SectionTitle, Carrousel, NecessityCard },
    data() {
        return {
            needs: []
        }
    },
    methods: {
        getActions() {
            const url = `${baseApiUrl}/needs`
            axios.get(url).then(res => {
                /* Take the last 6 needs to put on the carrousel */
                this.needs = res.data.needs.slice(0, 6)
            })
        }
    },
    mounted() {
        this.getActions()
    }
}
</script>

<style></style>
