<template>
  <b-container v-if="!isSelectedBestTen" fluid class="p-4 d-flex">
    <b-row class="justify-content-center">
      <b-col
          v-for="song in songsList"
          :key="song.id"
          cols="12" sm="4"
          class="mb-4"
          @click="clickedCard(song)"
      >
        <b-card
            :title="song.id + ': ' + song.title"
            :img-src='song.src !== "" ? song.src : noImage'
            img-alt="artwork"
            img-top
            tag="article"
            class="card-area"
            :class="selectedIdList.indexOf(song.id) !== -1 ? 'selected' : ''"
        >
          <div v-show="selectedIdList.indexOf(song.id) !== -1"
               class="text-center justify-content-center rank-cover">
            {{ selectedRankIdList.indexOf(song.id) + 1 }}</div>

          <div style="position: absolute; bottom: 10px; right: 10px">
            <b-icon v-if="song.id !== nowPlayingId" icon="play-circle-fill" font-scale="3" aria-hidden="true" @click.stop.prevent="playMusic(song)"></b-icon>
            <b-icon v-if="song.id === nowPlayingId" icon="stop-circle-fill" font-scale="3" aria-hidden="true" @click.stop.prevent="stopMusic()"></b-icon>
          </div>
        </b-card>
      </b-col>
      <b-col cols="12">
        <div class="music-player">
          <audio
              id="audio"
              ref="audio"
              :src="auditionSrc"
              preload
          ></audio>
        </div>
      </b-col>
    </b-row>
  </b-container>
  <b-container v-else class="p-4">
    <b-list-group>
      <b-list-group-item v-for="(song, index) in bestTenList" :key="song.id"
        class="d-flex justify-content-between align-items-center">{{ index + 1 + ': ' + song.title }}
          <b-btn variant="outline-primary" @click="copyToClipboard(song.title)" pill>コピー</b-btn>
      </b-list-group-item>
    </b-list-group>
    <div class="mt-4 text-center">
      <b-btn variant="outline-secondary" @click="returnBtn()" pill>戻る</b-btn>
    </div>
  </b-container>
</template>

<script>
import axios from 'axios';
// axios.defaults.baseURL = 'https://linq-best-ten.netlify.app/';
// axios.defaults.headers.get['Content-Type'] = 'application/json;charset=utf-8';
// axios.defaults.headers.get['Access-Control-Allow-Origin'] = '*';

export default {
  name: 'List',
  data() {
    return {
      bestTenList: [],
      isPlaying: false,
      isSelectedBestTen: false,
      nowPlayingId: null,
      auditionSrc: null,
      noImage: require('@/assets/noimage.png'),
      rankNumber: 1,
      selectedMax: 10,
      selectedIdList: [],
      selectedRankIdList: [],
      songsList: [
        {
          id : 1,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : 'ハジメマシテ'
        },
        {
          id : 2,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : 'for you'
        },
        {
          id : 3,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : 'Shining Star'
        },
        {
          id : 4,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music114/v4/82/95/ff/8295ffa7-ba7d-0500-dea2-1ae9ce6d738c/source/100x100bb.jpg",
          kana : "",
          title : 'なう。'
        },
        {
          id : 5,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music115/v4/4f/7a/c3/4f7ac39c-e791-f64e-f70c-e46562186968/source/100x100bb.jpg",
          kana : "",
          title : 'カロリーなんて'
        },
        {
          id : 6,
          src : "",
          kana : "",
          title : 'とんこつこつ'
        },
        {
          id : 7,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music2/v4/c0/a9/e6/c0a9e648-f48c-48f2-edb1-50d2ba355d19/source/100x100bb.jpg",
          kana : "",
          title : 'フクオカ好いとぉ'
        },
        {
          id : 8,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music125/v4/cf/fd/73/cffd7358-e733-f905-6862-bd85127c647e/source/100x100bb.jpg",
          kana : "",
          title : '祭りの夜～君を好きになった日～'
        },
        {
          id : 9,
          src : "",
          kana : "",
          title : 'Ice Queen of Love'
        },
        {
          id : 10,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/c2/e8/2e/c2e82e3f-10fc-7b21-6e64-595cf9a3404c/source/100x100bb.jpg",
          kana : "",
          title : 'シアワセのエナジー'
        },
        {
          id : 11,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music115/v4/4f/7a/c3/4f7ac39c-e791-f64e-f70c-e46562186968/source/100x100bb.jpg",
          kana : "",
          title : '手をつないで'
        },
        {
          id : 12,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music115/v4/7c/3e/96/7c3e96a8-c590-939c-d7a9-9e6fcf3d9a60/source/100x100bb.jpg",
          kana : "",
          title : 'ゴーイングマイウェイ！'
        },
        {
          id : 13,
          src : "",
          kana : "",
          title : '桜を見上げて'
        },
        {
          id : 14,
          src : "",
          kana : "",
          title : '虹'
        },
        {
          id : 15,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music115/v4/7c/3e/96/7c3e96a8-c590-939c-d7a9-9e6fcf3d9a60/source/100x100bb.jpg",
          kana : "",
          title : 'あの街の空'
        },
        {
          id : 16,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : 'HANABI!!'
        },
        {
          id : 17,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : '笑顔にLinQ'
        },
        {
          id : 18,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music4/v4/8b/13/fb/8b13fb58-d7c2-5961-3c3c-e383d8c98578/source/100x100bb.jpg",
          kana : "",
          title : '冒険'
        },
        {
          id : 19,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : 'GARNET'
        },
        {
          id : 20,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music2/v4/c0/a9/e6/c0a9e648-f48c-48f2-edb1-50d2ba355d19/source/100x100bb.jpg",
          kana : "",
          title : 'もっともっといつかきっと'
        },
        {
          id : 21,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music/v4/40/21/f6/4021f623-57fe-3af0-36f7-f49f013e69d3/source/100x100bb.jpg",
          kana : "",
          title : 'カラフルデイズ'
        },
        {
          id : 22,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music2/v4/c0/a9/e6/c0a9e648-f48c-48f2-edb1-50d2ba355d19/source/100x100bb.jpg",
          kana : "",
          title : 'カラフルデイズ（HAKATATrack）'
        },
        {
          id : 23,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : '未来日記'
        },
        {
          id : 24,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music4/v4/57/bf/12/57bf12ec-323a-9bb6-8ab5-e5cfd7d7fe2a/source/100x100bb.jpg",
          kana : "",
          title : 'ナツコイ'
        },
        {
          id : 25,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music4/v4/57/bf/12/57bf12ec-323a-9bb6-8ab5-e5cfd7d7fe2a/source/100x100bb.jpg",
          kana : "",
          title : 'シュワシュワシュワリ'
        },
        {
          id : 26,
          src : "",
          kana : "",
          title : 'Peace'
        },
        {
          id : 27,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : 'Wake up'
        },
        {
          id : 28,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music/v4/4c/48/29/4c482959-1c22-0d74-ff99-18ae80c0b136/source/100x100bb.jpg",
          kana : "",
          title : 'チャイムが終われば'
        },
        {
          id : 29,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music2/v4/c0/a9/e6/c0a9e648-f48c-48f2-edb1-50d2ba355d19/source/100x100bb.jpg",
          kana : "",
          title : '雨にぬれても'
        },
        {
          id : 30,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music124/v4/06/0c/43/060c43ec-2085-7fa6-8e3b-e8c195ebb3d2/source/100x100bb.jpg",
          kana : "",
          title : 'grown up'
        },
        {
          id : 31,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music2/v4/c0/a9/e6/c0a9e648-f48c-48f2-edb1-50d2ba355d19/source/100x100bb.jpg",
          kana : "",
          title : 'FRIEND'
        },
        {
          id : 32,
          src : "",
          kana : "",
          title : 'アオイハル'
        },
        {
          id : 33,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music4/v4/8b/13/fb/8b13fb58-d7c2-5961-3c3c-e383d8c98578/source/100x100bb.jpg",
          kana : "",
          title : 'ウェッサイ！！ガッサイ！！'
        },
        {
          id : 34,
          src : "",
          kana : "",
          title : 'マケナイガールズ'
        },
        {
          id : 35,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'ハピ☆デリ'
        },
        {
          id : 36,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'ハレハレ☆パレード'
        },
        {
          id : 37,
          src : "",
          kana : "",
          title : 'V to ROAD'
        },
        {
          id : 38,
          src : "",
          kana : "",
          title : 'bang bang LinQ Island'
        },
        {
          id : 39,
          src : "",
          kana : "",
          title : 'BATON'
        },
        {
          id : 40,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music30/v4/92/7c/41/927c41dd-de4d-5f18-c56f-861468274fc0/source/100x100bb.jpg",
          kana : "",
          title : 'LinQuest～やがて伝説へ…'
        },
        {
          id : 41,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'アイ、スクリーミン。'
        },
        {
          id : 42,
          src : "",
          kana : "",
          title : 'Jump Jump Jump!'
        },
        {
          id : 43,
          src : "",
          kana : "",
          title : 'あなたに'
        },
        {
          id : 44,
          src : "",
          kana : "",
          title : 'Baby'
        },
        {
          id : 45,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'マイ・フェイバリット・ソング'
        },
        {
          id : 46,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'White Drops'
        },
        {
          id : 47,
          src : "",
          kana : "",
          title : 'FRONTER'
        },
        {
          id : 48,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/c6/38/f7/c638f72a-3fd4-cb82-6f87-b0dcd4a17cff/source/100x100bb.jpg",
          kana : "",
          title : 'Supreme'
        },
        {
          id : 49,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/c6/38/f7/c638f72a-3fd4-cb82-6f87-b0dcd4a17cff/source/100x100bb.jpg",
          kana : "",
          title : 'Sweet Dream'
        },
        {
          id : 50,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/09/f5/98/09f59831-c346-07f4-4718-d74a9c3a129f/source/100x100bb.jpg",
          kana : "",
          title : 'ポ!ジ!ティブ!!'
        },
        {
          id : 51,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music115/v4/e8/5a/a9/e85aa966-aecc-2ff1-f3f4-ba6f088ef967/source/100x100bb.jpg",
          kana : "",
          title : 'キミがいたから'
        },
        {
          id : 52,
          src : "",
          kana : "",
          title : 'one love～キミへの贈り物～'
        },
        {
          id : 53,
          src : "",
          kana : "",
          title : 'ふるさとジャポン'
        },
        {
          id : 54,
          src : "",
          kana : "",
          title : 'Do! LinQ!!'
        },
        {
          id : 55,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music111/v4/2e/19/e3/2e19e311-8e30-cca4-a83e-f9727ecaa6aa/source/100x100bb.jpg",
          kana : "",
          title : '負けないぞ'
        },
        {
          id : 56,
          src : "",
          kana : "",
          title : '青春グラフティ'
        },
        {
          id : 57,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music111/v4/ba/a8/27/baa82728-df6c-84e6-f014-20b933855cca/source/100x100bb.jpg",
          kana : "",
          title : 'トレジャー'
        },
        {
          id : 58,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music122/v4/24/5e/3b/245e3bf3-0e4d-d3f6-19a7-f4c7c036a7dd/source/100x100bb.jpg",
          kana : "",
          title : 'スキヤーキが好きすぎる'
        },
        {
          id : 59,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music124/v4/0c/b2/64/0cb26455-f247-04e5-7321-a1f49c332aea/source/100x100bb.jpg",
          kana : "",
          title : 'ONE FOR ALL FOR ONE'
        },
        {
          id : 60,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/09/f5/98/09f59831-c346-07f4-4718-d74a9c3a129f/source/100x100bb.jpg",
          kana : "",
          title : 'Iam …'
        },
        {
          id : 61,
          src : "",
          kana : "",
          title : '失恋フォトグラフ'
        },
        {
          id : 62,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music62/v4/a3/14/6b/a3146bd7-79cd-a09e-4b05-25c6e70c8291/source/100x100bb.jpg",
          kana : "",
          title : 'あぁ情熱のバンバラヤー'
        },
        {
          id : 63,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/09/f5/98/09f59831-c346-07f4-4718-d74a9c3a129f/source/100x100bb.jpg",
          kana : "",
          title : 'SUMMER SWITCH'
        },
        {
          id : 64,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/06/c6/f7/06c6f7fa-ebc5-6c9d-9cc7-19f5517295c5/source/100x100bb.jpg",
          kana : "",
          title : '進め！少年少女'
        },
        {
          id : 65,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/06/c6/f7/06c6f7fa-ebc5-6c9d-9cc7-19f5517295c5/source/100x100bb.jpg",
          kana : "",
          title : '絶対！Alright!'
        },
        {
          id : 66,
          src : "https://is3-ssl.mzstatic.com/image/thumb/Music114/v4/9e/9a/84/9e9a84c2-2f5d-1d30-8715-15b8f9b2598d/source/100x100bb.jpg",
          kana : "",
          title : 'LOVE BOMB'
        },
        {
          id : 67,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/8f/be/9d/8fbe9d05-961a-e22e-b29a-bd9c3f534514/source/100x100bb.jpg",
          kana : "",
          title : '愛SCREAME'
        },
        {
          id : 68,
          src : "",
          kana : "",
          title : 'WavyHug'
        },
        {
          id : 69,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/8f/be/9d/8fbe9d05-961a-e22e-b29a-bd9c3f534514/source/100x100bb.jpg",
          kana : "",
          title : 'anytime'
        },
        {
          id : 70,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music124/v4/95/01/6d/95016ddd-cb54-ab16-934a-071b1f2b35ab/source/100x100bb.jpg",
          kana : "",
          title : 'ヒトリジメ'
        },
        {
          id : 71,
          src : "https://is3-ssl.mzstatic.com/image/thumb/Music114/v4/d7/df/9e/d7df9efd-d3b0-12bc-cc29-e50a1f16eb66/source/100x100bb.jpg",
          kana : "",
          title : 'Q＆A'
        },
        {
          id : 72,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music114/v4/79/01/09/790109bf-e8ac-4b50-5a38-1a36ac33e832/source/100x100bb.jpg",
          kana : "",
          title : 'RI・RI・RI'
        },
        {
          id : 73,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music124/v4/24/10/20/24102039-d637-64f7-59af-1e3893c21430/source/100x100bb.jpg",
          kana : "",
          title : '祭高音頭'
        },
        {
          id : 74,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music124/v4/a0/6d/ee/a06dee4a-87fb-9893-e2a0-861d04628c03/source/100x100bb.jpg",
          kana : "",
          title : '情熱☆サラマンダー'
        },
        {
          id : 75,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music115/v4/d0/b8/17/d0b817fd-670b-27f4-4e8d-fdebd878e52c/source/100x100bb.jpg",
          kana : "",
          title : '15センチの恋'
        },
        {
          id : 76,
          src : "",
          kana : "",
          title : 'FUKUOKA。'
        },
        {
          id : 77,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : "Let's feel Together"
        },
        {
          id : 78,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : 'Fighting Girl'
        },
        {
          id : 79,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music2/v4/41/7b/20/417b208a-afd8-bbec-5914-02b32b31b11e/source/100x100bb.jpg",
          kana : "",
          title : 'Pretty Woman'
        },
        {
          id : 80,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music125/v4/bc/08/0b/bc080b81-286c-03f5-75fd-8c2bd994876d/source/100x100bb.jpg",
          kana : "",
          title : '夏Magic'
        },
        {
          id : 81,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music115/v4/c4/4d/57/c44d57b7-1972-33f8-d424-dcc018b4b634/source/100x100bb.jpg",
          kana : "",
          title : 'Sakura物語'
        },
        {
          id : 82,
          src : "",
          kana : "",
          title : 'さよならマーメイド'
        },
        {
          id : 83,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music/v4/4c/48/29/4c482959-1c22-0d74-ff99-18ae80c0b136/source/100x100bb.jpg",
          kana : "",
          title : 'Lie'
        },
        {
          id : 84,
          src : "",
          kana : "",
          title : 'LaLaLa(Ladyversion)'
        },
        {
          id : 85,
          src : "",
          kana : "",
          title : 'No Lady, No Life'
        },
        {
          id : 86,
          src : "",
          kana : "",
          title : 'Bye-bye baby love'
        },
        {
          id : 87,
          src : "",
          kana : "",
          title : 'オリオンのキセキ'
        },
        {
          id : 88,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'My Letter'
        },
        {
          id : 89,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/09/f5/98/09f59831-c346-07f4-4718-d74a9c3a129f/source/100x100bb.jpg",
          kana : "",
          title : 'CHOKICHOKI'
        },
        {
          id : 90,
          src : "",
          kana : "",
          title : 'きもち'
        },
        {
          id : 91,
          src : "https://is5-ssl.mzstatic.com/image/thumb/Music115/v4/c4/4d/57/c44d57b7-1972-33f8-d424-dcc018b4b634/source/100x100bb.jpg",
          kana : "",
          title : 'さくら果実'
        },
        {
          id : 92,
          src : "https://is4-ssl.mzstatic.com/image/thumb/Music115/v4/7c/3e/96/7c3e96a8-c590-939c-d7a9-9e6fcf3d9a60/source/100x100bb.jpg",
          kana : "",
          title : 'CHIKU-TAKU'
        },
        {
          id : 93,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music/v4/4c/48/29/4c482959-1c22-0d74-ff99-18ae80c0b136/source/100x100bb.jpg",
          kana : "",
          title : '全力Everyday'
        },
        {
          id : 94,
          src : "",
          kana : "",
          title : 'LaLaLa(Qtyversion)'
        },
        {
          id : 95,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music/v4/40/21/f6/4021f623-57fe-3af0-36f7-f49f013e69d3/source/100x100bb.jpg",
          kana : "",
          title : 'Chocolate Kiss'
        },
        {
          id : 96,
          src : "https://is2-ssl.mzstatic.com/image/thumb/Music19/v4/02/93/2b/02932bda-ef01-2ebd-f936-fc80d08abc5c/source/100x100bb.jpg",
          kana : "",
          title : 'telephone'
        },
        {
          id : 97,
          src : "",
          kana : "",
          title : 'DingDong'
        },
        {
          id : 98,
          src : "https://is1-ssl.mzstatic.com/image/thumb/Music114/v4/09/f5/98/09f59831-c346-07f4-4718-d74a9c3a129f/source/100x100bb.jpg",
          kana : "",
          title : 'イツレン'
        },
        {
          id : 99,
          src : "",
          kana : "",
          title : 'SunSunRise!!'
        },
        {
          id : 100,
          src : "",
          kana : "",
          title : '人生最強・花景色'
        },
      ],
    };
  },
  methods: {
    clickedCard(song) {
      // 既に選択されていた場合、選択を解除する
      if (this.selectedIdList.includes(song.id)) {
        this.rankNumber--;

        // 選択済みIDリストから削除する
        const index = this.selectedIdList.indexOf(song.id);
        this.selectedIdList.splice(index, 1)

        // 曲IDのインデックスで入れていたランキングナンバーを削除する
        const valIndex = this.selectedRankIdList.indexOf(song.id);
        this.selectedRankIdList.splice(valIndex, 1);
      } else {
        if (this.selectedIdList.length >= this.selectedMax) return false;
        this.selectedRankIdList.push(song.id);
        this.selectedIdList.push(song.id);
        this.rankNumber++;
      }

      if (this.rankNumber > this.selectedMax) {
        this.isSelectedBestTen = true;
        this.bestTenList = [];
        this.selectedRankIdList.forEach((id) => {
          const song = this.songsList.filter(e => e.id === id);
          if (song.length > 0) {
            this.bestTenList.push({
              id: song[0].id,
              title: song[0].title,
              src: song[0].src,
            });
          }
        });
        // console.log(this.bestTenList);
      }
    },

    playMusic(song) {
      // const config = {
      //   headers: {
      //     'Accept': 'application/json',
      //   }
      // }
      axios.get(`https://itunes.apple.com/search?term=LinQ+${song.title}&country=JP&lang=ja_jp&media=music&entity=song&limit=1`)
        .then((res) => {
          // console.log(res);
          // console.log(res.data.results[0].previewUrl);
          if (res.data.resultCount > 0) {
            if (res.data.results[0].previewUrl !== undefined) {
              this.auditionSrc = res.data.results[0].previewUrl;
            } else {
              this.auditionSrc = "";
              this.$swal('試聴データが存在しませんでした');
              this.stopMusic();
            }
          } else {
            this.auditionSrc = "";
            this.$swal('曲データが見つかりませんでした');
            this.stopMusic();
          }
        }).then(() => {
          if (this.auditionSrc !== "") {
            this.toggleSound(song.id);
          }
        });
    },

    stopMusic() {
      const audio = this.$refs.audio;
      audio.pause();
      this.isPlaying = false;
      this.nowPlayingId = null;
    },

    toggleSound(songId) {
      const audio = this.$refs.audio;
      if (audio.paused) {
        audio.play();
        this.isPlaying = true;
        this.nowPlayingId = songId;
        document.querySelector(".toggle-sound").classList.remove("paused");
      } else {
        audio.pause();
        this.isPlaying = false;
        this.nowPlayingId = null;
        document.querySelector(".toggle-sound").classList.add("paused");
      }
    },

    returnBtn() {
      this.isSelectedBestTen = false;
    },

    copyToClipboard(text) {
      navigator.clipboard.writeText(text)
        .then(() => {
          // console.log("copied!")
        })
        .catch(e => {
          // console.error(e)
        });
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

.selected img {
  opacity: 0.4;
  background-color: black;
  border: #6591ee solid 8px;
}

.rank-cover {
  top: 35%;
  left: 0;
  position: absolute;
  font-size: 8em;
  color: #6591ee;
  height: 100%;
  width: 100%;
}

.music-player {
  background-color: beige;
  height: 80px;
  width: 200px;
}
</style>