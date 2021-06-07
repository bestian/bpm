<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <button class = "no-print" onclick="window.print()">友善列印</button>
    <br/>
    <div class="blue card" v-for = "(c, idx) in ziying" :key="c">
      {{c}}
      <span class="label">{{idx+1}}</span>
    </div>
    <hr/>
    <div class="red card" v-for = "(c, idx) in muying" :key="c">
      {{c}}
      <span class="label">{{idx+1}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      list: ['ㄅ','ㄆ','ㄇ','ㄈ','ㄉ','ㄊ','ㄋ','ㄌ', 
              'ㄍ','ㄎ','ㄏ','ㄐ','ㄑ','ㄒ',
              'ㄓ','彳','ㄕ','ㄖ','ㄗ','ㄘ','厶',
              'ㄚ','ㄛ','ㄜ','ㄝ','ㄞ','ㄟ','ㄠ','ㄡ',
              'ㄢ','ㄣ','ㄤ','ㄥ','ㄦ','ㄧ','ㄨ','ㄩ',
              '　','ˊ', 'ˇ', 'ˋ', '˙'],
      ziying: ['ㄅ','ㄆ','ㄇ','ㄈ','ㄉ','ㄊ','ㄋ','ㄌ', 'ㄍ','ㄎ','ㄏ','ㄐ','ㄑ','ㄒ', 'ㄓ','彳','ㄕ','ㄖ','ㄗ','ㄘ','厶'],
      muying: [ 'ㄚ','ㄛ','ㄜ','ㄝ','ㄞ','ㄟ','ㄠ','ㄡ',
              'ㄢ','ㄣ','ㄤ','ㄥ','ㄦ','ㄧ','ㄨ','ㄩ'],
      know: ['ㄅ','ㄆ','ㄇ']
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  max-width: 740px;
  margin: 0 auto;
}
.card {
  position: relative;
  display: inline-flex;
  padding: 1em;
  font-size: 36px;
  border-radius: 15px;
  border: 1px dotted black;
  transition: all 0.3s ease;
  background-color: white;
  font-weight: bolder;
}

.card:hover {
  transform: scale(2);
  z-index: 999;
}

button {
  font-size: 26px;
  border-radius: 15px;
  margin-bottom: 1em;
}

.blue {
  color: blue;
}

.red {
  color: red;
}

.label {
  position: absolute;
  bottom: 0.2em;
  right: 0.2em;
  font-size: 11px;
}
</style>
