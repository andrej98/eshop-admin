<template>
<div class="bg-grey-1 full-height q-px-md q-py-lg">
    <div class="q-subheading q-mb-sm">Products</div>
    <q-list link class="no-border">
        <q-item class="q-body-1" to="/products/index">
            <q-item-main>List</q-item-main>
        </q-item>
        <q-item class="q-body-1" to="/products/create">
            <q-item-main>Create</q-item-main>
        </q-item>
    </q-list>
    <div class="q-subheading q-mb-sm">Eshop</div>
    <q-list link class="no-border">
      <q-item class="q-body-1" @click.native="eshop" clickable>
        <q-item-main>Redirect</q-item-main>
      </q-item>
    </q-list>
</div>
</template>

<script>

export default {
  methods: {
    eshop () {
      window.location.href = window.location.href.split('ad')[0]
    }
  }
}
</script>
