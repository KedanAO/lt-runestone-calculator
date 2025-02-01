<script>
import runes from './utils/runes.js'
import scrolls from './utils/scrolls.js'

// todo: 
// - Clear button 
// - Simulator options
// -- Button to roll until a selected % quality | ?
// - Insight
// -- Make it so it displays 0% if it doesn't contain insight
// --- Add a message that explains why
// -- Improve Selection button and add explanation

export default {
  data() {
    return {
      runes,
      scrolls,
      runeStates: [
        false, false, false, false, false, false, false, false, false, false,
        false, false, false, false, false, false, false, false, false, false,
        false, false, false, false, false, false, false, false, false, false,
      ],
      selectedRunes: [],
      imgUrls: import.meta.glob('./assets/*.png', {
        import: 'default',
        eager: true,
      }),
      mode: 'Normal',

      tooltip: false,
      tooltipPosition: [0,0],
      tooltipText: '',
    }
  },

  methods: {
    calculateValue(){
      let total = 0

      if (this.mode === "Insight" && !this.selectedRunes.includes(20)) {
        return total.toFixed(3)
      }

      this.selectedRunes.forEach((r) => {
        total += this.runes[r-1]['value']
        if (this.selectedRunes.indexOf(r) === 7) {
          total += this.runes[r-1]['value']
        }
      })

      return total.toFixed(3)
    },

    getRandomInt(max){
      return Math.floor(Math.random() * max)
    },

    getRandomRunes() {
      let r = []

      for (let i=0; i<8; i++) {
        let roll = this.getRandomInt(30) + 1

        while (r.includes(roll)) {
          roll = this.getRandomInt(30) + 1
        }

        r.push(roll)
      }

      this.selectedRunes = r

      for (let i=0; i<this.runeStates.length; i++) {
        if (r.includes(i+1)) { this.runeStates[i] = true }
        else { this.runeStates[i] = false }
      }
    },

    getScrollsNeeded(rating) {
      const s = scrolls[this.mode]

      if (rating >= 1) {
        return 'Maxed!'
      }
      let n = s.indexOf(s.find((sr) => sr[0] > rating))+1
      if (n >= s.length) {
        return 'too many'
      }
      n = s[s.indexOf(s.find((sr) => sr[0] > rating))+1][1]
      if (n === 'too many') {
        return n
      }
      return n.toFixed(0) + ' (' + (n * 0.15).toFixed(2) + 'b Ely)'
    },

    formatRunestoneTooltip(r){
      return '<' + r['name'] + '>\n\n[Runestone of ' + r['name'].toUpperCase() + ']\n\n*Effects*\n' + r['stats'] + '\n\n*Damage Increase*\n- ' + r['value'].toFixed(5) + '%'
    },

    getMaximumValue() {
      if (this.mode === "Normal") {
        return 10.361
      } 
      else {
        return 9.647
      }
    }, 

    toggleRune(r) {
      if (this.runeStates[r]) {
        this.runeStates[r] = !this.runeStates[r]  
        this.selectedRunes.splice(this.selectedRunes.indexOf(r+1), 1)
      }
      else if (this.selectedRunes.length < 8) {
        this.runeStates[r] = !this.runeStates[r]
        this.selectedRunes.push(r+1)
      }
    },

    onMouseMove(e) {
      this.tooltipPosition[0] = e.pageX + 15
      this.tooltipPosition[1] = e.pageY
    },
  },

  mounted() {
    
  },

  updated() {
    
  }
}
</script>

<template>
  <div class="main">
    <img src="./assets/RunestoneEmpty.png" alt="">
    <div class="runes-all">
      <div class="rune" v-for="rune in runes" :key="rune">
        <div class="single-rune"
          @click="toggleRune(rune['num']-1)"
          :style="{left: 37 + (rune['num']-1) % 5 * 68 + 'px', top: 81 + Math.floor((rune['num']-1) / 5) * 68 + 'px', position: 'absolute'}">
          <img v-if="runeStates[rune['num']-1] && rune['num'] <= 20" src="./assets/ActiveSlotEmpty.png" alt=""
          @mouseenter="tooltip = true; tooltipText = formatRunestoneTooltip(rune)" 
          @mousemove.self="onMouseMove($event)" @mouseleave="tooltip=false">
          <img v-else-if="runeStates[rune['num']-1] && rune['num'] > 20" src="./assets/ActiveSlotEmpty2.png" alt=""
          @mouseenter="tooltip = true; tooltipText = formatRunestoneTooltip(rune)" 
          @mousemove.self="onMouseMove($event)" @mouseleave="tooltip=false">
          <img v-else src="./assets/InactiveSlotInvisible.png" alt=""
          @mouseenter="tooltip = true; tooltipText = formatRunestoneTooltip(rune)" 
          @mousemove.self="onMouseMove($event)" @mouseleave="tooltip=false">
        </div>
        <div class="single-rune"
          @click="toggleRune(rune['num']-1)"
          :style="{left: 49 + (rune['num']-1) % 5 * 68 + 'px', top: 93 + Math.floor((rune['num']-1) / 5) * 68 + 'px', position: 'absolute'}">
          <img v-if="runeStates[rune['num']-1]" :src="imgUrls[`./assets/Rune${rune['num']}.png`]"  alt=""
          @mouseenter="tooltip = true; tooltipText = formatRunestoneTooltip(rune)" 
          @mousemove.self="onMouseMove($event)" @mouseleave="tooltip=false">
        </div>
      </div>
    </div>
    <div v-for="n in 8" class="runes-selected">
      <img v-if="selectedRunes.length > n-1" :class="`rune-selected-` + n"
        :src="imgUrls[`./assets/Rune${selectedRunes[n-1]}.png`]" alt=""
        @click="toggleRune(selectedRunes[n-1]-1)"
        @mouseenter="tooltip = true; tooltipText = formatRunestoneTooltip(runes[selectedRunes[n-1]-1])" 
        @mousemove.self="onMouseMove($event)" @mouseleave="tooltip=false">
    </div>
    <div class="runes-stats">
      <div v-for="n in 7" class="runes-stats-normal">
        <div v-if="selectedRunes.length > n-1">
          {{ runes[selectedRunes[n-1]-1]['stats'].replace('- ', '').replace('\n- ', ' / ') }}
        </div>
      </div>
      <div v-if="selectedRunes.length > 7" class="runes-stats-last">
        (Double) {{ runes[selectedRunes[7]-1]['stats'].replace('- ', '').replace('\n- ', ' / ') }}
      </div>
    </div>
    <button class="button-roll" @click="getRandomRunes()"></button>
    <div class="side">
      <div class="results">
        <div class="results-di">
          DI: {{ calculateValue() }}% / Max: {{ getMaximumValue() }}%
        </div>
        <div class="results-rating">
          Rating: {{ (calculateValue() / getMaximumValue() * 100).toFixed(2) }}% <span v-if="mode === 'Insight' && !selectedRunes.includes(20)"> (No Insight)</span>
        </div>
        <div class="results-scrolls">
          Average scrolls to improve: {{ getScrollsNeeded(calculateValue() / getMaximumValue()) }}
        </div>
        <br><br>Please note values are purely theoretical and subject to change with future gear, dungeons and balance patches - if you want to do more detailed tests,
        please utilize the <a href="https://kedanao.github.io/lt-damage-calculator/">damage calculator</a>.
      </div>
      <div class="insight-selection">
        <h2>Insight</h2>
        When considering the Insight rune, its actual value cannot be accurately measured as it may change from class to class. However, if you particularly desire to have
        insight on your rune set no matter what, Insight mode will only consider pages that have Insight in them and thus will have a lower DI ceiling and different
        ratings/cost to improve upon.<br><br>
        Mode: 
        <select v-model="mode" class="insight-mode">
          <option value="Normal">Normal</option>
          <option value="Insight">Insight</option>
        </select>
      </div>
    </div>
  </div>
  <div class="bottom">
    <div class="rune-table-container">
      <table class="rune-table"> 
        <tr><th>Rune</th><th>Name</th><th>Stats</th><th>DI%</th></tr>
        <tr v-for="rune in runes">
          <td><img :src="imgUrls[`./assets/Rune${rune['num']}.png`]" alt=""></td>          
          <td>{{ rune['name'] }}</td>
          <td>{{ rune['stats'].replace('- ', '').replace('\n- ', ' / ') }}</td>
          <td>{{ rune['value'].toFixed(5) + '%' }}</td>
        </tr>
      </table>
      <table class="rating-table"> 
        <tr><th>Rating</th><th>Scrolls needed (Avg)</th></tr>
        <tr v-for="scroll in scrolls[mode]">
          <td>{{ (scroll[0] * 100).toFixed(2) + '%'}}</td>          
          <td>{{ scroll[1] }}</td>
        </tr>
      </table>
    </div>
  </div>

  <div v-if="tooltip" id="tooltip" :style="{left: tooltipPosition[0] + 'px', top: tooltipPosition[1] + 'px'}">
    {{ tooltipText }}
  </div>
</template>
