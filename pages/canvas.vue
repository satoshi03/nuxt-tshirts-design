<template>
  <div id="tshirts-canvas">
  </div>
</template>

<script>
import Konva from "konva"

export default {
  data () {
    return {
      canvas: {},
      context: {},
      stage: {},
      baseImageURL: require("~/assets/mona.jpg"),
      imageObj: null,
    }
  },
  mounted () {
    const width = 400
    const height = 600

    this.stage = new Konva.Stage({
      container: 'tshirts-canvas',
      width: width,
      height: height
    })

    let layer = new Konva.Layer()
    this.imageObj = new Konva.Image()

    const _this = this
    this.imageObj.onload = () => {
      let bgImage = new Konva.image({
        x: 100,
        y: 100,
        width: 300,
        height: 200,
        image: _this.imageObj,
      })

      console.log("onloaed")

      layer.add(bgImage)

      _this.stage.add(layer)

      _this.stage.draw()
    }

    console.log(this.baseImageURL)
    this.imageObj.src = this.baseImageURL
  },
  methods: {
    renderImages () {
    }
  }
}
</script>
