<template>
  <q-layout>

    <q-layout-header>
      <q-toolbar>
          <q-btn flat round dense icon="menu" @click="left = !left" class="q-mr-md"/>
          <q-toolbar-title>Elektro.sk - ADMIN</q-toolbar-title>
          <q-btn flat round dense icon="logout" @click="logout"  class="q-mr-md"/>
      </q-toolbar>
    </q-layout-header>

    <q-layout-drawer v-model="left" side="left">
      <main-layout-drawer></main-layout-drawer>
    </q-layout-drawer>

    <q-page-container>
      <div class="row full-width justify-center">
        <router-view class="c-container" />
        <q-ajax-bar />
      </div>
    </q-page-container>

    <q-layout-footer>
      <div class="text-center q-pa-md">Copyright (C) 2020, Andrej</div>
    </q-layout-footer>
  </q-layout>
</template>

<script>
import MainLayoutDrawer from 'components/layouts/Main/Drawer.vue'
import axios from 'axios'

export default {
  components: {
    MainLayoutDrawer
  },
  methods: {
    logout () {
      axios
        .post(window.location.href.split('ad')[0] + `logout`)
        .then((response) => {
          this.$q.notify({
            type: 'positive',
            timeout: 2000,
            message: 'logged out'
          })
          // window.location.href = `http://127.0.0.1:8000/login`
        })
        .catch((error) => {
          console.log(error)
        })
      window.location.href = window.location.href.split('ad')[0] + `login`
    }
  },
  computed: {
    left: {
      get () { return this.$store.state.moduleUI.layout.drawerState },
      set (val) { this.$store.commit('moduleUI/updateDrawerState', val) }
    }
  }
}
</script>
