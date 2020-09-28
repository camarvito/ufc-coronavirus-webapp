<template>
    <div>
        <DashboardHeader />
        <div class="card-row">
            <DashboardTableCard
                v-for="(card, index) in cards"
                :key="index"
                :title="card.title"
                :quantity="values.length"
                :isSelected="card.selected"
                @selected="changeCardSelected(card)"
            />
        </div>
        <div class="table-container">
            <Table :titles="titles" :values="values" />
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { baseApiUrl } from '@/global'

import DashboardHeader from './DashboardHeader'
import DashboardTableCard from './DashboardTableCard'
import Table from '../General/Table'

export default {
    name: 'Dashboard',
    components: {
        DashboardHeader,
        DashboardTableCard,
        Table
    },
    data() {
        return {
            titles: [
                'ID',
                'Título',
                'Criado em',
                'Última Atualização',
                'Responsável',
                ' ',
                ' '
            ],
            values: [],
            cards: [
                { title: 'Todas as ações', selected: true },
                { title: 'Todas as produções', selected: false },
                { title: 'Todas as necessidades', selected: false }
            ]
        }
    },
    methods: {
        getTableData() {
            const url = `${baseApiUrl}/actions`
            axios.get(url).then(res => {
                this.values = res.data.actions.map((action) => ({
                    id: action._id,
                    title: action.title,
                    createdAt: new Date(action.createdAt).toLocaleDateString('pt-br', {
                        day: 'numeric',
                        month: 'long',
                        year: 'numeric'
                    }),
                    updatedAt: new Date(action.updatedAt).toLocaleDateString('pt-br', {
                        day: 'numeric',
                        month: 'long',
                        year: 'numeric'
                    }),
                    author: action.author.name
                }))
            })
        },
        changeCardSelected(card) {
            /* eslint-disable no-param-reassign */
            this.cards.forEach(el => {
                el.selected = false
            })
            card.selected = true
        }
    },
    mounted() {
        this.getTableData()
    }
}
</script>

<style lang="scss" scoped>
.card-row {
    display: flex;
}
</style>
