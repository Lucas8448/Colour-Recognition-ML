<template>
  <div id="app">
    <h1>Color Learner</h1>
    <video ref="video" autoplay playsinline></video>
    <div>
      <label for="color-name">Name:</label>
      <input id="color-name" type="text" v-model="colorName" />
    </div>
    <button @click="capture">Capture Color</button>
    <button @click="train">Train Neural Network</button>
    <div v-if="prediction">Predicted Color: {{ prediction }}</div>
    <h2>All</h2>
    <div v-for="item in list">
      {{ item[0] }}: {{ item[1] }}
    </div>
  </div>
</template>

<script>
import { NeuralNetwork } from "brain.js";

export default {
  data() {
    return {
      list: null,
      colorName: "",
      trainingData: [
        { input: { r: 1, g: 0, b: 0 }, output: { red: 1 } },
        { input: { r: 0, g: 1, b: 0 }, output: { green: 1 } },
        { input: { r: 0, g: 0, b: 1 }, output: { blue: 1 } },
        { input: { r: 1, g: 1, b: 0 }, output: { yellow: 1 } },
        { input: { r: 1, g: 0, b: 1 }, output: { purple: 1 } },
        { input: { r: 0, g: 1, b: 1 }, output: { cyan: 1 } },
        { input: { r: 1, g: 1, b: 1 }, output: { white: 1 } },
        { input: { r: 0, g: 0, b: 0 }, output: { black: 1 } },
        { input: { r: 0.5, g: 0, b: 0 }, output: { maroon: 1 } },
        { input: { r: 0, g: 0.5, b: 0 }, output: { olive: 1 } },
        { input: { r: 0, g: 0, b: 0.5 }, output: { navy: 1 } },
        { input: { r: 0.5, g: 0.5, b: 0 }, output: { lime: 1 } },
        { input: { r: 0.5, g: 0, b: 0.5 }, output: { fuchsia: 1 } },
        { input: { r: 0, g: 0.5, b: 0.5 }, output: { teal: 1 } },
        { input: { r: 0.5, g: 0.5, b: 0.5 }, output: { silver: 1 } },
        { input: { r: 0.75, g: 0, b: 0 }, output: { darkred: 1 } },
        { input: { r: 0, g: 0.75, b: 0 }, output: { green: 1 } },
        { input: { r: 0, g: 0, b: 0.75 }, output: { blue: 1 } },
        { input: { r: 0.75, g: 0.75, b: 0 }, output: { yellow: 1 } },
        { input: { r: 0.75, g: 0, b: 0.75 }, output: { purple: 1 } },
        { input: { r: 0, g: 0.75, b: 0.75 }, output: { cyan: 1 } },
        { input: { r: 0.75, g: 0.75, b: 0.75 }, output: { white: 1 } },
        { input: { r: 0.25, g: 0, b: 0 }, output: { maroon: 1 } },
        { input: { r: 0, g: 0.25, b: 0 }, output: { olive: 1 } },
        { input: { r: 0, g: 0, b: 0.25 }, output: { navy: 1 } },
        { input: { r: 0.25, g: 0.25, b: 0 }, output: { lime: 1 } },
        { input: { r: 0.25, g: 0, b: 0.25 }, output: { fuchsia: 1 } },
        { input: { r: 0, g: 0.25, b: 0.25 }, output: { teal: 1 } },
        { input: { r: 0.25, g: 0.25, b: 0.25 }, output: { silver: 1 } },
        { input: { r: 1, g: 0.5, b: 0 }, output: { orange: 1 } },
        { input: { r: 1, g: 0, b: 0.5 }, output: { rose: 1 } },
        { input: { r: 0.5, g: 1, b: 0 }, output: { chartreuse: 1 } },
        { input: { r: 0, g: 1, b: 0.5 }, output: { turquoise: 1 } },
        { input: { r: 0.5, g: 0, b: 1 }, output: { violet: 1 } },
        { input: { r: 0, g: 0.5, b: 1 }, output: { azure: 1 } },
        { input: { r: 1, g: 0.75, b: 0 }, output: { darkorange: 1 } },
        { input: { r: 1, g: 0, b: 0.75 }, output: { deeprose: 1 } },
        { input: { r: 0.75, g: 1, b: 0 }, output: { limegreen: 1 } },
        { input: { r: 0, g: 1, b: 0.75 }, output: { aquamarine: 1 } },
        { input: { r: 0.75, g: 0, b: 1 }, output: { lavender: 1 } },
        { input: { r: 0, g: 0.75, b: 1 }, output: { babyblue: 1 } },
        { input: { r: 1, g: 0.5, b: 0.5 }, output: { salmon: 1 } },
        { input: { r: 0.5, g: 1, b: 0.5 }, output: { lightgreen: 1 } },
        { input: { r: 0.5, g: 0.5, b: 1 }, output: { cornflowerblue: 1 } },
        { input: { r: 0.5, g: 0.25, b: 0 }, output: { brown: 1 } },
        { input: { r: 0.5, g: 0, b: 0.25 }, output: { maroon: 1 } },
        { input: { r: 0.25, g: 0.5, b: 0 }, output: { olivegreen: 1 } },
        { input: { r: 0, g: 0.5, b: 0.25 }, output: { forestgreen: 1 } },
        { input: { r: 0.25, g: 0, b: 0.5 }, output: { indigo: 1 } },
        { input: { r: 0, g: 0.25, b: 0.5 }, output: { sapphire: 1 } },
        { input: { r: 0.75, g: 0.5, b: 0 }, output: { darkorange: 1 } },
        { input: { r: 0.75, g: 0, b: 0.5 }, output: { deeprose: 1 } },
        { input: { r: 0.5, g: 0.5, b: 0.25 }, output: { olive: 1 } },
        { input: { r: 0.25, g: 0.5, b: 0.5 }, output: { teal: 1 } },
        { input: { r: 0.5, g: 0.25, b: 0.5 }, output: { plum: 1 } },
        { input: { r: 0.75, g: 0.75, b: 0.25 }, output: { khaki: 1 } },
        { input: { r: 0.25, g: 0.75, b: 0.75 }, output: { skyblue: 1 } },
        { input: { r: 0.75, g: 0.25, b: 0.75 }, output: { orchid: 1 } },
        { input: { r: 0.5, g: 0.5, b: 0.75 }, output: { periwinkle: 1 } },
        { input: { r: 0.5, g: 0.25, b: 0.25 }, output: { brick: 1 } },
        { input: { r: 0.25, g: 0.5, b: 0.25 }, output: { olive: 1 } },
        { input: { r: 0.25, g: 0.25, b: 0.5 }, output: { steelblue: 1 } },
        { input: { r: 0.75, g: 0.5, b: 0.5 }, output: { coral: 1 } },
        { input: { r: 0.5, g: 0.75, b: 0.5 }, output: { lightgreen: 1 } },
        { input: { r: 0.5, g: 0.5, b: 0.75 }, output: { slateblue: 1 } },
        { input: { r: 0.75, g: 0.75, b: 0.5 }, output: { khaki: 1 } },
        { input: { r: 0.5, g: 0.75, b: 0.75 }, output: { powderblue: 1 } },
        { input: { r: 0.75, g: 0.5, b: 0.75 }, output: { mauve: 1 } },
        { input: { r: 0.75, g: 0.75, b: 0.75 }, output: { lightgrey: 1 } },
        { input: { r: 0.25, g: 0.25, b: 0.25 }, output: { darkgrey: 1 } },
        { input: { r: 1, g: 0.5, b: 0.75 }, output: { raspberry: 1 } },
        { input: { r: 0.75, g: 0.5, b: 1 }, output: { lavender: 1 } },
        { input: { r: 1, g: 0.75, b: 0.75 }, output: { pink: 1 } },
      ],
      prediction: "",
    };
  },
  methods: {
    train() {
      // Define the neural network
      this.network = new NeuralNetwork();

      // Train the neural network
      this.network.train(this.trainingData);
    },
    capture() {
      const rgb = this.getAverageColor();
      if (this.colorName) {
        this.trainingData.push({
          input: {
            r: rgb.r / 255,
            g: rgb.g / 255,
            b: rgb.b / 255,
          },
          output: { [this.colorName]: 1 },
        });
      }
    },
    predictColor() {
      const rgb = this.getAverageColor();
      const input = {
        r: rgb.r / 255,
        g: rgb.g / 255,
        b: rgb.b / 255,
      };

      if (this.network) {
        const output = this.network.run(input);
        console.log(output);
        this.prediction = Object.keys(output).reduce((a, b) =>
          output[a] > output[b] ? a : b
        );
        // sort each item in output and add top 10 to this.list
        this.list = Object.entries(output)
          .sort((a, b) => b[1] - a[1])
          .slice(0, 10);
      }

      requestAnimationFrame(this.predictColor);
    },
    getAverageColor() {
      const video = this.$refs.video;
      const canvas = document.createElement("canvas");
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      const ctx = canvas.getContext("2d");
      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

      const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height).data;

      let r = 0;
      let g = 0;
      let b = 0;

      for (let i = 0; i < imageData.length; i += 4) {
        r += imageData[i];
        g += imageData[i + 1];
        b += imageData[i + 2];
      }

      const numPixels = imageData.length / 4;
      r = Math.floor(r / numPixels);
      g = Math.floor(g / numPixels);
      b = Math.floor(b / numPixels);

      return { r, g, b };
    },
  },
  mounted() {
    this.train()
    navigator.mediaDevices
      .getUserMedia({ video: true, audio: false })
      .then((stream) => {
        this.$refs.video.srcObject = stream;
        this.$refs.video.addEventListener("loadedmetadata", () => {
          requestAnimationFrame(this.predictColor);
        });
      })
      .catch((error) => {
        console.error("Error accessing the camera:", error);
      });
      // create auto prediction every 1000 milliseconds
      setInterval(() => {
        this.predictColor();
      }, 1000);
  },
};
</script>
