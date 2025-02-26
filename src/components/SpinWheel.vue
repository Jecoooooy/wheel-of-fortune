
<template>
  <h2 v-if="!winnerResult">Spin the wheel</h2>
  <h2 v-else>you won ${{winnerResult.text}}</h2>
  <div class="h-5">
    <ConfettiExplosion v-if="confetii"></ConfettiExplosion>
  </div>

  <div class="border-[16px] boc-border rounded-full border-blue-900" :class="animate && 'bounce'">


  <VueWheelSpinner

      ref="spinner"
      :slices="slices"
      :winner-index="defaultWinner"
      :sounds="sounds"
      :cursor-angle="cursorAngle"
      :cursor-position="cursorPosition"
      :cursor-distance="cursorDistance"
      @spin-start="onSpinStart"
      @spin-end="onSpinEnd">

    <template #cursor>
      <div class="size-10 bg-white" style="clip-path: polygon(100% 0, 50% 100%, 0 0);"></div>
    </template>

    <template #default>
      <button
          class="spin-button"
          :disabled="isSpinning"
          @click="handleSpinButtonClick"
          @mouseover="handleSpinButtonHover"
          @mouseleave="handleSpinButtonLeave">
        Spin
      </button>
    </template>

  </VueWheelSpinner>
  </div>
  <button @click="onReset()"> Reset</button>
</template>

<script>
import VueWheelSpinner from 'vue-wheel-spinner';
import ConfettiExplosion from "vue-confetti-explosion";

import cursorImage from './../assets/cursor.png';
import wonSound from './../sounds/won.mp3';
import clickSound from './../sounds/click.mp3';
import hoverSound from './../sounds/hover.mp3';
import leaveSound from './../sounds/hover.mp3';
import spinningSound from './../sounds/spinning.mp3';

export default {
  components: {
    VueWheelSpinner,
    ConfettiExplosion
  },
  data() {
    return {
      winnerResult: null,
      animate:false,
      confetii:false,
      slices: [
        {color: '#eb4d4b', text: '10'},
        {color: '#f0932b', text: '100'},
        {color: '#f9ca24', text: '1,000'},
        {color: '#badc58', text: '10,000'},
        {color: '#7ed6df', text: '100,000'},
        {color: '#e056fd', text: '1,000,000'}
      ],
      isSpinning: false,
      defaultWinner: 0,
      sounds: {
        won: wonSound,
        spinButtonClick: clickSound,
        spinButtonHover: hoverSound,
        spinButtonLeave: leaveSound,
        spinning: spinningSound
      },
      cursorImage,
      cursorAngle: 0,
      cursorPosition: 'edge',
      cursorDistance: 0
    };
  },
  methods: {
    playAudio(audio) {
      if (audio) {
        audio.volume = 0.5
        audio.play();
      }
    },
    handleSpinButtonClick() {
      if (this.buttonClickAudio) {
        this.playAudio(this.buttonClickAudio)
      }
      this.$refs.spinner.spinWheel(this.defaultWinner);
    },
    handleSpinButtonHover() {
      if (this.buttonHoverAudio) {
        this.playAudio(this.buttonHoverAudio)
      }
    },
    handleSpinButtonLeave() {
      if (this.buttonLeaveAudio) {
        this.playAudio(this.buttonLeaveAudio)
      }
    },
    spinFor(index) {
      this.defaultWinner = index;
      this.$refs.spinner.spinWheel(index);
    },
    onSpinStart() {
      this.winnerResult = null;
      this.animate = true
      this.confetii=false
      this.isSpinning = true;
    },
    onSpinEnd(winnerIndex) {
      this.isSpinning = false;
      this.animate = false
      this.confetii=true
      this.winnerResult = this.slices[winnerIndex];
    },
    onReset(){
      this.winnerResult = null;
    }
  },
  mounted() {
    this.buttonHoverAudio = new Audio(hoverSound);
    this.buttonLeaveAudio = new Audio(leaveSound);
    this.buttonClickAudio = new Audio(clickSound);
  }
};
</script>

<style>

.cursor-img {
  width: 50px;
  aspect-ratio: 1 / 1;
  filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, 0.19));
}

.spin-button {
  width: 100px;
  height: 100px;
  margin: 0 auto;
  aspect-ratio: 1 / 1;
  font-size: 20px;
  cursor: pointer;
  background: #eb4d4b;
  border-radius: 50%;
  transition: all 150ms;
  border: 10px solid white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  color: white !important;
  box-shadow: inset -3px -3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  z-index: 11;
  position: relative;
  user-select: none;

  &:hover {
    box-shadow: inset -5px -5px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:active {
    box-shadow: inset 3px 3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:disabled {
    background: #ccc;
    cursor: not-allowed;
    pointer-events: none;
  }

}

@keyframes bounce {
  0%{
    transform: translateY(0px) scaleX(100%) scaleY(100%);
  }
  60%{
    transform: translateY(-200px) scaleX(60%) scaleY(110%);
  }
  80%{
    transform: translateY(20px)  scaleX(110%) scaleY(60%);
  }
  100%{
    transform: translateY(0px) scaleX(100%) scaleY(100%);
  }
}
.bounce {
  animation: bounce 1s ease-in-out forwards ;
}
</style>