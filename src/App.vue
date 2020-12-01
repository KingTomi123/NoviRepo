<template>
  <div>
    <pre class="debugger" v-if="debug">
      {{ xd }},{{ yd }} and {{ xu }},{{ yu }}
      {{ isMouseDown }}, {{ radius }}
      {{ list }}
    </pre>
    <v-stage
      ref="stage"
      :config="stageSize"
      @mouseup.native="updateCoordinatesUp"
      @mousedown.native="updateCoordinatesDown"
      @mousemove.native="updateCoordinates"
    >
      <v-layer ref="layer">
        <v-line
          v-for="item in list"
          :config="item"
          :key="item.id"
          @mousemove.native="updateCoordinates"
        />
      </v-layer>
    </v-stage>
    
    <div class="tools">
      <input type="color" v-model="stroke" />
      <input type="number" v-model.number="strokeWidth" />
      <button @click="clearLines">Clear canvas</button> 
      <button @click="debug = !debug">Debug</button> 
    </div>
  </div>
</template>

<script>
const width = window.innerWidth;
const height = window.innerHeight;

export default {
  data() {
    return {
      debug: false,
      stroke: 'black',
      strokeWidth: 4,
      list: [],
      stageSize: {
        width: width,
        height: height,
      },
      text: "",
      xd: 1,
      yd: 1,
      xu: 1,
      yu: 1,
      radius: 0,
      isMouseDown: false,
    };
  },
  methods: {
    writeMessage(message) {
      this.text = message;
    },
    updateCoordinatesDown(event) {
      this.isMouseDown = true;
      this.xd = event.clientX;
      this.yd = event.clientY;

      const pos = { points: [this.xd, this.yd, this.xd, this.yd], stroke: this.stroke, strokeWidth: this.strokeWidth };
      this.list.push(pos);
    },
    updateCoordinatesUp(event) {
      this.isMouseDown = false;
      const points = [this.xd, this.yd, this.xu, this.yu];
      this.list[this.list.length - 1].points = points
    },
    updateCoordinates(event) {
      if (this.isMouseDown) {
        this.xu = event.clientX;
        this.yu = event.clientY;
        this.list[this.list.length - 1].points = [this.xd, this.yd, event.clientX, event.clientY];
      }
    },
    clearLines () {
      this.list = []
    }
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
.tools {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 100%;
  background: #EFEFEF;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  z-index: 100;
}

.tools input {
  margin-right: 20px;
}

.debugger {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  overflow-y: auto;
  z-index: 10;
  background: rgba(255, 255, 255, 0.8);
}
</style>