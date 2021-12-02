<template>
  <b-container fluid class="p-4 d-flex">
    <b-row class="justify-content-center">
      <b-col
          v-for="(title, index) in musicTitleList"
          :key="title"
          cols="12" sm="4"
          @click="clickedCard(index)"
      >
        <b-card
            :title="title"
            img-src="https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg"
            img-alt="Image"
            img-top
            tag="article"
            class="mb-2 card-area"
            :class="selectedList.indexOf(index) !== -1 ? 'selected' : ''"
        >
          <b-card-text class="text-truncate">
          </b-card-text>

          <b-button href="#" variant="primary"
              @click="playMusic(title)"
          >聴く</b-button>
        </b-card>
        <div v-show="selectedList.indexOf(index) !== -1"
             class="text-center justify-content-center"
             style="top: 40%; left: 0; position: absolute; font-size: 6em; height: 100%; width: 100%">
          {{ selectedCntList[index] }}</div>
      </b-col>
      <b-col cols="12">
        <div class="music-player">
          <audio
              id="audio"
              ref="audio"
              :src="auditionSrc"
              preload
          ></audio>
          <b-btn class="toggle-sound" @click="toggleSound()">
          </b-btn>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import axios from 'axios';
// import * as store from 'core-js';

export default {
  name: 'List',
  data() {
    return {
      auditionSrc: null,
      deletedIndex: null,
      selectedCnt: 1,
      selectedCntList: [],
      selectedList: [],
      musicTitleList: [
        'ハジメマシテ',
        'for you', 'Shining Star', 'なう。', 'カロリーなんて', 'とんこつこつ', 'フクオカ好いとぉ',
        '祭りの夜 ～君を好きになった日～', 'Ice Queen of Love', 'シアワセのエナジー',
        '手をつないで', 'ゴーイング マイ ウェイ！', '桜を見上げて', '虹', 'あの街の空',
        'HANABI!!', '笑顔にLinQ', '冒険', 'GARNET', 'もっともっといつかきっと',
        'カラフルデイズ', 'カラフルデイズ（HAKATA Track）', '未来日記', 'ナツコイ',
        'シュワシュワシュワリ', 'Peace', 'Wake up', 'チャイムが終われば', '雨にぬれても', 'grown up',
        'FRIEND', 'アオイハル', 'ウェッサイ！！ガッサイ！！', 'マケナイガールズ', 'ハピ☆デリ',
        'ハレハレ☆パレード', 'V to ROAD', 'bang bang LinQ Island', 'BATON', 'LinQuest～やがて伝説へ…',
        'アイ、スクリーミン。', 'Jump Jump Jump!', 'あなたに', 'Baby', 'マイ・フェイバリット・ソング',
        'White Drops', 'FRONTER', 'Supreme', 'Sweet Dream', 'ポ!ジ!ティブ!!', 'キミがいたから',
        'one love～キミへの贈り物～', 'ふるさとジャポン', 'Do! LinQ!!', '負けないぞ',
        '青春グラフティ', 'トレジャー', 'スキヤーキが好きすぎる', 'ONE FOR ALL FOR ONE',
        'I am …', '失恋フォトグラフ', 'あぁ情熱のバンバラヤー', 'SUMMER SWITCH',
        '進め！少年少女', '絶対！Alright!', 'LOVEBOMB', '愛SCREAME', 'WavyHug', 'anytime',
        'ヒトリジメ', 'Q＆A', 'RI・RI・RI', '祭高音頭', '情熱☆サラマンダー', '15センチの恋',
        'FUKUOKA。', 'Let\'s feel Together', 'Fighting Girl', 'Pretty Woman', '夏Magic',
        'Sakura物語', 'さよならマーメイド', 'Lie', 'LaLaLa(Ladyversion)', 'No Lady,No Life',
        'Bye-bye baby love', 'オリオンのキセキ', 'My Letter', 'CHOKICHOKI', 'きもち',
        'さくら果実', 'CHIKU-TAKU', '全力Everyday', 'LaLaLa(Qtyversion)', 'Chocolate Kiss',
        'telephone', 'Ding Dong', 'イツレン', 'Sun Sun Rise!!', '人生最強・花景色',
      ],
      artist: "LinQ",
      term: "LinQ",
    };
  },
  mounted() {
    const { data } = axios.get(`//itunes.apple.com/search?term=${this.term}&country=JP&lang=ja_jp&media=music&entity=song&limit=30`);
    console.log(data);

    // this.response = response.data.results;
    // this.$store.commit(JSON.stringify(response))
    // store.subscribe((mutation, state) => {
      // Store the state object as a JSON string
      // localStorage.setItem('store', JSON.stringify(state));
    // });
  },
  methods: {
    clickedCard(index) {
      if (this.selectedList.includes(index)) {
        this.selectedCnt--;
        const valIndex = this.selectedList.indexOf(index);
        this.selectedList.splice(valIndex, 1)
        this.selectedCntList.forEach((cnt, i) => {
          // if (cnt > this.selectedCnt) {
          //   this.selectedCntList[index] = this.selectedCntList[index]--;
          // }
          if (cnt === this.selectedCnt) {
            // console.log('delete');
            // this.selectedCntList[index] = "";
            // const valIndex = this.selectedCntList.indexOf(cnt);
            // this.selectedCntList.splice(index, 1);
            this.deletedIndex = this.selectedCntList[index];
            delete this.selectedCntList[index];
          }
        });
        this.selectedCntList.forEach((cnt, i) => {
          // console.log(i + "/" + cnt);
          if (this.deletedIndex < cnt) {
            this.selectedCntList[cnt] = cnt-1;
          }
        });
        // console.log(this.deletedIndex);
        // console.log(this.selectedCnt);
        // console.log(this.selectedList);
        console.log(this.selectedCntList);
        } else {
        this.selectedCntList[index] = this.selectedCnt;
        this.selectedList.push(index);
        this.selectedCnt++;
      }
    },

    playMusic(musicTitle) {
      const config = {
        headers: {
          'Accept': 'application/json'
        }
      }
      axios.get(`https://itunes.apple.com/search?term=LinQ+${musicTitle}&country=JP&lang=ja_jp&media=music&entity=song&limit=1`, config)
        .then((res) => {
          console.log(res);
        // console.log(res.data.results[0].previewUrl);
        if (res.data.resultCount > 0) {
          if (res.data.results[0].previewUrl !== undefined) {
            this.auditionSrc = res.data.results[0].previewUrl;
          } else {
            // TODO ありません
            this.auditionSrc = "";
          }
        } else {
          // TODO 曲が見つかりません
          this.auditionSrc = "";
        }
      }).then(() => {
        this.toggleSound();
      });
    },

    toggleSound() {
      const audio = this.$refs.audio;
      if (audio.paused) {
        console.log("play it")
        console.log(this.auditionSrc);
        audio.play();
        document.querySelector(".toggle-sound").classList.remove("paused");
      } else {
        console.log("pause it")
        audio.pause();
        document.querySelector(".toggle-sound").classList.add("paused");
      }
    },
  }
};
</script>

<style scoped>
.card-area {
  cursor: pointer;
}
.card-title {
  font-size: 1em;
}
.selected {
  opacity: 0.6;
  background-color: gray;
}

.music-player {
  background-color: beige;
  height: 80px;
  width: 200px;
}
</style>