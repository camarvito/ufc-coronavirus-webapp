<template>
    <div class="actions pad-sm">
        <SectionTitle title="Nossas Ações">
            Seção destinada as ações realizadas pela UFC e por pessoas
            externas para
            <br />com o enfrentamento aos danos causados pelo novo
            coronavírus (COVID-19)
        </SectionTitle>
        <Carrousel
            style="margin-bottom: 5rem"
            :itemsQuantity="actions.length"
            :windowSize="2"
            :paginationFactor="405"
        >
            <ActionCard v-for="action in actions" :key="action.id" :action="action" />
        </Carrousel>
    </div>
</template>

<script>
import axios from 'axios'
import { baseApiUrl } from '@/global'

import Carrousel from '../Carrousel/Carrousel.vue'
import SectionTitle from '../utils/SectionTitle.vue'
import ActionCard from './ActionCard.vue'

export default {
    components: { Carrousel, SectionTitle, ActionCard },
    data() {
        return {
            actions: []
        }
    },
    computed: {},
    methods: {
        getActions() {
            const url = `${baseApiUrl}/actions`
            axios.get(url).then(res => {
                /* Take the last 6 actions to put on the carrousel */
                this.actions = res.data.actions.slice(0, 6)
            })
        }
    },
    mounted() {
        this.getActions()
    }
}
</script>

<style lang="scss" scoped>
.actions {
    background-color: #fff;
}

.action-card {
    margin-right: 1.5rem;

    &:last-child {
        margin-right: 0;
    }
}
</style>
