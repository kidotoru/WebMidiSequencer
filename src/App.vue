<script setup>
import { ref } from 'vue'

let playTimerID = 0

const bpm = ref(120)

const currentStepNo = ref(0)
const lastStepNo = ref(64)

const isPlay = ref(false)
const isRec = ref(false)

const steps = ref([])

const midiInputs = ref([])
const midiOutputs = ref([])

const midiInputCh = ref(0)
const midiOutputCh = ref(2)


for (let i = 0; i < 64; i++) {
  let step = {}
  step.no = i + 1
  steps.value.push(step)
}

navigator.requestMIDIAccess().then(onMIDISuccess, onMIDIFailure)

function play(event) {
  isPlay.value = !isPlay.value

  if (isPlay.value) {
    currentStepNo.value = 1
    // t = 60,000 ms / n / bpm
    // 60,000 ms = 1 min
    // n = 4 拍子
    // t: 1stepの長さ ms
    const n = 4
    const t = (60 * 1000) / n / bpm.value

    playTimerID = setInterval(tickStep, t)
  } else {
    currentStepNo.value = 0
    clearInterval(playTimerID)
  }
}

function rec(event) {
  isRec.value = !isRec.value
}

function tickStep() {
  currentStepNo.value++
  currentStepNo.value = currentStepNo.value > lastStepNo.value ? 1 : currentStepNo.value
}

function onMIDISuccess(midi) {
  window.navigator.requestMIDIAccess().then((midi) => {
    midi.inputs.forEach((input) => console.log(input))
    midi.outputs.forEach((output) => console.log(output))

    midi.inputs.forEach((input) => {
      console.log(input)
      midiInputs.value.push(input)
      input.onmidimessage = onMIDIEvent
    })

    midi.outputs.forEach((output) => {
      console.log(output)
      midiOutputs.value.push(output)
    })
  })
}

function onMIDIFailure(msg) {
  console.log("onMIDIFailure" + msg)
}

function onMIDIEvent(e) {
  if (e.data.length >= 2) {
    // なにかをうけとったときの処理
    if (e.data[0] === 0x90 | midiInputCh.value) {
        console.log(e)
        sendMIDINoteOn(e.data[1], e.data[2])
      } else if(e.data[0] === 0x80 | midiInputCh.value) {
        console.log(e)
        sendMIDINoteOff(e.data[1], e.data[2])
      }
  }
}

function sendMIDINoteOn(note, velocity = 0x7f) {
  midiOutputs.value.forEach((output) => {
    output.send([0x90 | midiOutputCh.value, note, velocity])
  })
}

function sendMIDINoteOff(note, velocity = 0x7f) {
  midiOutputs.value.forEach((output) => {
    output.send([0x80 | midiOutputCh.value, note, velocity])
  })
}

</script>


<template>
  <div class="midi-app">
    <div style="display: inline-block;">
      <div style="display: block;">
        <ul class="steps">
          <li class="step" v-bind:class="{'active-step' : steps[0].no === currentStepNo}"><span>01</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[1].no === currentStepNo}"><span>02</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[2].no === currentStepNo}"><span>03</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[3].no === currentStepNo}"><span>04</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[4].no === currentStepNo}"><span>05</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[5].no === currentStepNo}"><span>06</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[6].no === currentStepNo}"><span>07</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[7].no === currentStepNo}"><span>08</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[8].no === currentStepNo}"><span>09</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[9].no === currentStepNo}"><span>10</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[10].no === currentStepNo}"><span>11</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[11].no === currentStepNo}"><span>12</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[12].no === currentStepNo}"><span>13</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[13].no === currentStepNo}"><span>14</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[14].no === currentStepNo}"><span>15</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[15].no === currentStepNo}"><span>16</span></li>

          <li class="step-filler"></li>

          <li class="step" v-bind:class="{'active-step' : steps[16].no === currentStepNo}"><span>17</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[17].no === currentStepNo}"><span>18</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[18].no === currentStepNo}"><span>19</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[19].no === currentStepNo}"><span>20</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[20].no === currentStepNo}"><span>21</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[21].no === currentStepNo}"><span>22</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[22].no === currentStepNo}"><span>23</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[23].no === currentStepNo}"><span>24</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[24].no === currentStepNo}"><span>25</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[25].no === currentStepNo}"><span>26</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[26].no === currentStepNo}"><span>27</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[27].no === currentStepNo}"><span>28</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[28].no === currentStepNo}"><span>29</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[29].no === currentStepNo}"><span>30</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[30].no === currentStepNo}"><span>31</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[31].no === currentStepNo}"><span>32</span></li>
        </ul>
      </div>

      <div style="display: block;">
        <ul class="steps">
          <li class="step" v-bind:class="{'active-step' : steps[32].no === currentStepNo}"><span>33</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[33].no === currentStepNo}"><span>34</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[34].no === currentStepNo}"><span>35</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[35].no === currentStepNo}"><span>36</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[36].no === currentStepNo}"><span>37</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[37].no === currentStepNo}"><span>38</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[38].no === currentStepNo}"><span>39</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[39].no === currentStepNo}"><span>40</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[40].no === currentStepNo}"><span>41</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[41].no === currentStepNo}"><span>42</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[42].no === currentStepNo}"><span>43</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[43].no === currentStepNo}"><span>44</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[44].no === currentStepNo}"><span>45</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[45].no === currentStepNo}"><span>46</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[46].no === currentStepNo}"><span>47</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[47].no === currentStepNo}"><span>48</span></li>

          <li class="step-filler"></li>

          <li class="step" v-bind:class="{'active-step' : steps[48].no === currentStepNo}"><span>49</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[49].no === currentStepNo}"><span>50</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[50].no === currentStepNo}"><span>51</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[51].no === currentStepNo}"><span>52</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[52].no === currentStepNo}"><span>53</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[53].no === currentStepNo}"><span>54</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[54].no === currentStepNo}"><span>55</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[55].no === currentStepNo}"><span>56</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[56].no === currentStepNo}"><span>57</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[57].no === currentStepNo}"><span>58</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[58].no === currentStepNo}"><span>59</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[59].no === currentStepNo}"><span>60</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[60].no === currentStepNo}"><span>61</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[61].no === currentStepNo}"><span>62</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[62].no === currentStepNo}"><span>63</span></li>
          <li class="step" v-bind:class="{'active-step' : steps[63].no === currentStepNo}"><span>64</span></li>
        </ul>
      </div>
    </div>


    <div style="display: inline-block;margin-top: 1em;">
      <ul class="keyboard">
        <li class="key-filler"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(49)" v-on:mouseup="sendMIDINoteOff(49)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(51)" v-on:mouseup="sendMIDINoteOff(51)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(54)" v-on:mouseup="sendMIDINoteOff(54)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(56)" v-on:mouseup="sendMIDINoteOff(56)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(58)" v-on:mouseup="sendMIDINoteOff(58)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(61)" v-on:mouseup="sendMIDINoteOff(61)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(63)" v-on:mouseup="sendMIDINoteOff(63)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(66)" v-on:mouseup="sendMIDINoteOff(66)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(68)" v-on:mouseup="sendMIDINoteOff(68)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(70)" v-on:mouseup="sendMIDINoteOff(70)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(73)" v-on:mouseup="sendMIDINoteOff(73)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(75)" v-on:mouseup="sendMIDINoteOff(75)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(78)" v-on:mouseup="sendMIDINoteOff(78)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(80)" v-on:mouseup="sendMIDINoteOff(80)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(82)" v-on:mouseup="sendMIDINoteOff(82)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(85)" v-on:mouseup="sendMIDINoteOff(85)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(87)" v-on:mouseup="sendMIDINoteOff(87)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(90)" v-on:mouseup="sendMIDINoteOff(90)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(92)" v-on:mouseup="sendMIDINoteOff(92)"></li>
        <li class="key-b" v-on:mousedown="sendMIDINoteOn(94)" v-on:mouseup="sendMIDINoteOff(94)"></li>
        <li class="key-filler"></li>
      </ul>

      <!-- https://www.asahi-net.or.jp/~hb9t-ktd/music/Japan/Research/DTM/freq_map.html -->

      <ul class="keyboard">
        <li class="key" v-on:mousedown="sendMIDINoteOn(48)" v-on:mouseup="sendMIDINoteOff(48)"><span>C</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(50)" v-on:mouseup="sendMIDINoteOff(50)"><span>D</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(52)" v-on:mouseup="sendMIDINoteOff(52)"><span>E</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(53)" v-on:mouseup="sendMIDINoteOff(53)"><span>F</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(55)" v-on:mouseup="sendMIDINoteOff(55)"><span>G</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(57)" v-on:mouseup="sendMIDINoteOff(57)"><span>A</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(59)" v-on:mouseup="sendMIDINoteOff(59)"><span>B</span></li>

        <li class="key" v-on:mousedown="sendMIDINoteOn(60)" v-on:mouseup="sendMIDINoteOff(60)"><span>C</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(62)" v-on:mouseup="sendMIDINoteOff(62)"><span>D</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(64)" v-on:mouseup="sendMIDINoteOff(64)"><span>E</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(65)" v-on:mouseup="sendMIDINoteOff(65)"><span>F</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(67)" v-on:mouseup="sendMIDINoteOff(67)"><span>G</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(69)" v-on:mouseup="sendMIDINoteOff(69)"><span>A</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(71)" v-on:mouseup="sendMIDINoteOff(71)"><span>B</span></li>

        <li class="key" v-on:mousedown="sendMIDINoteOn(72)" v-on:mouseup="sendMIDINoteOff(72)"><span>C</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(74)" v-on:mouseup="sendMIDINoteOff(74)"><span>D</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(76)" v-on:mouseup="sendMIDINoteOff(76)"><span>E</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(77)" v-on:mouseup="sendMIDINoteOff(77)"><span>F</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(79)" v-on:mouseup="sendMIDINoteOff(79)"><span>G</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(81)" v-on:mouseup="sendMIDINoteOff(81)"><span>A</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(83)" v-on:mouseup="sendMIDINoteOff(83)"><span>B</span></li>

        <li class="key" v-on:mousedown="sendMIDINoteOn(84)" v-on:mouseup="sendMIDINoteOff(84)"><span>C</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(86)" v-on:mouseup="sendMIDINoteOff(86)"><span>D</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(88)" v-on:mouseup="sendMIDINoteOff(88)"><span>E</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(89)" v-on:mouseup="sendMIDINoteOff(89)"><span>F</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(91)" v-on:mouseup="sendMIDINoteOff(91)"><span>G</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(93)" v-on:mouseup="sendMIDINoteOff(93)"><span>A</span></li>
        <li class="key" v-on:mousedown="sendMIDINoteOn(95)" v-on:mouseup="sendMIDINoteOff(95)"><span>B</span></li>

        <li class="key" v-on:mousedown="sendMIDINoteOn(96)" v-on:mouseup="sendMIDINoteOff(96)"><span>C</span></li>
      </ul>
    </div>

    <div style="display: block;"></div>


    <div class="controller">
      <fieldset>
        <label>BPM</label>
        <input type="number" style="width: 4em;text-align: center;" v-model="bpm" />
      </fieldset>
      <fieldset>
        <label>PART</label>
        <input type="radio" name="part" checked /><label for="1">1</label>
        <input type="radio" name="part" /><label for="2">2</label>
        <input type="radio" name="part" /><label for="2">3</label>
        <input type="radio" name="part" /><label for="2">4</label>
        <input type="radio" name="part" /><label for="2">R</label>
      </fieldset>
      <fieldset>
        <label>LENGTH</label>
        <input type="radio" name="length" /><label for="1">1</label>
        <input type="radio" name="length" /><label for="2">2</label>
        <input type="radio" name="length" checked /><label for="2">4</label>
        <input type="radio" name="length" /><label for="2">8</label>
        <input type="radio" name="length" /><label for="2">16</label>
      </fieldset>
      <fieldset>
        <label>VELOCITY</label>
        <input type="number" style="width: 4em;text-align: center;" value="127" />
      </fieldset>
      <fieldset>
        <button v-on:click="play" v-bind:class="{'play' : isPlay}">PLAY</button>
        <button v-on:click="rec" v-bind:class="{'rec' : isRec}">REC</button>
      </fieldset>
      <fieldset>
        <button>COPY</button>
        <button>PASTE</button>
      </fieldset>

    </div>

    <div class="controller">
      <fieldset>
        <label>MIDI INPUT CH</label>
        <select name="midi-input-ch" v-model="midiInputCh">
          <option value="0">Ch01</option>
          <option value="1">Ch02</option>
          <option value="2">Ch03</option>
          <option value="3">Ch04</option>
          <option value="4">Ch05</option>
          <option value="5">Ch06</option>
          <option value="6">Ch07</option>
          <option value="7">Ch08</option>
          <option value="8">Ch09</option>
          <option value="9">Ch10</option>
          <option value="10">Ch11</option>
          <option value="11">Ch12</option>
          <option value="12">Ch13</option>
          <option value="13">Ch14</option>
          <option value="14">Ch15</option>
          <option value="15">Ch16</option>
        </select>

        <label>MIDI OUTPUT CH</label>
        <select name="midi-output-ch" v-model="midiOutputCh">
          <option value="0">Ch01</option>
          <option value="1">Ch02</option>
          <option value="2">Ch03</option>
          <option value="3">Ch04</option>
          <option value="4">Ch05</option>
          <option value="5">Ch06</option>
          <option value="6">Ch07</option>
          <option value="7">Ch08</option>
          <option value="8">Ch09</option>
          <option value="9">Ch10</option>
          <option value="10">Ch11</option>
          <option value="11">Ch12</option>
          <option value="12">Ch13</option>
          <option value="13">Ch14</option>
          <option value="14">Ch15</option>
          <option value="15">Ch16</option>
        </select>


      </fieldset>
    </div>

  </div>
</template>

<style scoped>

.midi-app {
  width: 100%;
  justify-content: center;
  align-items: center;
}

ul.steps {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  justify-self: center;
  padding: 0;
  margin: 0;
}

li.step {
  width: 2em;
  height: 3em;
  text-align: center;
  margin: 0.15em;
  background-color: #ddd;
  border-radius: 0.1em;
  cursor: pointer;
}


li.step-filler {
  width: 0.5em;
}

li.step span {
  display: inline-flex;
  height: 100%;
  align-items: center;
  color: #333;
}

ul.keyboard {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  justify-self: center;
  padding: 0;
  margin: 0;
}

li.key {
  width: 2em;
  height: 4em;
  text-align: center;
  margin: 0.15em;
  background-color: #ddd;
  cursor: pointer;
}

li.key-b {
  width: 2em;
  height: 4em;
  text-align: center;
  margin: 0.15em;
  background-color: #333;
  cursor: pointer;
}

li.key-none {
  width: 2em;
  height: 4em;
  text-align: center;
  margin: 0.15em;
  background-color: transparent;

}

li.key-filler {
  width: 1em;
  height: 4em;
  text-align: center;
  margin: 0;
  background-color: transparent;

}




li.key span {
  display: inline-flex;
  height: 100%;
  align-items: center;
  color: #aaa;
}

li.key-b span {
  display: inline-flex;
  height: 100%;
  align-items: center;
  color: #ccc;
}

div.controller {
  display:inline-flex;
  margin-top: 1em;
  border: 1px solid #ccc;
  padding: 0.25em;

}

.controller fieldset {
  display: inline-flex;
  padding: 0.5em;
  align-items: center;

}

.controller label {
  margin-right: 0.5em;
}


.play {
  color: greenyellow;
}

.rec {
  color: red;
}

.active-step {
  background-color: yellow !important;
}

</style>
