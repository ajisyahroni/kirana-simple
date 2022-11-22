<template>
  <v-app
    :style="{ 'background-color': moods[currentMood].bg }"
    style="transition: background-color 1000ms linear"
  >
    <div
      class="d-flex justify-center align-center fill-height"
      style="height: 100vh"
    >
      <v-card flat class="d-flex flex-column" width="50%">
        <v-card-title
          class="white--text"
          :style="{ 'background-color': moods[currentMood].header }"
          style="transition: background-color 1000ms linear"
        >
          <v-avatar color="primary" size="36"><span>K</span></v-avatar>
          <span class="ml-3">Kirana</span>
        </v-card-title>
        <v-card-text
          class="flex-grow-1 overflow-y-auto"
          style="max-height: 60vh; min-height: 60vh"
          id="scrollable-chatlist"
        >
          <div
            v-for="(msg, i) in messages"
            :class="{ 'd-flex flex-row-reverse': msg.me }"
            :key="i"
            class="mt-3"
          >
            <v-chip
              :color="msg.me ? 'orange' : 'grey'"
              dark
              style="height: auto; white-space: normal"
              class="pa-4 mb-2"
              v-on="on"
            >
              {{ msg.content }}
            </v-chip>

            <!-- show special menu -->
            <div v-if="msg.special_menu == true">
              <v-btn
                class="mx-1"
                small
                outlined
                rounded
                v-for="menu in menuSkripsi"
                :key="menu.code"
                @click="executeSkripsiMenu(menu.code)"
                >{{ menu.text }}
              </v-btn>
            </div>

            <!-- if message has image from menu -->
            <div v-if="msg.special_img">
              <v-img max-height="150" max-width="250" :src="msg.img"></v-img>
            </div>
            <!--  -->
          </div>
        </v-card-text>
        <v-card-text class="flex-shrink-1">
          <v-text-field
            v-model="messageForm.content"
            label="Tanya Kirana"
            type="text"
            no-details
            outlined
            append-outer-icon="mdi-send"
            hide-details
            @click:append-outer="sendMsg(messageForm.content)"
            @keyup.enter="sendMsg(messageForm.content)"
          />
        </v-card-text>
      </v-card>
    </div>
  </v-app>
</template>

<script>
import dum0 from "./assets/skripsi-0.png";
import dum1 from "./assets/skripsi-1.png";
import dum2 from "./assets/skripsi-2.png";
import ava from "./assets/ava.png";
export default {
  methods: {
    executeSkripsiMenu(code) {
      this.currentMood = this.moods.anget.name;
      this.messageForm.content = "";
      switch (code) {
        case "FAV":
          this.messages.push({
            me: false,
            content: "ini skripsi terfavorit di tahun ini",
            img: dum0,
            special_img: true,
          });
          this.$nextTick(() => {
            this.scrollBottom();
          });
          break;
        case "MOST":
          this.messages.push({
            me: false,
            content: "ini skripsi terbanyak di tahun ini",
            img: dum1,
            special_img: true,
          });
          this.$nextTick(() => {
            this.scrollBottom();
          });
          break;
        case "TREND":
          this.messages.push({
            me: false,
            content: "ini trend skripsi ",
            img: dum2,
            special_img: true,
          });
          this.$nextTick(() => {
            this.scrollBottom();
          });
          break;

        default:
          break;
      }
    },
    scrollBottom() {
      let x = document.getElementById("scrollable-chatlist");
      // x.scrollTop = x.scrollHeight + 10000;
      x.scrollTo(0, x.scrollHeight);
    },
    showSkripsiMenu() {
      this.messages.push({
        me: false,
        special_menu: true,
        content: "silahkan pilih layanan",
      });
    },
    sendMsg(msg) {
      if (msg == "/c") {
        this.showSkripsiMenu();
      } else if (msg == "pap") {
        this.messages.push({
          me: true,
          content: msg,
        });
        this.messages.push({
          me: false,
          content: "nihh",
          special_img: true,
          img: ava,
        });
        this.messages.push({
          me: false,
          content: 'Cakep kek pantun kan ?',
        });

        this.$nextTick(() => {
          this.scrollBottom();
        });
        this.messageForm.content = "";
      } else {
        this.messages.push({
          me: true,
          content: msg,
        });

        fetch("http://localhost:8080/chat", {
          method: "POST",
          body: JSON.stringify({ text: msg }),
          headers: { "Content-Type": "application/json" },
        })
          .then((r) => r.json())
          .then((d) => {
            // set kirana mood

            d?.response.length > 200
              ? (this.currentMood = this.moods.anget.name)
              : (this.currentMood = this.moods.normal.name);

            // remove prev msg
            this.messageForm.content = "";
            this.messages.push({
              me: false,
              content: d.response,
            });
            this.$nextTick(() => {
              this.scrollBottom();
            });

            // play voices
            // var voices = window.speechSynthesis.getVoices();
            // let msg = new SpeechSynthesisUtterance();
            // msg.voice = voices[156];
            // msg.text = d.response;
            // window.speechSynthesis.speak(msg);
          });
      }
    },
  },
  data: () => ({
    // sini
    currentMood: "normal",
    moods: {
      normal: {
        name: "normal",
        header: "purple",
        bg: "#cdd3f9",
      },
      anget: {
        name: "anget",
        header: "red",
        bg: "#d69992",
      },
      baper: {
        name: "baper",
        header: "pink",
        bg: "#f2aeed",
      },
    },
    menuSkripsi: [
      { code: "TREND", text: "trend topik skripsi" },
      { code: "FAV", text: "skripsi paling favorit" },
      { code: "MOST", text: "skripsi terbanyak" },
    ],
    messages: [
      {
        content: "Haloo, ada yang bisa kirana banting, eh bantu",
        me: false,
      },
    ],
    messageForm: {
      content: "",
      me: true,
    },
  }),
};
</script>

