<template>
  <div id="app" class="container my-5">
    <h1 class="text-center text-uppercase">English translater</h1>
    <div class="form-group row my-4">
      <div class="col-xs-12 col-sm-6 col-md-4">
        <textarea class="form-control" v-model="text"></textarea>
      </div>
      <div class="col-xs-12 col-sm-6 col-md-4">
        <select v-if="Object.keys(languages).length" v-model="selectedLanguage">
          <option v-for="(lang,key) in languages"  :value="key">{{lang}}</option>
        </select>
        <div v-else>Loading...</div>
      </div>
      <div class="col-xs-12 col-sm-12 col-md-4">
        <button class="btn-success btn" @click="translate">Translate IT</button>
      </div>
    </div>
    
    <div v-if="translation" class="h3 text-center text-danger">
      {{translation}}
    </div>
  </div>
</template>

<script>

  import axios from 'axios';
  import { parseString } from 'xml2js';

  export default {
    name: 'app',
    data() {
      return {
        text: "",
        languages: [],
        selectedLanguage: "ru",
        key: "trnsl.1.1.20181122T033922Z.92bb3adffe2a8dc4.278cd129f1fd6d1ccb15fce0bb3172a84e6abc51",
        translation: ""
      }
    },
    methods:{
      translate: function () {
        axios.post(`https://translate.yandex.net/api/v1.5/tr/translate?format=plain&lang=en-${this.selectedLanguage}&key=${this.key}&text=${encodeURI(this.text)}`)
          .then(response => {
            let self = this;
            parseString(response.data, (err, result) => {
              self.translation = result.Translation.text[0];
              console.log(result.Translation)
            });
          })
          .catch(e => {
            console.log(e);
          });
      }
    },
    created(){
      axios.get(`https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=`+this.key+"&ui=en")
        .then(response => {
          // JSON responses are automatically parsed.
          this.languages = response.data.langs;
        })
        .catch(e => {
          console.log(e);
        });
    }
  }
</script>

<style lang="scss">

</style>
