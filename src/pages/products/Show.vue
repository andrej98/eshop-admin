<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title> {{ product.name }} </q-card-title>
      <q-card-separator />
      <q-card-main>
        <p class="q-mt-md"><strong>Brand:</strong> {{ product.brand }}</p>
        <q-card-separator />

        <p class="q-mt-md"><strong>Color:</strong> {{ product.color }}</p>
        <q-card-separator />

        <p class="q-mt-md"><strong>Price:</strong> {{ product.price }} €</p>
        <q-card-separator />

        <p class="q-mt-md"><strong>Description:</strong></p>
        <p>{{ product.description }}</p>
        <q-card-separator />

        <p class="q-mt-md"><strong>Images:</strong></p>

        <div class="q-mt-md row">
          <div
            v-for="image in product.images"
            v-bind:key="image.file">
            <img :src="`http://localhost:8000/storage/productsimages/images200/${image.file}`" />
          </div>
        </div>
      </q-card-main>
      <q-card-actions class="q-mt-md">
        <div class="row justify-end full-width docs-btn">
          <q-btn label="Back" flat to="/products/index" />
          <q-btn label="Edit" color="primary" @click="$router.push('/products/' + $route.params.id + '/edit')" />
        </div>
      </q-card-actions>
    </q-card>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      product: {
        name: '',
        description: '',
        price: '',
        brand: '',
        color: '',
        images: []
      }
    }
  },
  mounted () {
    axios
      .get(
        `http://127.0.0.1:8000/api/products/` + this.$route.params.id + '/show'
      )
      .then((response) => {
        this.product = response.data
        console.log(this.product.images)
      })
      .catch((error) => {
        this.$q.notify({
          type: 'negative',
          timeout: 2000,
          message: 'Loading product: an error has been occured.'
        })
        console.log(error)
      })
  }
}
</script>

<style lang="stylus">
.docs-btn .q-btn {
  padding: 15px 20px;
}
</style>
