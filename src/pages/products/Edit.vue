<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title>Edit {{ product.name }}</q-card-title>
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
        <q-field :count="250">
          <q-input
            float-label="Price"
            v-model="product.price"
            type="number"
            min="1"
          />
        </q-field>
        <q-field :count="5000">
          <q-input
            type="textarea"
            float-label="Description"
            v-model="product.description"
            :max-height="100"
            rows="7"
          />
        </q-field>

        <p class="q-mt-md"><strong>Images:</strong></p>

        <div class="q-mt-md row">
          <div v-for="(image,index) in product.images" v-bind:key="image.file">
            <img
              :src="`http://localhost:8000/storage/productsimages/images200/${image.file}`"
            />
            <br />
            <button v-on:click="deleteImage(index)">DELETE</button>
          </div>
        </div>

        <q-field
          helper="Supported format: JPG, max. file size: 300KiB"
          class="q-mt-lg"
        >
          <q-uploader
            float-label="Upload new imagess"
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
          <q-btn label="Update" color="primary" @click="updateProduct" />
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
        images: [],
        id: ''
      },
      deletedImages: []
    }
  },
  methods: {
    uploadImages (file) {
      const fd = new FormData()
      fd.append('file', file)
      console.log(fd)
      axios
        .post(
          'http://127.0.0.1:8000/api/products/' + this.product.id + '/images',
          fd,
          {
            headers: { 'content-type': 'multipart/form-data' }
          }
        )
        .then((response) => {
          // this.$q.notify({
          //   type: 'positive',
          //   timeout: 2000,
          //   message: 'The image has been uploaded.'
          // })
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
    deleteImage (index) {
      this.$q
        .dialog({
          title: 'Delete',
          message: 'Are you sure to delete this image?',
          color: 'primary',
          ok: true,
          cancel: true
        }).then(() => {
          let filename = this.product.images.splice(index, 1)[0]
          filename = filename.file
          this.deletedImages.push(filename)
          console.log(this.deletedImages)
        }).catch(() => {
          // cancel - do nothing?
        })
    },
    updateProduct () {
      axios
        .put(
          `http://127.0.0.1:8000/api/products/` + this.$route.params.id,
          this.productData
        )
        .then((response) => {
          this.$q.notify({
            type: 'positive',
            timeout: 2000,
            message: 'The product has been updated.'
          })
          // this.$router.push('/')
          this.product.id = this.$route.params.id

          var deleted = {
            'images': this.deletedImages
          }
          axios
            .put(
              `http://127.0.0.1:8000/api/products/` + this.$route.params.id + '/images',
              deleted
            ).then((response) => {
              // this.$q.notify({
              //   type: 'positive',
              //   timeout: 2000,
              //   message: 'The image has been deleted.'
              // })
              setTimeout(() => { this.$router.push({ path: '/products/' + this.product.id }) }, 500)
            }).catch((error) => {
              this.$q.notify({
                type: 'negative',
                timeout: 2000,
                message: 'An error has been occured deleting image'
              })
              console.log(error)
            })

          this.$refs.uploader.upload()
        })
        .catch((error) => {
          this.$q.notify({
            type: 'negative',
            timeout: 2000,
            message: 'An error has been occured.'
          })
          console.log(error)
        })
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
  },
  computed: {
    productData: function () {
      return {
        name: this.product.name,
        description: this.product.description,
        price: this.product.price,
        brand: this.product.brand,
        color: this.product.color
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
