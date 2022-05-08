<script>

export default {
  data() {
    return {
      error: false,
      loading: true,
      verse: null,
      quran: null,
      chapter: null,
      translation: null,
      audio: null,
    }
  },

  methods:{
    async getRandom(){
      const random = Math.floor(Math.random() * 10);
      this.fetchQuran("verses/random?language=id&words=true&translations=id&audio=1&fields="+random)
        .then(res => {
          const verse = res.verse;
          this.verse = verse;
          this.audio = this.getAudioPath(verse.audio.url);

          const verseKey = verse.verse_key;
          this.getQuran(verseKey);

          const chapterNumber = verseKey.split(':')[0];
          this.getChapter(chapterNumber);

          this.getTranslate(verseKey);
        })
    },

    async getQuran(verseKey){
      this.fetchQuran('quran/verses/uthmani?verse_key='+verseKey)
        .then(res => {
          this.quran = res.verses[0]
        })
    },

    getAudioPath(path){
      return 'https://verses.quran.com/'+path;
    },

    getChapter(id){
      this.fetchQuran('chapters/'+id+'?language=id')
        .then(res => {
          this.chapter = res.chapter
        })
    },

    getTranslate(verseKey){
      this.fetchQuran('quran/translations/33?verse_key='+verseKey)
        .then(res => {
          this.translation = res.translations[0]
        })
    },

    async fetchQuran(path){
      this.loading = true;
      const url = "https://api.quran.com/api/v4/"+path;
      return fetch(url, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json'
        }
      })
        .then(res => {
          if (!res.ok) {
            const error = new Error(res.statusText);
            error.json = res.json();
            throw error;
          }
          return res.json();
        })
        .catch(err => {
          alert(err.toString())
        })
        .finally(() => {
          this.loading = false
        })
    }
  },
  mounted() {
    this.getRandom()
  }
}
</script>
<template>
  <section class="hero is-small">
    <div class="text-center m-5">
      <h1 class="title">
        <strong>Random Ayat</strong>
      </h1>
      <h2 class="subtitle">
        Random Ayat from Quran.com API
      </h2>
    </div>
  </section>
  <section class="content">
    <div>
      <h2 v-if="chapter" class="text-lg-end">{{chapter.name_arabic}} {{verse.verse_number}}</h2>
      <p v-if="audio" class="text-lg-end">
        <audio controls>
          <source :src=audio type="audio/mpeg">
        </audio>
      </p>
      <h3 v-if="quran" class="text-lg-end">{{quran.text_uthmani}} {{quran.verse_key}}</h3>
      <h4 class="text-lg-start mt-4">Terjemahan</h4>
      <p v-if="translation" class="text-lg-start">{{translation.text}}</p>
    </div>
    <div class="has-text-centered" v-if="loading">
      <i class="fa-solid fa-spinner fa-pulse"></i>
    </div>
  </section>
</template>



