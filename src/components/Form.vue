<script>
  import VueGoogleAutocomplete from 'vue-google-autocomplete';
  import LocalStorage from '../assets/js/LocalStorage';

  export default {
   name: 'vgForm',

   components: { VueGoogleAutocomplete },

   mounted() {
    this.firebase = window.vgFirebase;

    let storage = new LocalStorage();

    if (storage.get('userInfo')) {
      this.userInfo = storage.get('userInfo');
    }
   },

   methods: {
    onSubmitForm() {
      let obj = {};

      obj.form = this.form;
      obj.form.id = this.userInfo.user.uid + '-' + new Date().getTime();

      obj.user = {
        email: this.userInfo.user.email,
        name: this.userInfo.user.displayName,
        id: this.userInfo.user.uid,
        image: this.userInfo.user.photoURL,
        social: this.userInfo.credential.provider.replace('.com', '')
      };

      this.firebase.addVeggie(obj);
      this.clearFields();
    },

    clearFields() {
      this.form = {
        type: '',
        veggie: {
          name: '',
          address: '',
          location: []
        }
      }
    },

    getAddressData(addressData) {
      let { route, locality, latitude, longitude, country } = addressData;

      this.form.veggie.address = `${route}, ${locality} - ${country}`;

      this.form.veggie.location[0] = latitude;
      this.form.veggie.location[1] = longitude;
    }
   },

   data() {
    return {
      form: {
        type: '',
        veggie: {
          name: '',
          address: '',
          location: []
        }
      },
      userInfo: {}
    }
   }
  }
</script>

<template>
  <div>
    <form @submit.prevent="onSubmitForm">
      <div class="control is-horizontal">
        <div class="control-label">
          <label for="name" class="label">Nome</label>
        </div>

        <div class="control is-grouped">
          <p class="control is-expanded">
            <input
              id="name"
              class="input"
              type="text"
              required
              v-model="form.veggie.name"
              placeholder="Nome do Veggie">
          </p>
        </div>
      </div>

      <div class="control is-horizontal">
        <div class="control-label">
          <label for="address" class="label">Endereço</label>
        </div>

        <div class="control is-grouped">
          <p class="control is-expanded">
            <vue-google-autocomplete
              id="map"
              class="input"
              required
              placeholder="Endereço do Veggie"
              v-model="form.veggie.address"
              v-on:placeChanged="getAddressData">
            </vue-google-autocomplete>
          </p>
        </div>
      </div>

      <div class="control">
        <label class="radio">
          <input
            type="radio"
            name="type"
            required
            v-model="form.type"
            value="evento">

            Evento
        </label>

        <label class="radio">
          <input
            type="radio"
            name="type"
            required
            v-model="form.type"
            value="fixo">

            Fixo
        </label>
      </div>

      <div class="control">
        <button type="submit" class="button is-primary">Pronto</button>
      </div>
    </form>

  </div>
</template>

<style lang="scss"></style>