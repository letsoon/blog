<template>
  <div class="container">
    <div class="lyric">
      <ul :style="{marginTop:-index*25+'px'}">
        <li v-for="(item, index) in lyric"
        :key="item.time"
        :class="current(index)"
        >
          {{item.content}}
        </li>
      </ul>
    </div>
    <div class="player">
      <audio :src="url" controls ref="music" autoplay></audio>
    </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  name: 'Music',
  data() {
    return {
      lyric: '',
      url: '',
      time: 0,
      index: 0,
    };
  },
  methods: {
    current(index) {
      if (index === this.lyric.length - 1 && this.time > this.lyric[index].time) {
        this.index = index;
        return 'current';
      }
      if (this.time > this.lyric[index].time && this.time < this.lyric[index + 1].time) {
        this.index = index;
        return 'current';
      }
      return 'nocurrent';
    },
  },
  created() {
    axios({
      method: 'get',
      url: 'http://localhost:3000/song/url?id=27871276',
    }).then((res) => {
      this.url = res.data.data[0].url;
      this.$refs.music.play();
      setInterval(() => {
        this.time = this.$refs.music.currentTime * 1000;
      }, 10);
    });
    axios({
      method: 'get',
      url: 'http://localhost:3000/lyric?id=27871276',
    }).then((res) => {
      let { lyric } = res.data.lrc;
      lyric = lyric.replace(/[\s\S]+\[00:00.000\]/, '');
      lyric = lyric.split('\n');
      lyric = lyric.slice(-(lyric.length), -1);
      // eslint-disable-next-line prefer-const
      let index = [];
      lyric = lyric.map((item) => {
        // eslint-disable-next-line no-useless-escape
        let time = item.match(/\d+\:\d+\.\d+/) ? item.match(/\d+\:\d+\.\d+/)[0] : '';
        if (time) {
          const temp = time.split(':');
          time = temp[0] * 60000 + temp[1] * 1000;
          index.push(time);
        }
        // eslint-disable-next-line no-useless-escape
        return item.replace(/\[\S+\]/, '');
        // return item;
      });
      const lyrics = [];
      for (let i = 0; i < index.length; i += 1) {
        const item = { time: index[i], content: lyric[i] };
        lyrics.push(item);
      }
      this.lyric = lyrics;
    });
  },
};
</script>
<style lang="scss" scoped>
.lyric{
  overflow: hidden;
  height: 500px;
}
ul{
  padding:100px;
  transition: margin-top 0.3s ease;
  list-style: none;
  li{
    padding:5px 0;
  }
}
.current{
  color:red;
}
.player{
  margin-top:20px;
}
</style>
