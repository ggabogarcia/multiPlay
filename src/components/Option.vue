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
    </v-ons-toolbar>

    <div class="center text">
      <h1>Option</h1>
      <form
        action="index.html"
        method="post"
      >
        <div class="form-group center">
          <ons-button
            class="finalButton"
            type="button"
            @click="changeNightMod()"
          >
            {{ textNightMod }}
          </ons-button >
        </div>
        <div class="form-group center">
          <div>
            desactivation du verrouillage du téléphone sur le lecteur losque l'appareil est en charge <br>
            (peut augmenter la consommation de l'appareil hors charge)
          </div>
          <div class="span__switch">
            <v-ons-switch
              v-model="lockSwitch"
            />
          </div>
        </div>
        <div class="form-group center">
          <label for="stackLimit"> Nombre de retour en arrière dans la playlist possible</label>
          <input
            v-model="stackLimit"
            class="input__text"
            type="number"
            name="stackLimit"
            min="1"
          >
        </div>
        <div class="form-group center">
          <label for="noRepeat"> Nombre de musique avant une possibilité de réécouter la musique actuelle</label>
          <input
            v-model="noRepeat"
            class="input__text"
            type="number"
            name="noRepeat"
            min="1"
          >
        </div>
        <div class="form-group center">
          <label for="folderMusic"> Nom du dossier contenant les musiques (effectif au démarrage)</label>
          <input
            v-model="folderMusic"
            class="input__text"
            type="text"
            name="folderMusic"
          >
        </div>
        <div class="form-group end">
          <ons-button
            class="finalButton"
            type="submit"
            @click="saveOption()"
          >
            Sauvegarder
          </ons-button >
          <ons-button
            class="finalButton"
            type="button"
            @click="init()"
          >
            Réinitialiser
          </ons-button >
        </div>
      </form>

    </div>
  </v-ons-page>
</template>

<script>
import store from '@/store'
export default {
  name: 'About',
  data() {
    return {
      stackLimit: '',
      noRepeat: '',
      folderMusic: 'Music',
      nightMod: '',
      textNightMod: '',
      lockSwitch: false,
    }
  },
  async beforeCreate() {
    this.nightMod = await store.state.nightMod
    this.stackLimit = await store.state.stackLimit
    this.noRepeat = await store.state.noRepeat
    this.switchNightMod()
    if (store.state.folderMusic.length > 0) {
      this.folderMusic = store.state.folderMusic
    }
    this.lockSwitch = store.state.lockSwitch
  },
  methods: {
    saveOption() {
      if (this.stackLimit < 1) {
        this.stackLimit = 1
      }
      if (this.noRepeat < 1) {
        this.noRepeat = 1
      }
      if (this.folderMusic.length < 1) {
        this.folderMusic = 'Music'
      }
      let json = {
        stackLimit: this.stackLimit,
        noRepeat: this.noRepeat,
        folderMusic: this.folderMusic,
        lockSwitch: this.lockSwitch,
      }
      store.dispatch('changeOption', json)
      this.$ons.notification
        .toast({
          animation: 'fall',
          message: 'Sauvegardé!',
          timeout: 2000,
        })
        .then(i => (this.shutUp = i === 0))
    },
    async init() {
      //don't delete or modify
      //default value
      let r = await this.$ons.notification.confirm(
        'Tous les paramètres vont être réinitialiser!',
        { title: 'ATTENTION' },
      )
      if (r == 1) {
        this.stackLimit = '25'
        this.noRepeat = '10'
        this.folderMusic = 'Music'
        this.saveOption()
      }
    },
    changeNightMod() {
      this.nightMod = !this.nightMod
      store.commit('changeNightMod', this.nightMod)
      this.$emit('interface', this.nightMod)

      this.switchNightMod()
    },
    switchNightMod() {
      if (this.nightMod == true) {
        this.textNightMod = 'Mode jour'
      } else {
        this.textNightMod = 'Mode nuit'
      }
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.form-group {
  width: 90%;
  margin: 15px 5% 10px 5%;
}
.form-group label {
  display: block;
}
input {
  height: 1.1em;
  margin-top: 5px;
  width: 60%;
}
.finalButton {
  display: block;
  width: 70%;
  margin: 10px 15% 10px 15%;
  text-align: center;
}
.finalButton-night {
  background-color: red;
}
.end {
  margin-top: 20px;
}
</style>
