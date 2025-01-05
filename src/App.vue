<script>
import runes from './utils/runes.js'

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
    onMouseMove(e) {
      this.tooltipPosition[0] = e.pageX + 15
      this.tooltipPosition[1] = e.pageY
    },

    formatRunestoneTooltip(r){
      return '<' + r['name'] + '>\n\n[Runestone of ' + r['name'].toUpperCase() + ']\n\n*Effects*\n' + r['stats']
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
    <div class="runes-selected">
      <img v-if="selectedRunes.length > 0" class="rune-selected-1" :src="imgUrls[`./assets/Rune${selectedRunes[0]}.png`]" alt=""
        :style="{left: 540 + 'px', top: 43 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 1" class="rune-selected-2" :src="imgUrls[`./assets/Rune${selectedRunes[1]}.png`]" alt=""
        :style="{left: 465 + 'px', top: 83 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 2" class="rune-selected-3" :src="imgUrls[`./assets/Rune${selectedRunes[2]}.png`]" alt=""
        :style="{left: 615 + 'px', top: 83 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 3" class="rune-selected-4" :src="imgUrls[`./assets/Rune${selectedRunes[3]}.png`]" alt=""
        :style="{left: 450 + 'px', top: 153 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 4" class="rune-selected-5" :src="imgUrls[`./assets/Rune${selectedRunes[4]}.png`]" alt=""
        :style="{left: 630 + 'px', top: 153 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 5" class="rune-selected-6" :src="imgUrls[`./assets/Rune${selectedRunes[5]}.png`]" alt=""
        :style="{left: 495 + 'px', top: 213 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 6" class="rune-selected-7" :src="imgUrls[`./assets/Rune${selectedRunes[6]}.png`]" alt=""
        :style="{left: 585 + 'px', top: 213 + 'px', position: 'absolute'}">
      <img v-if="selectedRunes.length > 7" class="rune-selected-8" :src="imgUrls[`./assets/Rune${selectedRunes[7]}.png`]" alt=""
        :style="{left: 540 + 'px', top: 133 + 'px', position: 'absolute'}">
    </div>
    <div class="runes-stats">

    </div>
    <button class="roll">

    </button>
  </div>
  <div class="side">
    <div class="rune-table">
      <div></div>
      <div></div>
    </div>
    <div class="results">

    </div>
  </div>

  <div v-if="tooltip" id="tooltip" :style="{left: tooltipPosition[0] + 'px', top: tooltipPosition[1] + 'px'}">
    {{ tooltipText }}
  </div>
</template>
