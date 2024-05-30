<script setup>
import { ref } from 'vue'

let playTimerID = 0

const messages = ref([])

const bpm = ref(120)

const currentStepNo = ref(0)
const lastStepNo = ref(64)

const isPlay = ref(false)
const isRec = ref(false)

const steps = ref([])
const keys = ref([])

const velocity = ref(127)

const midiInputs = ref([])
const midiOutputs = ref([])

const midiInputCh = ref(0)
const midiOutputCh = ref(2)

const isChords = ref(false)



for (let i = 0; i < 64; i++) {
  let step = {}
  step.no = i + 1
  steps.value.push(step)
}

for (let i = 0; i < 128; i++) {
  keys.value.push(false)
}

for(let i = 0; i < 10; i++) {
  messages.value.push(" ")
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

function chords(event) {
  isChords.value = !isChords.value
}

function tickStep() {
  currentStepNo.value++
  currentStepNo.value = currentStepNo.value > lastStepNo.value ? 1 : currentStepNo.value
}

function keyDown(tone) {
  sendMIDINoteOn(tone, velocity.value)
}

function keyUp(tone) {
  sendMIDINoteOff(tone)
}

function onMIDISuccess(midi) {
  window.navigator.requestMIDIAccess().then((midi) => {
    midi.inputs.forEach((input) => {
      console.log(input)
      outputMessage("[INOUT]"  + input.id + ':' + input.manufacturer + ':' + input.name)
      midiInputs.value.push(input)
      input.onmidimessage = onMIDIEvent
    })

    midi.outputs.forEach((output) => {
      console.log(output)
      outputMessage("[OUTPUT]"  + output.id + ':' + output.manufacturer + ':' + output.name + ':')
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
    outputMessage(e.data)
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
    keys.value[note] = true
    output.send([0x90 | midiOutputCh.value, note, velocity])
  })
}

function sendMIDINoteOff(note, velocity = 0x00) {
  midiOutputs.value.forEach((output) => {
    keys.value[note] = false
    output.send([0x80 | midiOutputCh.value, note, velocity])
  })
}

function outputMessage(message) {

  if(messages.value.length >= 10) {
    messages.value.splice(0, 1)
  }
  messages.value.push(message)

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
        <li class="key-b" v-bind:class="{'key-press' : keys[49]}" v-on:mousedown="keyDown(49)" v-on:mouseup="keyUp(49)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[51]}" v-on:mousedown="keyDown(51)" v-on:mouseup="keyUp(51)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[54]}" v-on:mousedown="keyDown(54)" v-on:mouseup="keyUp(54)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[56]}" v-on:mousedown="keyDown(56)" v-on:mouseup="keyUp(56)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[58]}" v-on:mousedown="keyDown(58)" v-on:mouseup="keyUp(58)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[61]}" v-on:mousedown="keyDown(61)" v-on:mouseup="keyUp(61)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[63]}" v-on:mousedown="keyDown(63)" v-on:mouseup="keyUp(63)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[66]}" v-on:mousedown="keyDown(66)" v-on:mouseup="keyUp(66)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[68]}" v-on:mousedown="keyDown(68)" v-on:mouseup="keyUp(68)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[70]}" v-on:mousedown="keyDown(70)" v-on:mouseup="keyUp(70)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[73]}" v-on:mousedown="keyDown(73)" v-on:mouseup="keyUp(73)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[75]}" v-on:mousedown="keyDown(75)" v-on:mouseup="keyUp(75)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[78]}" v-on:mousedown="keyDown(78)" v-on:mouseup="keyUp(78)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[80]}" v-on:mousedown="keyDown(80)" v-on:mouseup="keyUp(80)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[82]}" v-on:mousedown="keyDown(82)" v-on:mouseup="keyUp(82)"></li>

        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[85]}" v-on:mousedown="keyDown(85)" v-on:mouseup="keyUp(85)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[87]}" v-on:mousedown="keyDown(87)" v-on:mouseup="keyUp(87)"></li>
        <li class="key-none"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[90]}" v-on:mousedown="keyDown(90)" v-on:mouseup="keyUp(90)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[92]}" v-on:mousedown="keyDown(92)" v-on:mouseup="keyUp(92)"></li>
        <li class="key-b" v-bind:class="{'key-press' : keys[94]}" v-on:mousedown="keyDown(94)" v-on:mouseup="keyUp(94)"></li>
        <li class="key-filler"></li>
      </ul>

      <!-- https://www.asahi-net.or.jp/~hb9t-ktd/music/Japan/Research/DTM/freq_map.html -->

      <ul class="keyboard">
        <li class="key" v-bind:class="{'key-press' : keys[48]}" v-on:mousedown="keyDown(48)" v-on:mouseup="keyUp(48)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[50]}" v-on:mousedown="keyDown(50)" v-on:mouseup="keyUp(50)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[52]}" v-on:mousedown="keyDown(52)" v-on:mouseup="keyUp(52)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[53]}" v-on:mousedown="keyDown(53)" v-on:mouseup="keyUp(53)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[55]}" v-on:mousedown="keyDown(55)" v-on:mouseup="keyUp(55)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[57]}" v-on:mousedown="keyDown(57)" v-on:mouseup="keyUp(57)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[59]}" v-on:mousedown="keyDown(59)" v-on:mouseup="keyUp(59)"></li>

        <li class="key" v-bind:class="{'key-press' : keys[60]}" v-on:mousedown="keyDown(60)" v-on:mouseup="keyUp(60)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[62]}" v-on:mousedown="keyDown(62)" v-on:mouseup="keyUp(62)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[64]}" v-on:mousedown="keyDown(64)" v-on:mouseup="keyUp(64)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[65]}" v-on:mousedown="keyDown(65)" v-on:mouseup="keyUp(65)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[67]}" v-on:mousedown="keyDown(67)" v-on:mouseup="keyUp(67)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[69]}" v-on:mousedown="keyDown(69)" v-on:mouseup="keyUp(69)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[71]}" v-on:mousedown="keyDown(71)" v-on:mouseup="keyUp(71)"></li>

        <li class="key" v-bind:class="{'key-press' : keys[72]}" v-on:mousedown="keyDown(72)" v-on:mouseup="keyUp(72)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[74]}" v-on:mousedown="keyDown(74)" v-on:mouseup="keyUp(74)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[76]}" v-on:mousedown="keyDown(76)" v-on:mouseup="keyUp(76)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[77]}" v-on:mousedown="keyDown(77)" v-on:mouseup="keyUp(77)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[79]}" v-on:mousedown="keyDown(79)" v-on:mouseup="keyUp(79)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[81]}" v-on:mousedown="keyDown(81)" v-on:mouseup="keyUp(81)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[83]}" v-on:mousedown="keyDown(83)" v-on:mouseup="keyUp(83)"></li>

        <li class="key" v-bind:class="{'key-press' : keys[84]}" v-on:mousedown="keyDown(84)" v-on:mouseup="keyUp(84)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[86]}" v-on:mousedown="keyDown(86)" v-on:mouseup="keyUp(86)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[88]}" v-on:mousedown="keyDown(88)" v-on:mouseup="keyUp(88)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[89]}" v-on:mousedown="keyDown(89)" v-on:mouseup="keyUp(89)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[91]}" v-on:mousedown="keyDown(91)" v-on:mouseup="keyUp(91)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[93]}" v-on:mousedown="keyDown(93)" v-on:mouseup="keyUp(93)"></li>
        <li class="key" v-bind:class="{'key-press' : keys[95]}" v-on:mousedown="keyDown(95)" v-on:mouseup="keyUp(95)"></li>

        <li class="key" v-bind:class="{'key-press' : keys[96]}" v-on:mousedown="keyDown(96)" v-on:mouseup="keyUp(96)"></li>
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
        <input type="number" style="width: 4em;text-align: center;" v-model="velocity" />
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

      <fieldset>
        <button v-on:click="chords" v-bind:class="{'play' : isChords}">CHORDS</button>
      </fieldset>

    </div>
    <div style="width: 100%;background-color: #333;margin-top: 1em;padding: 0.5em;">
      <pre class="message" v-for="message in messages">{{ message }}</pre>
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

.key-press {
  background-color: orange !important;
}

.message {
  margin: 0;
  text-align: left;
}

</style>
