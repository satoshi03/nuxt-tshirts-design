<template>
  <div class="mosaicCanvas">
    <canvas id="paperCanvas" resize></canvas>
  </div>
</template>

<script>
import paper from 'paper'

export default {
  props: {
    imgURL: String
  },
  data () {
    return {
      imageLoaded: false,
      raster: null,
      lastPos: null,
    }
  },
  created () {
    if (process.client) {
      paper.install(window)
    }
  },
  mounted () {
    if (process.client) {
      paper.setup('paperCanvas')
      this.raster = new Raster(this.imgURL)
      this.imageLoaded = false

      const _this = this
      this.raster.on('load', function() {
          _this.imageLoaded = true
          _this.onResize()
      })

      this.raster.visible = false
      this.lastPos = view.center
    }
  },
  methods: {
    moveHandler (event) {
      if (!this.imageLoaded) {
        return
      }

      if (this.lastPos.getDistance(event.point) < 10) {
        return
      }
      this.lastPos = event.point

      let size = event.currentTarget.bounds.size.clone()
      const isLandscape = size.width > size.height

      if (isLandscape) {
        size.width = size.width / 2
      } else {
        size.height = size.height / 2
      }

      var path = new Path.Rectangle({
        point: event.currentTarget.bounds.topLeft.floor(),
        size: size.ceil(),
        onMouseMove: this.moveHandler
      })
      path.fillColor = this.raster.getAverageColor(path)

      var path = new Path.Rectangle({
        point: isLandscape
          ? event.currentTarget.bounds.topCenter.ceil()
          : event.currentTarget.bounds.leftCenter.ceil(),
        size: size.floor(),
        onMouseMove: this.moveHandler
      })
      path.fillColor = this.raster.getAverageColor(path)

      event.currentTarget.remove()
    },
    onResize (event) {
      if (!this.imageLoaded) {
        return
      }

      project.activeLayer.removeChildren()
      this.raster.fitBounds(view.bounds, true)

      new Path.Rectangle({
        rectangle: view.bounds,
        fillColor: this.raster.getAverageColor(view.bounds),
        onMouseMove: this.moveHandler
      })
    },
    getMosaicImage () {
      let canvas = document.getElementById('paperCanvas')
      return canvas.toDataURL()
    }
  }
}
</script>

<style>
</style>
