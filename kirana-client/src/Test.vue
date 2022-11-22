<template>
  <div>
    <input type="text" v-model="msg" />
    <button @click="playMsg()">play</button>
  </div>
</template>

<script>
export default {
  mounted() {
    // let f = speechSynthesis.getVoices().map((it) => ({
    //   name: it.name,
    //   lang: it.lang,
    // }));
    // console.table(f);
    //id 155 m , 156 f
    // jv 164 dimas , 165 siti
    // sunda 266 jajang , 267 tuti
  },
  methods: {
    playMsg() {
      var voices = window.speechSynthesis.getVoices();
      let msg = new SpeechSynthesisUtterance();
      //   msg.volume = 1; // 0.1 sampai 1
      //   msg.rate = 1; // 0.1 sampai 10
      //   msg.pitch = 2; // 0 sampai 2
      msg.voice = voices[266];

      //   msg.text =
      //     "pramila inggih menawi wonten perkawis kados mekaten, langkung sae rembagan rumiyin, ampun mendel-mendelan kemawon. cobi mang carios ";
      msg.text = `Panonna alus, irung alus, biwir alus
Ditempo ti hareup ti gigir mani mulus
Ngan hanjakal pisan kalakuan siga sétan
Gunta-ganti jalu teu sirikna unggal minggu`;
      window.speechSynthesis.speak(msg);
    },
  },
  data: () => ({
    msg: "I love you",
  }),
};
</script>

<style>
</style>