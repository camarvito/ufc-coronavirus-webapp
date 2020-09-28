<template>
    <div class="necessity-page">
        <Navbar />
        <div class="post pad-sm">
            <h1 class="post__title">{{ need.title }}</h1>
            <h2 class="post__subtitle">{{ need.subtitle }}</h2>

            <div class="post__info">
                <div class="post__info--headers">
                    <h3>Por {{ need.manager.name }}</h3>
                    <h4>05 de abril de 2020 - 12h15</h4>
                    <h4>Última Atualizalição há uma hora</h4>
                </div>
                <div class="post__info--social">
                    <svg class="social-icon">
                        <use xlink:href="@/assets/svg/sprites.svg#facebook-1" />
                    </svg>
                    <svg class="social-icon">
                        <use xlink:href="@/assets/svg/sprites.svg#twitter-1" />
                    </svg>
                    <svg class="social-icon">
                        <use xlink:href="@/assets/svg/sprites.svg#whatsapp" />
                    </svg>
                    <svg class="social-icon">
                        <use xlink:href="@/assets/svg/sprites.svg#pinterest" />
                    </svg>
                </div>
            </div>

            <hr />

            <SwitchLight />

            <div class="post__image">
              <img :src="need.imageUrl" alt="need-image">
            </div>

            <div class="post__donate--title">Como doar</div>
            <div class="post__content" v-html="need.donateText"></div>

            <div class="post__donate--title">Para onde doar</div>
            <div class="post__donate-container">
              <LocationCard v-for="location in need.needyPlaces"
              :key="location"
              :location="location" />
            </div>

            <div class="post__donate--contribute">
              <div>Não tem o produto, mas <br>
              quer contribuir?</div>
              <button @click="searchInternet">Achar Produto</button>
            </div>

            <SectionTitle title="Recomendadas para você"></SectionTitle>

            <div class="post__recommended">
                <NecessityCard
                    style="margin: 0 1rem;"
                    v-for="(recommendation, index) in recommended"
                    :need="recommendation"
                    :key="index"
                />
            </div>
        </div>
        <Footer />
    </div>
</template>

<script>

import axios from 'axios'
import { baseApiUrl } from '@/global'

import Navbar from '../Header/Navbar.vue'
import SectionTitle from '../utils/SectionTitle.vue'
import SwitchLight from '../utils/SwitchLight.vue'
import Footer from '../Footer/Footer.vue'

import LocationCard from './LocationCard.vue'
import NecessityCard from './NecessityCard.vue'

export default {
    components: {
        Navbar,
        SectionTitle,
        SwitchLight,
        Footer,
        NecessityCard,
        LocationCard
    },
    data() {
        return {
            need: {},
            recommended: []
        }
    },
    methods: {
        searchInternet() {
            window.open(this.need.searchUrl)
        },
        loadNeed() {
            const url = `${baseApiUrl}/needs/${this.$route.params.id}`
            axios.get(url).then(res => {
                this.need = res.data.need
            })
        },
        loadRecommended() {
            const url = `${baseApiUrl}/needs`
            axios.get(url).then(res => {
                console.log(res)
                this.recommended = res.data.needs
                    .filter(el => el.id !== this.$route.params.id)
                    .splice(0, 2)
            })
        }
    },
    mounted() {
        this.loadNeed()
        this.loadRecommended()
    }
}
</script>

<style lang="scss">
.post__donate--title {
  color: var(--black-2);
  text-transform: uppercase;
  font-weight: 700;
  font-size: 2.4rem;
  margin: 2rem 0;
}

.post__donate--contribute {
  letter-spacing: .2rem;
  text-align: center;

  margin: 4rem 0;
  font-weight: 700;
  font-size: 2.8rem;

  button {
        border: none;
        border-radius: 0.7rem;
        padding: 1rem 3rem;
        margin: 2.5rem 0;

        font-family: 'Roboto', sans-serif;
        font-size: 1.8rem;
        font-weight: 700;
        letter-spacing: .3rem;
        text-transform: uppercase;
        outline: none;
        cursor: pointer;

        color: $white;
        background-color: #2f80ed;

        z-index: 3;
    }
}

.post__donate-container {
  display: flex;
  flex-direction: row;
}

</style>
