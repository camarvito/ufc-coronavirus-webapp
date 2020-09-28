<template>
    <div class="my-needs">
        <div class="my-actions__header">
            <AdminPageTitle :title="pageTitle">{{ pageSub }}</AdminPageTitle>
            <ActionButton v-if="mode === 'list'" title="Adicionar" :action="addNewAction" />
        </div>

        <!--Mode: List -->
        <div
            v-if="mode === 'list'"
            :class="{ centralize: !values.length }"
            class="my-actions__list"
        >
            <svg v-if="!values.length" class="my-actions__list--no-data-icon">
                <use xlink:href="@/assets/svg/no_data.svg#no-data" />
            </svg>
            <Table v-else :titles="titles"
              :values="values"
              :editFunction="editAction"
              :deleteFunction="deleteAction" />
        </div>

        <!--Mode: Register new action-->
        <div class="my-actions__content" v-if="mode === 'content'">
            <!--Step 1-->
            <div v-if="step === 1" class="my-actions__content--step">
                <label for="title">Titulo da necessidade</label>
                <input
                    type="text"
                    id="title"
                    placeholder="Ex: Máscaras"
                    v-model="need.title"
                />

                <label for="subtitle">Subtítulo</label>
                <input
                    type="text"
                    id="subtitle"
                    placeholder="Ex: Posto de saúde precisando de máscaras"
                    v-model="need.subtitle"
                />

                <label for="subtitle">Texto de doação</label>
                <input
                    type="text"
                    id="subtitle"
                    placeholder="Ex: Para doar entre contato conosco..."
                    v-model="need.donateText"
                />

                <label for="imageUrl">Imagem da necessidade</label>
                <input
                    type="text"
                    id="imageUrl"
                    placeholder="URL da Imagem"
                    v-model="need.imageUrl"
                />

                <label for="searchUrl">URL de busca na internet</label>
                <input
                    type="text"
                    id="searchUrl"
                    placeholder="URL de busca"
                    v-model="need.searchUrl"
                />
            </div>

            <!--Step 2-->
            <div v-if="step === 2" class="my-actions__content--step">
                <label for="imageUrl">Local de necessidade</label>
                <select v-model="p">
                  <option v-for="(place, index) in allPlaces"
                    :value="place"
                    :key="index"> {{ place.name }}</option>
                </select>


                <label for="imageUrl">Quantidade</label>
                <input
                    type="number"
                    id="imageUrl"
                    autocomplete="off"
                    placeholder="Informe a URL da imagem do artigo..."
                    v-model="productQuantity"
                />

                <button @click="() => {
                  this.selectedPlaces.push({ place: this.p, quantity: this.productQuantity });
                  this.productQuantity = ''
                  this.logg()
                }">+ Inserir</button>

                <Table :titles="['Local Necessitado', 'Quantidade']"
                :values="selectedPlaces.map(l => {
                  return { name: l.place.name, quantity: l.quantity }
                })"
                :editFunction="editAction"
                :deleteFunction="deleteAction" />

            </div>

            <!--Bottom -->
            <div class="my-actions__bottom-container">
                <ActionButton
                    :title="previousStepButtonTitle"
                    :action="previousStepAction"
                    :filled="false"
                    style="margin-right: 2rem"
                />
                <ActionButton :title="nextStepButtonTitle" :action="nextStepAction" />
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

import { baseApiUrl } from '@/global'

import AdminPageTitle from '../General/AdminPageTitle'
import ActionButton from '../General/ActionButton'
import Table from '../General/Table'

export default {
    components: {
        AdminPageTitle,
        ActionButton,
        Table
    },
    data() {
        return {
            p: '',
            mode: 'list',
            step: 1,
            titles: [
                'ID',
                'Título',
                'Quantidade',
                'Situação',
                ' ',
                ' '
            ],
            productQuantity: '',
            values: [],
            allPlaces: [],
            selectedPlaces: [],
            need: {}
        }
    },
    computed: {
        pageTitle() {
            if (this.mode === 'list') {
                return 'Minhas Necessidades'
            }

            if (this.mode === 'content' && this.step === 1) {
                return 'Passo 1'
            }

            return 'Passo 2'
        },
        pageSub() {
            if (this.mode === 'list') {
                return this.units
            }

            if (this.mode === 'content' && this.step === 1) {
                return 'Realize 2 passos para criar uma ação'
            }

            return 'Realize o último passo para criar uma ação'
        },
        nextStepButtonTitle() {
            if (this.step === 1) {
                return 'Continuar'
            }

            return 'Salvar'
        },
        previousStepButtonTitle() {
            if (this.step === 1) {
                return 'Cancelar'
            }

            return 'Voltar'
        },
        units() {
            return this.values.length
                ? `${this.values.length} Unidades`
                : 'Nenhuma unidade encontrada.'
        }
    },
    methods: {
        logg() {
            console.log(this.selectedPlaces)
        },
        loadPlaces() {
            const url = `${baseApiUrl}/places`
            axios.get(url).then(res => {
                this.allPlaces = res.data.places
                    .map(el => {
                        const fields = {}

                        fields.id = el._id
                        fields.name = el.name
                        fields.street = el.street
                        fields.uf = el.uf
                        fields.city = el.city
                        fields.imageUrl = el.imageUrl

                        return fields
                    })
            })
        },
        loadUserActions() {
            const url = `${baseApiUrl}/needs`
            axios.get(url).then(res => {
                console.log(res)
                this.values = res.data.needs
                    .filter(el => el.manager._id === this.$store.state.user.data._id)
                    .map(el => {
                        const fields = {}

                        fields.id = el._id
                        fields.title = el.title
                        fields.quantity = el.quantity
                        fields.situation = el.situation

                        return fields
                    })
            })
        },
        addNewAction() {
            this.mode = 'content'
        },
        nextStepAction() {
            if (this.step === 1) {
                this.step += 1
                return
            }

            if (this.step === 2) {
                this.need.needyPlaces = this.selectedPlaces
                    .map(e => ({ place: e.place.id, quantity: e.quantity }))

                this.need.managerId = this.$store.state.user.data._id
                this.need.situation = 'Em andamento'

                // console.log(this.need)

                axios.post(`${baseApiUrl}/needs`, this.need)
                    .then(() => {
                        this.step = 1
                        this.mode = 'list'
                        this.need = {}
                        this.loadUserActions()
                    })
                    .catch(err => console.log(err))
                // this.action.authorId = this.$store.state.user.data._id

                // if (this.action._id) {
                //     axios
                //         .patch(`${baseApiUrl}/actions/${this.action._id}`, this.action)
                //         .then(() => {
                //             this.step = 1
                //             this.mode = 'list'
                //             this.action = {}
                //             this.loadUserActions()
                //         })
                //         .catch(err => console.log(err))
                // } else {
                // }
            }
        },
        previousStepAction() {
            if (this.step === 1) {
                this.mode = 'list'
            }

            if (this.step === 2) {
                this.step -= 1
            }
        },
        editAction(id) {
            const url = `${baseApiUrl}/actions/${id}`
            axios
                .get(url)
                .then(res => {
                    this.action = res.data.action
                    this.mode = 'content'
                })
        },
        deleteAction(id) {
            const url = `${baseApiUrl}/needs/${id}`
            axios
                .delete(url)
                .then(() => {
                    this.loadUserActions()
                })
                .catch(err => {
                    console.log(err)
                })
        }
    },
    mounted() {
        this.loadUserActions()
        this.loadPlaces()
    }
}
</script>

<style lang="scss" scoped>
.centralize {
    margin-top: auto;
    margin-bottom: auto;
}

.my-actions {
    display: flex;
    flex-direction: column;
    height: 100%;

    &__header {
        display: flex;
        justify-content: space-between;
        margin-bottom: 2rem;
    }

    &__list {
        display: flex;
        justify-content: center;
        align-items: center;

        &--no-data-icon {
            height: 262px;
            width: 379px;
        }
    }

    /* Content Form */
    &__content {
        &--step {
            display: flex;
            flex-direction: column;
        }

        label {
            margin: 1rem 0;
            font-size: 1.6rem;
            color: $black;
        }

        input[type*='text'] {
            background-color: #eeeeee;
            border: none;
            font-size: 1.4rem;
            font-family: 'Roboto';
            color: rgba($black, 0.8);
            padding: 1rem 1.5rem;
            border-radius: 0.7rem;
        }
    }

    &__bottom-container {
        display: flex;
        margin: 1rem 0;
    }
}
</style>
