<template>
  <div class="about">
    <h1>Test Screen</h1>
    <div v-if="!isTesting && !isFinish">
      <p>Click the button below to start the test.</p>
      <button @click="doTest">Start Test</button>
    </div>
    <div class="test" v-if="isTesting">
      <p>Now Playing: <strong>{{currentFrequency}} Hertz</strong></p>
      <p>Current Volume: <strong>{{currentVolume}}</strong></p>
      <p>Have you heard that sound?</p>
      <a @click="() => answer(true)">Yes</a>
      &nbsp; &nbsp;
      <a @click="() => answer(false)">No</a>
      &nbsp; &nbsp;  &nbsp; &nbsp;
      <a @click="() => playSound(currentFrequency)">Play Again</a>
    </div>
    <div v-if="!isTesting && isFinish">
      <p>Test finished! Thank you</p>
    </div>
  </div>
</template>

<script>


export default {

  name: 'TestView',
  data() {
    return {
      isTesting: false,
      isFinish: false,
      currentFrequency: 0,
      currentVolume: 0,
      frequencies: [250, 500, 1000, 2000, 4000, 8000],
      answers: [],
      audioCtx: null,
    }
  },
  mounted() {
    console.log('mounted');
    
    this.audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    
  },
  methods: {
    doTest() {
      this.isTesting = true;
      this.currentFrequency = this.frequencies.shift();
      this.playSound(this.currentFrequency);
    },
    answer(isYes) {
      this.answers.push({
        frequency: this.currentFrequency,
        volume: this.currentVolume,
        isYes: isYes
      });
      this.next();
    },
    next() {
      if (this.frequencies.length > 0) {
        this.currentFrequency = this.frequencies.shift();
        this.playSound(this.currentFrequency);
      } else {
        this.isFinish = true;
        this.isTesting = false;
        console.log(this.answers);
      }
    },
    playSound(frequency, gain=0, duration=0.5) {
      let oscillator;
      oscillator = this.audioCtx.createOscillator();
      let gainNode = this.audioCtx.createGain();
      oscillator.type = 'sine';
      oscillator.connect(gainNode);
      oscillator.frequency.setValueAtTime(frequency, this.audioCtx.currentTime); // value in hertz
      oscillator.connect(this.audioCtx.destination);
      oscillator.connect(gainNode);
      gainNode.connect(this.audioCtx.destination);
      gainNode.gain.value = gain;
      oscillator.start();
      oscillator.stop(this.audioCtx.currentTime + duration);
    }
  }

}











</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .test {
    margin: 0 auto;
  }
}
</style>
