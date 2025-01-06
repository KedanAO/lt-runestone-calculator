<script>
import runes from './utils/runes.js'
import scrolls from './utils/scrolls.js'

// todo: 
// - Make main window (like in-game)
// --  Make it so you can click to select each rune, adding it to the 8 on top right | DONE
// -- List effects on bottom right box
// -- Hover effect on each rune to describe the name + stats
// --- Separate into header/body to be more similar to in-game
// --- Change the box to be more similar to the in-game box
// - Clear button
// - On the right, make an additional table that lists all runes (also shows if selected and can click) and their values
// -- Below that, show current value and max potential
// -- Show table of rating % / rolls / value somewhere
// - Simulator options
// -- Button to generate one random set
// -- Button to roll until a selected % quality


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

      tooltip: false,
      tooltipPosition: [0,0],
      tooltipText: '',
    }
  },

  methods: {
    calculateValue(){
      let total = 0

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
      if (rating >= 1) {
        return 'Maxed!'
      }
      const n = scrolls[scrolls.indexOf(scrolls.find((s) => s[0] > rating))+1][1]
      if (n === 'too many') {
        return n
      }
      return n.toFixed(0) + ' (' + (n * 0.15).toFixed(2) + 'b Ely)'
    },

    formatRunestoneTooltip(r){
      return '<' + r['name'] + '>\n\n[Runestone of ' + r['name'].toUpperCase() + ']\n\n*Effects*\n' + r['stats'] + '\n\n*Damage Increase*\n- ' + r['value'].toFixed(5) + '%'
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
  </div>
  <div class="side">
    <div class="results">
      <div class="results-di">
        DI: {{ calculateValue() }}% / Max: 10.361%
      </div>
      <div class="results-rating">
        Rating: {{ (calculateValue() / 10.361 * 100).toFixed(2) }}%
      </div>
      <div class="results-scrolls">
        Average scrolls to improve: {{ getScrollsNeeded(calculateValue() / 10.361) }}
      </div>
    </div>
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
        <tr v-for="scroll in scrolls">
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
