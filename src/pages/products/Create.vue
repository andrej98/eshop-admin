<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title>Create a new product</q-card-title>
      <q-card-main>
        <q-field :count="250">
          <q-input float-label="Name" v-model="product.name" max-length="250" />
        </q-field>
        <q-field :count="250">
          <q-input
            float-label="Brand"
            v-model="product.brand"
            max-length="250"
          />
        </q-field>
        <q-field :count="250">
          <q-input
            float-label="Color"
            v-model="product.color"
            max-length="250"
          />
        </q-field>
        <q-field>
          <q-input
            float-label="Price"
            v-model="product.price"
            type="number"
            min="1"
          />
        </q-field>
        <q-field>
          <q-select
            v-model="select"
            float-label="Categories"
            :options="categories"
          />
        </q-field>
        <q-field :count="5000">
          <q-input
            type="textarea"
            float-label="Description"
            v-model="product.description"
            :max-height="100"
            rows="7"
            :rules="[
              (val) => (val && val.length > 0) || 'Please type something',
            ]"
            ref="input1"
          />
        </q-field>
        <q-field
          helper="Supported format: JPG, max. file size: 300KiB"
          class="q-mt-lg"
        >
          <q-uploader
            float-label="Images"
            url=""
            ref="uploader"
            multiple
            extensions=".png, .jpeg, .jpg"
            :max-file-size="2000"
            auto-expand
            hide-upload-button
            hide-upload-progress
            :upload-factory="uploadImages"
          />
        </q-field>
      </q-card-main>
      <q-card-actions class="q-mt-md">
        <div class="row justify-end full-width docs-btn">
          <q-btn label="Cancel" flat to="/products/index" />
          <q-btn label="Create" color="primary" @click="createProduct" />
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
        images: '',
        id: ''
      },
      categories: [
        {
          label: 'Phones',
          value: 1
        },
        {
          label: 'Notebooks',
          value: 2
        },
        {
          label: 'Accessories',
          value: 3
        }
      ],
      select: ''
    }
  },
  methods: {
    uploadImages (file) {
      console.log(file)
      const fd = new FormData()
      fd.append('file', file)
      console.log(fd)
      axios
        .post('http://127.0.0.1:8000/api/products/' + this.product.id + '/images', fd, {
          headers: { 'content-type': 'multipart/form-data' }
        })
        .then((response) => {
          // this.$q.notify({
          //   type: 'positive',
          //   timeout: 2000,
          //   message: 'The image has been uploaded.'
          // })
          // console.log(response)
          setTimeout(() => { this.$router.push({ path: '/products/' + this.product.id }) }, 500)
        })
        .catch((error) => {
          this.$q.notify({
            type: 'negative',
            timeout: 2000,
            message: 'image upload failed'
          })
          console.log(error)
        })
    },
    createProduct () {
      console.log(this.productData)
      axios
        .post(`http://127.0.0.1:8000/api/products`, this.productData)
        .then((response) => {
          this.$q.notify({
            type: 'positive',
            timeout: 2000,
            message: 'The product has been created.'
          })
          // this.$router.push({ path: '/products/' + response.data.id })
          this.product.id = response.data.id
          this.$refs.uploader.upload()
        })
        .catch((error) => {
          this.$q.notify({
            type: 'negative',
            timeout: 2000,
            message: 'Fill all fields.'
          })
          console.log(error)
        })
    }
  },
  computed: {
    productData: function () {
      return {
        name: this.product.name,
        description: this.product.description,
        price: this.product.price,
        brand: this.product.brand,
        color: this.product.color,
        category: this.select
      }
    }
  }
}
</script>

<style lang="stylus">
.docs-btn .q-btn {
  padding: 15px 20px;
}
</style>
