<script >
import { ref } from "vue";
import axios from "axios";

export default {
  data() {
    return {
      cari: "1",
      surah: ref([]),
      judul: ref([]),
      name: [],
      audio: '',
      translations: ref([]),
      tafsirs: ref([]),
    };
  },

  watch: {
    cari()
    {
      this.getSurah();
      this.getJudul();
      this.getAudio();
      this.getTranslate();
      this.getTafsir();
    }
  },

  mounted()
  {
    this.getSurah();
    this.getJudul();
    this.getAudio();
    this.getTranslate();
    this.getTafsir();
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
    getTafsir()
    {
      axios.get('https://api.quran.com/api/v4/quran/tafsirs/381?chapter_number=' + this.cari)
        .then(response => {
          this.tafsirs = response.data.tafsirs;
        })
        .catch(error => {
          console.log(error);
          this.error = true;
        })
        .finally(() => this.loading = false);
    },
  }
};
</script>

<template>
  <div class="text-lg-center mt-5">
    <h1 class="text-lg-center">Masukkan nomor surah!</h1>
    <input v-model="cari" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
    <form class="d-flex">
      <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-success" type="submit">Search</button>
    </form>
    <div class="mt-5">
      <h1>{{ judul.name_complex }}</h1>
      <br>
      <h1>{{ judul.name_arabic }}</h1>
      <br>
      <h3>{{ name.name }}</h3>
      <br>
      <h4>Diturunkan di {{ judul.revelation_place }}</h4>
      <br>
      <h5>Terdiri dari {{judul.verses_count}} ayat</h5>
      <p v-if="audio" class="text-lg-center mt-4">
        <audio v-bind:src="audio.audio_url" controls>
        </audio>
      </p>
    </div>
    <div v-for="(ayat,i) in surah" :key="i" class="text-lg-center mt-4">
        <h5>{{ ayat.text_uthmani }}{{ayat.verse_key}}</h5>
        <p v-html="translations[i].text"></p>
    </div>
    </div>
</template>
