<template>
  <div class="word ui container">
    <div v-if="!err">
      <div v-for = "(b, idx) in bs" :key = "idx">
        <div v-if="idx == 0">
          <span v-for = "(y, i) in yindiao(w, bs[idx], data.h[idx].p, data.h[idx].T)" :key="i">
            <h1 class="stroke">{{ y.w }}</h1>
            <span id ="b">
              <span class="yindiao">
                <span class = "yin stroke"> {{y.yin}} </span>
                <span class = "diao stroke"> {{y.diao}} </span>
              </span>
            </span>
          </span>

          <audio id="au" v-if = "data.h[idx]['=']">
            <source :src="'https://203146b5091e8f0aafda-15d41c68795720c6e932125f5ace0c70.ssl.cf1.rackcdn.com/' + data.h[idx]['='] + '.mp3'" type="audio/mp3"/>
            <source :src="'https://203146b5091e8f0aafda-15d41c68795720c6e932125f5ace0c70.ssl.cf1.rackcdn.com/' + data.h[idx]['='] + '.ogg'" type="audio/mp3"/>
            <source :src="'https://a7ff62cf9d5b13408e72-351edcddf20c69da65316dd74d25951e.ssl.cf1.rackcdn.com/1-' + data.h[idx]['='] + '.mp3'" type="audio/mp3"/>
            <source :src="'https://a7ff62cf9d5b13408e72-351edcddf20c69da65316dd74d25951e.ssl.cf1.rackcdn.com/1-' + data.h[idx]['='] + '.ogg'" type="audio/mp3"/>
          </audio>

          <a name ="play" id = "play" @click = "play()" v-if = "data.h[idx]['='] || data.h[idx]['_']">
            <span name="play_arrow" v-if="!playing"><i class="play icon"/></span>
            <span name="pause" v-else><i class="pause icon"/></span>
          </a>
        </div>

      </div>
    </div>
    <form class="ui form">
      <div class="two fields">
        <div class="field">
          <input type="text" name="" v-model="ans" placeholder="輸入注音" @keyup.enter="check()"/>
        </div>
        <div class="left field">
          <button @click="check()">送出</button>
        </div>
      </div>

      <div class="three fields">
        <div class="field">
          子音:
          <select name="" v-model="z">
            <option value="">--選擇並加入--</option>
            <option v-for ="o in ziying" :key="o">{{o}}</option>
          </select>
          <button @click="addZ()">加入</button>
        </div>
        <div class="field">
          母音:
          <select name="" v-model="m">
            <option value="">--選擇並加入--</option>
            <option v-for ="o in muying" :key="o">{{o}}</option>
          </select>

          <button @click="addM()">加入</button>
        </div>
        <div class="field">
          聲調:
          <select name="" v-model="d">
            <option value="">--選擇並加入--</option>
            <option v-for ="o in diao" :key="o">{{o}}</option>
          </select>

          <button @click="addD()">加入</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
// eslint-disable-next-line
export default {
  name: 'Words',
  components: { },
  data () {
    return {
      showBox: false,
      z: '',
      m: '',
      d: '',
      ziying: ['ㄅ','ㄆ','ㄇ','ㄈ','ㄉ','ㄊ','ㄋ','ㄌ', 'ㄍ','ㄎ','ㄏ','ㄐ','ㄑ','ㄒ', 'ㄓ','彳','ㄕ','ㄖ','ㄗ','ㄘ','厶'],
      muying: [ 'ㄚ','ㄛ','ㄜ','ㄝ','ㄞ','ㄟ','ㄠ','ㄡ',
              'ㄢ','ㄣ','ㄤ','ㄥ','ㄦ','ㄧ','ㄨ','ㄩ'],
      diao: ['ˊ', 'ˇ', 'ˋ', '˙'],
      boxW: '萌',
      t: 0,
      l: 0,
      moes: [],
      w: '',
      bs: [],
      data: null,
      playing: false,
      pre: '',
      title: '萌典',
      url: 'a',
      ans: '',
      err: false,
      num: 0,
      progress: [],
      showDraw: false,
      idx: 0
    }
  },
  props: [],
  meta () {
    return {
      // this accesses the "title" property in your Vue "data";
      // whenever "title" prop changes, your meta will automatically update
      title: this.w + ' - ' + this.title
    }
  },
  mounted () {
    // console.log('m')
    this.set()
    setTimeout(this.startDraw, 1000)
  },
  methods: {
    randomRoute () {
      var p = ''
      var u = 'a'
      this.pre = p
      this.url = u
      console.log(this.url)
      this.$http.get('' + this.url + '/index.json')
        .then((response) => {
          this.data = response.data.filter(function (o) { return o.length == 1
          })
          const r = this.data[Math.floor(Math.random() * this.data.length)]
          this.$router.push('/word/' + this.pre + r)
        })
    },
    addZ() { 
      this.ans = this.ans + this.z
      this.z = ''
    },
    addM() {
      this.ans = this.ans + this.m
      this.m = ''
    },
    addD() {
      this.ans = this.ans + this.d
      this.d = ''
    },
    check: function () {
      console.log(this.data.h[0].b)
      if (this.ans == this.data.h[0].b) {
        alert('對了')
        this.ans = ''
        this.win = true
        this.randomRoute()
      } else {
        alert('不對喔')
      }
    },
    bucketOf: function (it) {
      console.log(it)
      var code
      if (/^[=@]/.exec(it)) {
        return it[0]
      }
      code = it.charCodeAt(0)
      if (code >= 0xD800 && code <= 0xDBFF) {
        code = it.charCodeAt(1) - 0xDC00
      }
      return code % (this.url === 'a' ? 1024 : 128)
    },
    fillBucket: function (id, bucket) {
      var vm = this
      this.$http.get('p' + this.url + 'ck/' + bucket + '.txt').then((d) => {
        console.log(d)
        if (d) {
          var key = escape(id)
          console.log(key)
          var part = d.data[key]
          console.log(part)
          this.data = part
          console.log(this.data)
          if (this.data) {
            this.bs = this.data.h.map((o) => { return o.b || '' })
          }
          this.playing = false
          // addToLru(id)
        } else {
          vm.$http.get('p' + vm.url + 'ck/' + bucket + '.txt').then((response) => {
            // console.log(response.data)
            /* var raw = JSON.stringify(response.data)
            var key, idx, part
            key = escape(id)
            idx = raw.indexOf('"' + key + '"')
            if (idx === -1) {
              return
            }
            part = raw.slice(idx + key.length + 3)
            idx = part.indexOf('\n')
            part = part.slice(0, idx) */
            var key = escape(id)
            var part = response.data[key]
            // console.log(part)
            vm.data = part
            // console.log(this.data)
            if (vm.data) {
              vm.bs = vm.data.h.map((o) => { return o.b || '' })
            }
            vm.playing = false
            // addToLru(id)
          }).catch(err => {
            vm.err = true
            console.log(err)
          })
        }
      })
    },
    closeD () {
      this.$emit('closeD')
    },
    set (k) {
      this.w = k || this.$route.params.id
      this.pre = ''
      this.url = 'a'
      this.title = '萌典'
      if (this.w.match(/^(~)/)) {
        this.pre = '~'
        this.url = 'c'
        this.title = '兩岸萌典'
        this.w = this.w.replace('~', '')
      }
      if (this.w.match(/^(')/)) {
        this.pre = '\''
        this.url = 't'
        this.title = '台灣閩南語'
        this.w = this.w.replace('\'', '')
      }
      if (this.w.match(/^(:)/)) {
        this.pre = ':'
        this.url = 'h'
        this.title = '台灣客家語'
        this.w = this.w.replace(':', '')
      }
      /*
      this.$http.get('https://www.moedict.tw/' + this.url + '/' + this.w + '.json')
        .then((response) => {
          this.err = false
          this.data = response.data
          // console.log(this.data)
          this.bs = this.data.h.map((o) => { return o.b || '' })
          this.playing = false
        }).catch(err => {
          this.err = true
          console.log(err)
        })
      this.$forceUpdate() */
      // console.log(this.w)
      // console.log(this.bucketOf(this.w))
      this.fillBucket(this.w, this.bucketOf(this.w))
      var vm = this
      // console.log(this.w)
      var list = vm.w.split('')
      this.progress = list.map(() => { return 0 })
      this.idx = 0
    },
    play () {
      if (!this.playing) {
        document.getElementById('au').load()
        document.getElementById('au').play()
        this.playing = true
      } else {
        document.getElementById('au').pause()
        this.playing = false
      }
    },
    yindiao (w, b, p, T) {
      var word = w
      var arr
      var ts = []
      var ps = []
      var ws
      if (b) {
        ws = ('' + w).split('')
        arr = ('' + b).split('　')
        ps = p.split(' ')
        if (T) {
          ts = T.split(' ')
        }
        if (ws.indexOf('，') > -1) {
          arr.splice(ws.indexOf('，'), 0, '')
          ps.splice(ws.indexOf('，'), 0, '&nbsp;&nbsp;&nbsp;&nbsp;')
          if (T) { ts.splice(ws.indexOf('，'), 0, '&nbsp;&nbsp;&nbsp;&nbsp;') }
        }
        // console.log(ps)
        return arr.map((k, idx) => {
          k = k.replace(/（.+）/g, '').replace('ㄧ', '─')
          var obj = {
            w: word[idx],
            yin: k.substr(0, k.length - 1),
            diao: k.substr(k.length - 1, k.length),
            pin: ps[idx],
            T: ts[idx]
          }
          if (obj.diao !== 'ˋ' && obj.diao !== 'ˊ' && obj.diao !== 'ˇ' && obj.diao !== 'ˊ') {
            obj.yin = obj.yin + obj.diao
            obj.diao = ''
          }
          return obj
        })
      } else {
        ws = ('' + w).split('')
        arr = ('' + b).split('　')
        if (T) {
          ts = T.split(' ')
        }
        if (p) {
          ps = p.replace('四⃞', '四')
            .replace('海⃞', '海')
            .replace('海⃞', '海')
            .replace('大⃞', '大')
            .replace('平⃞', '平')
            .replace('安⃞', '安')
            .replace('南⃞', '南').split(' ')
        }
        if (ws.indexOf('，') > -1) {
          arr.splice(ws.indexOf('，'), 0, '')
          ps.splice(ws.indexOf('，'), 0, '&nbsp;&nbsp;&nbsp;&nbsp;')
          if (T) { ts.splice(ws.indexOf('，'), 0, '&nbsp;&nbsp;&nbsp;&nbsp;') }
        }
        return w.split('').map((k, idx) => {
          k = k.replace(/（.+）/g, '').replace('ㄧ', '─')
          var obj = {
            w: word[idx],
            pin: (idx === 0 ? ps.join('') : ''),
            T: ts[idx]
          }
          return obj
        })
      }
    },
    dis (w) {
      return w.match(/(\s|⚋|⚊|☰|☱|☲|☳|☴|☵|☶|☷|灾|从|0|1|2|3|4|5|6|7|8|9|：|《|》|〈|〉|!|-|"￹|É|é|Ɨ|ɨ|Ṟ|ṟ|Ʉ|ʉ|’O͘|Ò͘|Ô͘|Ǒ͘|Ō͘|O̍͘|Ő͘|Ŏ͘|Á|Í|Ú|É|À|Ì|Ù|È|Â|Î|Û|Ê|Ǎ|Ǐ|Ǔ|Ě|Ā|Ī|Ū|Ē|A̍|I̍|U̍|E̍|A̋|I̋|Ű|Ă|Ĭ|Ŭ|Ĕ|Ḿ|Ń|M̀|Ǹ|M̂|N̂|M̌|Ň|M̄|N̄|M̍|N̍|M̋|N̋|M̆|N̆|á|í|ú|é|ó|ó͘|ḿ|ń|à|ì|ù|è|ò|ò͘|m̀|ǹ|â|î|û|ê|ô|ô͘|m̂|n̂|ǎ|ǐ|ǔ|ě|ǒ|ǒ͘|m̌|ň|ā|ī|ū|ē|ō|ō͘|m̄|n̄|a̍|i̍|u̍|e̍|o̍|o̍͘|m̍|n̍|a̋|i̋|ű|e̋|ő|ő͘|m̋|n̋|ă|ĭ|ŭ|ĕ|ŏ|ŏ͘|m̆|n̆|ⁿ|ˆ|ˇ|ˊ|ˋ|⁺|[a-z]|[A-Z]|．|、|。|；|「|」|『|』|（|）|\(|\)|，|,|`(.+?)~)/)
    },
    p (s) {
      var a = s
        .replace(/{\[8ebc\]}/g, '⚋')
        .replace(/{\[8ebd\]}/g, '⚊')
        .replace(/{\[8e79\]}/g, '☰')
        .replace(/{\[8e7b\]}/g, '☱')
        .replace(/{\[8e7c\]}/g, '☲')
        .replace(/{\[8e7e\]}/g, '☳')
        .replace(/{\[8e7d\]}/g, '☴')
        .replace(/{\[8ea1\]}/g, '☵')
        .replace(/{\[8ea2\]}/g, '☶')
        .replace(/{\[8e7a\]}/g, '☷')
        .replace(/{\[9264\]}/g, '灾')
        .replace(/{\[9064\]}/g, '从')
        .replace(/\s/g, '')
      var arr = [...a.matchAll(/(\s|⚋|⚊|☰|☱|☲|☳|☴|☵|☶|☷|灾|从|0|1|2|3|4|5|6|7|8|9|：|《|》|〈|〉|!|-|"￹|É|é|Ɨ|ɨ|Ṟ|ṟ|Ʉ|ʉ|’O͘|Ò͘|Ô͘|Ǒ͘|Ō͘|O̍͘|Ő͘|Ŏ͘|Á|Í|Ú|É|À|Ì|Ù|È|Â|Î|Û|Ê|Ǎ|Ǐ|Ǔ|Ě|Ā|Ī|Ū|Ē|A̍|I̍|U̍|E̍|A̋|I̋|Ű|Ă|Ĭ|Ŭ|Ĕ|Ḿ|Ń|M̀|Ǹ|M̂|N̂|M̌|Ň|M̄|N̄|M̍|N̍|M̋|N̋|M̆|N̆|á|í|ú|é|ó|ó͘|ḿ|ń|à|ì|ù|è|ò|ò͘|m̀|ǹ|â|î|û|ê|ô|ô͘|m̂|n̂|ǎ|ǐ|ǔ|ě|ǒ|ǒ͘|m̌|ň|ā|ī|ū|ē|ō|ō͘|m̄|n̄|a̍|i̍|u̍|e̍|o̍|o̍͘|m̍|n̍|a̋|i̋|ű|e̋|ő|ő͘|m̋|n̋|ă|ĭ|ŭ|ĕ|ŏ|ŏ͘|m̆|n̆|ⁿ|ˆ|ˇ|ˊ|ˋ|⁺|[a-z]|[A-Z]|．|、|。|；|「|」|『|』|（|）|\(|\)|，|,|`(.+?)~)/g)].map((o) => {
        const w = o.filter((k) => { return k })
        // console.log(w)
        return w[w.length - 1]
      })
      return arr
    }
  },
  watch: {
    $route () {
      // console.log('w')
      this.set()
    }
  }
}
</script>

<style type="text/css" scoped="">

  .divider {
  }

  #word {
    position: absolute;
    transform-origin: top left;
  }

  #word div {
    display: inline-block;
  }

  @media screen and (max-width: 600px) {
    #word {
      display: block;
    }
  }

  .word {
    padding: 0 1em;
  }

  h1 {
    font-size: 64px;
    display: inline;
    margin-right: .3em;
  }

  #b {
    position: relative;
    right: .5em;
    top: .9em;
    display: inline-block;
    font-size: 24px;
    width: 1em;
    height: 4em;
    overflow: visible;
  }

  .diao {
    position: relative;
  }

  a {
    cursor: pointer;
    text-decoration: none;
  }

  .r {
    text-decoration: none;
  }

  .r::after {
  }

  .yindiao {
    display: inline-flex;
    align-items: center;
    position: relative;
    top: -2.2em;
    color: gray;
  }

  .yin {
    line-height: 1em;
  }

  #play {
    color: #c33;
    margin-right: 1em;
    font-size: 40px;
  }

  .red {
    margin-left: 1em;
  }

  .p {
    position: absolute;
    bottom: 2.2em;
    left: -2.5em;
    color: gray;
    width: 70vw;
    overflow: visible;
    height: 1em;
  }

  .p.hakka {
    font-size: .5em;
  }

  @media screen and (max-width: 600px) {
    .p {
       /* font-size: .5em; */
    }
  }

  .left {
    text-align: left;
  }

  .print {
    margin-left: 1em;
  }

  .antonyms {
    border-bottom: 6px solid #ccc;
  }

  .soc {
    width: 100%;
    text-align: right;
  }

  .soc .q-btn {
    margin: 0 1em;
  }

  .soc .q-icon {
    color: white;
  }

  .small {
  }

  .radical {
    color: black;
  }

  input {
    font-size: 22px;
  }

  button {
    font-size: 22px;
  }
</style>
