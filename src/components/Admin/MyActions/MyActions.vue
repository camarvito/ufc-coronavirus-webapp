<template>
    <div class="my-actions">
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
                <label for="title">Titulo da ação</label>
                <input
                    type="text"
                    id="title"
                    placeholder="Ex: Confecção de máscaras"
                    v-model="action.title"
                />

                <label for="subtitle">Subtítulo</label>
                <input
                    type="text"
                    id="subtitle"
                    placeholder="Ex: Postos de saúde precisam de máscaras"
                    v-model="action.subtitle"
                />

                <label>Conteúdo</label>
                <VueEditor v-model="action.content" placeholder="Informe o Post da Ação..." />
            </div>

            <!--Step 2-->
            <div v-if="step === 2" class="my-actions__content--step">
                <label for="imageUrl">URL da Imagem</label>
                <input
                    type="text"
                    id="imageUrl"
                    placeholder="Informe a URL da imagem do artigo..."
                    v-model="action.imageUrl"
                />
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

import { VueEditor } from 'vue2-editor'
import { baseApiUrl } from '@/global'

import AdminPageTitle from '../General/AdminPageTitle'
import ActionButton from '../General/ActionButton'
import Table from '../General/Table'

export default {
    components: {
        VueEditor,
        AdminPageTitle,
        ActionButton,
        Table
    },
    data() {
        return {
            mode: 'list',
            step: 1,
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
            action: {}
        }
    },
    computed: {
        pageTitle() {
            if (this.mode === 'list') {
                return 'Minhas ações'
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
        loadUserActions() {
            const url = `${baseApiUrl}/actions`
            axios.get(url).then(res => {
                this.values = res.data.actions
                    .filter(el => el.author._id === this.$store.state.user.data._id)
                    .map(el => {
                        const fields = {}

                        fields.id = el._id
                        fields.title = el.title
                        fields.createdAt = new Date(el.createdAt).toLocaleDateString('pt-br', {
                            day: 'numeric',
                            month: 'long',
                            year: 'numeric'
                        })
                        fields.updatedAt = new Date(el.updatedAt).toLocaleDateString('pt-br', {
                            day: 'numeric',
                            month: 'long',
                            year: 'numeric'
                        })
                        fields.user = el.author.name

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
                this.action.authorId = this.$store.state.user.data._id

                if (this.action._id) {
                    axios
                        .patch(`${baseApiUrl}/actions/${this.action._id}`, this.action)
                        .then(() => {
                            this.step = 1
                            this.mode = 'list'
                            this.action = {}
                            this.loadUserActions()
                        })
                        .catch(err => console.log(err))
                } else {
                    axios
                        .post(`${baseApiUrl}/actions`, this.action)
                        .then(() => {
                            this.step = 1
                            this.mode = 'list'
                            this.action = {}
                            this.loadUserActions()
                        })
                        .catch(err => console.log(err))
                }
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
            const url = `${baseApiUrl}/actions/${id}`
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
