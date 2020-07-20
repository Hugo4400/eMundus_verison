<template>
  <div id="app">
    <div class="number">{{major}}</div>:<div class="number">{{minor}}</div>:<div class="number">{{patch}}</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Axios from 'axios';

interface Obj {
  url: string;
}

interface Tag {
  commit: Obj;
  name: string;
}

@Component
export default class App extends Vue {
  major = '0'

  minor = '0'

  patch = '0'

  mounted() {
    this.updateNumbers();
    setInterval(this.updateNumbers, 100000);
  }

  updateNumbers() {
    let latestDate = new Date('2018-10-24T12:39:41Z');
    let commitDate = new Date(0);

    Axios.get('https://api.github.com/repos/emundus/v6/tags')
      .then(res => {
        if (res.status === 200) {
          res.data.forEach((tag: Tag) => {
            Axios.get(tag.commit.url)
              .then(commit => {
                if (commit.status === 200) {
                  commitDate = new Date(commit.data.commit.author.date);
                  if (commitDate > latestDate) {
                    latestDate = commitDate;
                    [this.major, this.minor, this.patch] = tag.name.split('.');
                  }
                }
              });
            return true;
          });
        }
      });
  }
}
</script>

<style lang="scss">
  * {
    font-size: 30vh;
  }

  #app {
    font-family: Lato, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .number {
    color: #de6339;
  }
</style>
