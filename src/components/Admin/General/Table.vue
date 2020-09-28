<template>
    <table class="table">
        <tr class="title-row">
            <th v-for="(title, index) in titles" :key="index">{{ title }}</th>
        </tr>
        <template v-if="!places">
          <tr
              v-for="(value, index) in values"
              :key="index"
              :class="{ dark: values.indexOf(value) % 2 === 0 }"
          >
              <td v-for="(field, index) in value" :key="index">{{ field }}</td>
              <td v-if="editFunction" style="padding: 0 1rem;">
                  <svg class="icon edit" @click="editFunction(value.id)">
                      <use xlink:href="@/assets/svg/sprites.svg#pencil" />
                  </svg>
              </td>
              <td v-if="deleteFunction" style="padding: 0 1rem;">
                  <svg class="icon delete" @click="deleteFunction(value.id)">
                      <use xlink:href="@/assets/svg/sprites.svg#delete" />
                  </svg>
              </td>
          </tr>
        </template>
        <!-- Table for places -->
        <template v-else>
          <tr
              v-for="(value, index) in values"
              :key="index"
              :class="{ dark: values.indexOf(value) % 2 === 0 }"
          >
            <td>
              <img :src="value.imageUrl" alt="place-image">
            </td>
            <td>
              {{ value.name }}
            </td>
            <td>
              {{ value.street }}
            </td>
            <td>
              {{ value. city }}
            </td>
            <td>
              {{ value.uf }}
            </td>
          </tr>
        </template>
    </table>
</template>

<script>
export default {
    props: {
        titles: {
            type: Array,
            required: false
        },
        values: {
            type: Array,
            required: false
        },
        places: {
            type: Boolean,
            required: false,
            default: false
        },
        editFunction: {
            type: Function,
            required: false
        },
        deleteFunction: {
            type: Function,
            required: false
        }
    },
    computed: {

    }
}
</script>

<style lang="scss" scoped>
.dark {
    background-color: #f7f7f7;
}

.table {
    font-size: 1.4rem;
    width: 100%;

    tr {
        height: 60px;
    }

    img {
      height: 4rem;
      width: 4rem;
      border-radius: 50%;
    }
}

.title-row {
    text-transform: uppercase;

    th {
        text-align: start;
    }
}

.icon {
    height: 1.6rem;
    width: 1.6rem;
    fill: rgba($black, 0.45);
    cursor: pointer;

    &:hover.edit {
        fill: #f6e58d;
    }

    &:hover.delete {
        fill: #ff7979;
    }
}
</style>
