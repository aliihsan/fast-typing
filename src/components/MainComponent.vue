<template>
  <div class="container jumbotron">
    <h1 class="mt-2 mb-3">
      <a href="./index.html">Kendini test et</a>
    </h1>
    <div class="alert alert-primary" v-if="isFinish">
      <h1>Süre Bitti!</h1>
      <h4>Girilen Kelime Sayısı: {{dks}}</h4>
      <h4>Başarı: %{{dogrulukYuzdesi.toFixed(0)}}</h4>
      <h4>Doğru: {{trueCount}}</h4>
      <h4>Yanlış: {{falseCount}}</h4>
      <button class="btn btn-success mt-2" @click="newGame">Yeni Oyun</button>
    </div>
    <div v-else>
      <div class="card">
        <div class="card-body">
          <span :key="key" v-for="(word, key) in words.filter((data, index)=>index < 5)" v-bind:class="key!=0 || writingWordControl" class="m-2 words">{{word}}</span>
        </div>
      </div>
      <div class="card">
        <div class="card-body bg-secondary">
          <div class="input-group input-group-lg">
            <input type="text" class="form-control" v-model="writingWord" placeholder="Kelime yazın...">
            <button class="btn btn-light" type="button" disabled>{{timer}} sn.</button>
            <button class="btn btn-light" type="button" @click="getWords" :disabled="isRunning">Yenile</button>
          </div>
        </div>
      </div>
    </div>
    <div class="alert alert-primary mt-3" v-if="!isRunning">
      <ol class="m-0">
        <li>Yazmaya başladığınızda süre başlar.</li>
        <li>Kelime onayı <b>Space (boşluk)</b> tuşu ile yapılır.</li>
      </ol>
    </div>
  </div>
</template>
<script>
import wordList from '@/words.json'
export default {
  data () {
    return {
      words: [],
      writingWord: '',
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 60,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList
    }
  },
  mounted () {
    this.getWords()
  },
  watch: {
    writingWord (val) {
      if (!val || val === ' ') {
        this.writingWord = ''
        return
      }
      this.isRunning || this.toggleTimer()
      const word = this.words[0].slice(0, val.length)
      const userWord = val.trim()
      this.isTrue = word === userWord

      if (val.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writingWord = ''
        this.isTrue = true
      }
    }
  },
  computed: {
    writingWordControl () {
      return this.isTrue ? 'writing-word' : 'writing-word bg-danger'
    },
    dks () {
      return this.trueCount + this.falseCount
    },
    dogrulukYuzdesi () {
      const val = this.trueCount / this.dks * 100
      return !isNaN(val) ? val : 0
    }
  },
  methods: {
    newGame () {
      this.getWords()
      this.isFinish = false
      this.timer = 60
      this.isTrue = true
      this.isRunning = false
      this.writingWord = ''
    },
    toggleTimer () {
      this.isRunning = true
      this.interval = setInterval(this.timeProcess, 1000)
    },
    timeProcess () {
      if (this.timer === 0) {
        clearInterval(this.interval)
        this.isFinish = true
        return
      }
      this.timer--
    },
    getWords () {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    }
  }
}

</script>
<style scoped>
.words{
  font-size: 25px;
}
.writing-word{
  background-color: gray;
  color: white;
  padding: 5px;
  border-radius: 5px;
}
</style>
