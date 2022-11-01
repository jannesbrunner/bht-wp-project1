<template>
  <div class="about">
    <h1>Test Screen</h1>
    <div v-if="!isTesting && !isFinish">
    <div><strong>Name:</strong>&nbsp; &nbsp;<input type="text" v-model="name" /></div>
    <div><strong>Age:</strong> &nbsp; &nbsp;<input type="number" v-model="age"/></div>
      <p>Click the button below to start the test.</p>
      <button @click="() => { if (checkInput()) {doTest()}}">Start Test</button>
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
      name: '',
      age: 0,
      currentFrequency: 0,
      currentVolume: 0,
      frequencies: [
        250, 
        500, 
        1000, 
        2000, 
        4000, 
        6000, 
        6500, 
        7000, 
        7500, 
        8000, 
        8500, 
        9000, 
        9500, 
        10000, 
        10500, 
        11000, 
        11500, 
        12000, 
        12500, 
        13000, 
        13500, 
        14000, 
        14500, 
        15000, 
        15500, 
        16000, 
        16500, 
        17000, 
        17500, 
        18000, 
        18500, 
        19000, 
        19500, 
        20000],
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
    checkInput() {
      if (this.name === '' || this.age <= 0) {
        alert('Please input your name and age');
        return false;
      }
      return true;
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
        this.saveTestResult();
      }
    },
    saveTestResult() {

      // get data from localstorage if exists
      let data = localStorage.getItem('testResults');
      if(data) {
        data = JSON.parse(data);
      } else {
        data = [];
      }

      let newEntry = {
        name: this.name,
        age: this.age,
        answers: this.answers
      }

      data.push(newEntry);
      localStorage.setItem('testResults', JSON.stringify(data));
    },
    playSound(frequency, gain=0, duration=1) {
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
