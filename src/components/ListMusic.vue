<template>
  <v-ons-page>
    <v-ons-toolbar class="home-toolbar">
      <div class="left">
        <v-ons-toolbar-button @click="$store.commit('splitter/toggle')">
          <v-ons-icon icon="md-menu"/>
        </v-ons-toolbar-button>
      </div>
      <div class="center">
        <img
          class="logo"
          src="@/assets/img/MultiPlay.png"
          alt=""
        >
        MultiPlay
      </div>
      <div class="right">
        <v-ons-toolbar-button @click="savePlaylist()">
          <v-ons-icon icon="md-save"/>
        </v-ons-toolbar-button>
        <v-ons-toolbar-button @click="goingBack()">
          <v-ons-icon icon="ion-arrow-return-left"/>
        </v-ons-toolbar-button>
      </div>
    </v-ons-toolbar>
    <div>
      <v-ons-list-title>Liste des musiques</v-ons-list-title>
      <v-ons-list>

        <v-ons-list-item
          v-for="item in listMusic"
          :key="item.name"
        >
          <label class="label_music">

            <ons-checkbox
              :id="item.path"
              class="checkbox"
            />
            {{ item.name }}

          </label>
        </v-ons-list-item>

      </v-ons-list>

    </div>



  </v-ons-page>
</template>

<script>
import store from '@/store'

export default {
  name: 'ListMusic',
  data() {
    return {
      listMusic: [],
    }
  },
  created() {
    if (store.state.memSecondListID == '') {
      this.$router.push({ name: 'HomePage' })
      store.commit('removeMemMainListID')
      store.commit('removeMemSecondListID')
    }
  },
  beforeMount() {
    if (!store.getters['emptyListMusic']) {
      let globalThis = this
      MediaRetrieve.getAudioList(data => { //eslint-disable-line
        //doesn't need to import MediaRetrieve
        globalThis.getData(data)
        //write here because asynchonus function
      })
    } else {
      this.loadList()
    }
  },
  mounted() {
    this.loadCheck()
  },
  methods: {
    savePlaylist() {
      let array = []
      for (let i in this.listMusic) {
        if (document.getElementById(this.listMusic[i].path).checked) {
          array.push(this.listMusic[i].path)
        }
      }
      store.dispatch('changeSecondListPlaylist', array)
      this.$ons.notification
        .toast({
          animation: 'fall',
          message: 'Sauvegardé !',
          timeout: 2000,
        })
        .then(i => (this.shutUp = i === 0))
    },
    goingBack() {
      this.$router.push({ name: 'PlaylistPage' })
      store.commit('removeMemSecondListID')
    },
    getData(rawData) {
      store.dispatch('setListMusic', rawData.data)
      this.loadList()
    },
    loadList() {
      this.listMusic = store.getters['getListMusic']
    },
    loadCheck() {
      let select =
        store.state.secondList[
          store.state.secondList
            .map(item => {
              return item.id
            })
            .indexOf(store.state.memSecondListID)
        ].subPlaylist

      for (let y in select) {
        document.getElementById(select[y]).checked = true
      }
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.label_music {
  display: block;
  width: 100%;
}
</style>
