<script >
import { ref } from "vue";
import axios from "axios";

export default {
  data() {
    return {
      cari: "",
      surah: ref([]),
      judul: ref([]),
      name: [],
      audio: '',
      translations: ref([]),
    };
  },

  watch: {
    cari() {
      this.getSurah();
      this.getJudul();
      this.getAudio();
      this.getTranslate();
    }
  },

  mounted() {
    this.getSurah();
    this.getJudul();
    this.getAudio();
    this.getTranslate();
  },

  methods: {
    getSurah()
    {
      axios.get("https://api.quran.com/api/v4/quran/verses/uthmani?chapter_number=" + this.cari)
        .then(response =>
        {
          this.surah = response.data.verses;
        })
        .catch(error =>
        {
          console.log(error);
        });
    },
    getJudul()
    {
      axios.get("https://api.quran.com/api/v4/chapters/" + this.cari + "?language=id")
        .then(response =>
        {
          this.judul = response.data.chapter;
          this.name = this.judul.translated_name;
        })
        .catch(error =>
        {
          console.log(error);
        });
    },
    getAudio()
    {
      axios.get('https://api.quran.com/api/v4/chapter_recitations/4/' + this.cari)
        .then(response =>
        {
          this.audio = response.data.audio_file;
        })
        .catch(error =>
        {
          console.log(error);
        });
    },
    getTranslate()
    {
      axios.get('https://api.quran.com/api/v4/quran/translations/39?chapter_number=' + this.cari)
        .then(response =>
        {
          this.translations = response.data.translations;
        })
        .catch(error =>
        {
          console.log(error);
        });
    },
  }
};
</script>

<template>
    <div class="text-lg-center mt-2">
      <div>
        <img src="../assets/dds.png" alt="logo" width="250">
      </div>
    <h1 class="s text-lg-center">&mcy;asukkan &naturals;omor sura&hcirc;!</h1>

      <div class="search-box mt-5">
        <input v-model="cari" type="search" placeholder="Search Here..." class="search-input"/>
        <a href="#" class="search-btn">
          <i class="fi fi-br-search"></i>
        </a>
      </div>

    <div v-if="cari" class=" y text-center mt-5">
      <h1>{{ judul?.name_complex }}</h1>
      <br>
      <h1>{{ judul?.name_arabic }}</h1>
      <br>
      <h3>{{ name?.name }}</h3>
      <br>
      <h4>Diturunkan di {{ judul?.revelation_place }}</h4>
      <br>
      <h5>Terdiri dari {{judul?.verses_count}} ayat</h5>
    </div>
      <p v-if="audio" class="y text-lg-center mt-4">
        <audio v-bind:src="audio?.audio_url" controls>
        </audio>
      </p>
    </div>
    <div v-if="cari" v-for="(ayat,i) in surah" :key="i" class="y text-lg-center mt-4">
        <h5>{{ ayat?.text_uthmani }}{{ayat?.verse_key}}</h5>
        <p v-html="translations[i]?.text"></p>
    </div>
    </template>

<style>
.s {
  font-family: "Corbel Light";
}
.y{
  margin-top: 30px;
}
* {
  margin: 0;
  box-sizing: border-box;
}
.search-box {
  position: absolute;
  top: 49%;
  left: 50%;
  transform: translate(-50%,-50%);
  height: 60px;
  background: #353535;
  line-height: 50px;
  padding: 10px;
  border-radius: 60px;
  cursor: pointer;
}
.search-input{
  background: #353535;
  color: #fff;
  outline: none;
  border: none;
  line-height: 40px;
  width: 0px;
  float: left;
  font-size: 1em;
  transition: 0.7s ease;
}
.search-btn{
  display: flex;
  justify-content: center;
  align-items: center;
  text-decoration: none;
  background: #353535;
  padding: 12px;
  background: url("../assets/search.svg") no-repeat 5px/15px,  white;
  border-radius: 50%;
  float: right;
  transition: 0.4s ease;
  color: white;
}
.search-box:hover > .search-input,
.search-input:focus {
  width: 240px;
  margin: 0px 8px;
}
.search-box:hover > .search-btn,
.search-input:focus + .search-btn {
  background: white;
  color: #101010;
}
</style>
