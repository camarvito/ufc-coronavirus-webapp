<template>
  <div class="location-card">
    <div class="location-card__header">
      <img :src="location.place.imageUrl"
          alt="local-doacao">
      <div>
        <div class="location-card__header--title">
        {{ location.place.name }}
        </div>
        <div class="location-card__header--city">
        {{ location.place.city }}, {{ location.place.uf }}
        </div>
      </div>
    </div>
    <div class="location-card__body">
      <div class="location-card__body--title">
        Quantidade Solicitada
      </div>
      <div class="location-card__body--value">
        {{ location.quantity }} Unidades
      </div>
      <div class="location-card__body--title">
        Início da Necessidade
      </div>
      <div class="location-card__body--value">
        09/04/2020
      </div>
      <div class="location-card__body--title">
        Término da Necessidade
      </div>
      <div class="location-card__body--value">
        22/05/2020
      </div>
      <a class="location-card__body--link">Acessar Mapa</a>
    </div>
  </div>
</template>

<script>
export default {
    props: ['location']
}
</script>

<style lang="scss">
.location-card {
  display: grid;
  grid-template-rows: 35% 1fr;

  height: 36.5rem;
  width: 28.5rem;
  padding: 3rem;
  margin: 0 1rem;
  border-radius: 2rem;

  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);

  &__header {
    display: flex;
    align-items: center;
    flex-direction: row;
    margin-bottom: 2rem;
    border-bottom: 1px solid rgba(0, 0, 0, 0.25);

    img {
      height: 5.4rem;
      width: 5.4rem;
      border-radius: 50%;
      margin-right: 2rem;
    }

    &--title {
      color: var(--black-4);
      line-height: 2.2rem;
      font-weight: 500;
      font-size: 1.8rem;
    }

    &--city {
      color: var(--black-5);
      font-weight: 400;
      font-size: 1.4rem;
    }

  }

  &__body {
    &--title {
      color: var(--black-5);
      font-weight: 400;
      font-size: 1.8rem;
    }

    &--value {
      color: var(--black-4);
      font-weight: 700;
      font-size: 1.4rem;
      margin-bottom: 1rem;
    }

    &--link {
      color: #2F80ED;
      font-weight: 500;
      font-size: 1.4rem;
    }
  }

}
</style>
