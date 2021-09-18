
<template>
  <div class="relative">
    <div id="cameraArea">
    </div>
    <div v-if="code.length"  class="results-modal fixed top-0 left-0 w-screen h-screen z-30 flex items-center justify-center">
      <div class="bg-white rounded-lg p-8 w-10/12 h-2/3">
        <img src="/jacket-shimobe.jpg" alt="">
        <p class="text-center mt-4">ニューロナの曲をひろった！</p>
        <p class="resultCode">{{ code }}</p>
      </div>
    </div>
    <div class="fixed z-10 bottom-0 left-0">
      <button @click="startScan">Scan</button>
      <button aria-label="close" @click.prevent.stop="stopScan">Stop</button>
    </div>

  </div>
</template>

<script>


export default {
  data() {
    return {
      Quagga: null,
      code: "",
      resultImgSrc: null
    };
  },
  methods: {
    startScan() {
      this.code = "";
      this.initQuagga();
    },
    stopScan() {
      this.Quagga.offProcessed(this.onProcessed)
      this.Quagga.offDetected(this.onDetected)
      this.Quagga.stop();
    },
    initQuagga() {
      this.Quagga = require("quagga");
      this.Quagga.onProcessed(this.onProcessed);
      this.Quagga.onDetected(this.onDetected);

      // 設定
      const config = {
        inputStream: {
          name: "Live",
          type: "LiveStream",
          target: document.querySelector("#cameraArea"),
          constraints: {width : window.innerWidth, height : window.innerHeight, facingMode: "environment" }
        },
        numOfWorkers: navigator.hardwareConcurrency || 4,
        decoder: { readers: ["ean_reader", "ean_8_reader"] }
      };
      this.Quagga.init(config, this.onInit);
    },
    onInit(err) {
      if (err) {
        console.log(err);
        return;
      }
      console.info("Initialization finished. Ready to start");
      console.log(this.Quagga.canvas.dom)
      this.Quagga.start();
    },
    onDetected(success) {
      this.code = success.codeResult.code;
      // 取得時の画像を表示
      //this.resultImgSrc= this.Quagga.canvas.dom.image.toDataURL()
      this.Quagga.stop();
    },
    onProcessed(result) {
      const drawingCtx = this.Quagga.canvas.ctx.overlay;
      const drawingCanvas = this.Quagga.canvas.dom.overlay;

      if (result) {
        // 検出中の緑の線の枠
        if (result.boxes) {
          drawingCtx.clearRect(0, 0, drawingCanvas.width, drawingCanvas.height);
          const hasNotRead = (box) => box !== result.box;
          result.boxes.filter(hasNotRead).forEach((box) => {
            this.Quagga.ImageDebug.drawPath(box, { x: 0, y: 1 }, drawingCtx, {
              color: "green",
              lineWidth: 2
            });
          });
        }

        // 検出に成功した瞬間の青い線の枠
        if (result.box) {
          this.Quagga.ImageDebug.drawPath(
            result.box,
            { x: 0, y: 1 },
            drawingCtx,
            {
              color: "blue",
              lineWidth: 2
            }
          );
        }

        // 検出に成功した瞬間の水平の赤い線
        if (result.codeResult && result.codeResult.code) {
          this.Quagga.ImageDebug.drawPath(
            result.line,
            { x: "x", y: "y" },
            drawingCtx,
            {
              color: "red",
              lineWidth: 3
            }
          );
        }
      }
    }
  }
}
</script>

<style>
#cameraArea {
  /* overflow: hidden;
  width: 320px;
  height: 240px;
  margin: auto;
  position: relative;
  display: flex;
  align-items: center; */
  position: relative;
  width: 100vw;
  height: 100vh;
}
#cameraArea video,
#cameraArea canvas {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
button {
  width: 100px;
  height: 40px;
  background-color: #fff;
  border: 1px solid #333;
  margin-top: 30px;
}
.resultImg {
  width: 100%;
}
.resultCode {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
}
.getMessage {
  color: red;
}
</style>
