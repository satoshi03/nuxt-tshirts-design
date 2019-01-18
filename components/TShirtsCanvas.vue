<template>
  <v-layout
    justify-content
  >
    <v-flex>
      <div id="t-shirts-canvas">
        <v-stage ref="stage" :config="stageSize">
          <v-layer ref="bgLayer">
              <v-image
                :config="{
                  image: bgImage,
                  x: 100,
                  y: 0,
                }"
              />
          </v-layer>
          <v-layer ref="layer">
              <v-image
                :config="{
                  image: image,
                  x: 200,
                  y: 100,
                }"
              />
          </v-layer>
        </v-stage>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  props: {
    imageURL: String,
  },
  data() {
    return {
      stageSize: {
        width: 0,
        height: 0
      },
      image: null,
      imageX: 0,
      imageY: 0,
      bgImage: null,
      bgImageX: 0,
      bgImageY: 0
    }
  },
  mounted() {
    if (process.client) {
      this.stageSize.width = 600
      this.stageSize.height = 400

      // create background image (T-Shirts)
      const bgImage = new window.Image()
      bgImage.setAttribute('crossOrigin', 'anonymous')
      bgImage.src = require('~/assets/tshirts.png')
      bgImage.width = 400
      bgImage.height = 400
      bgImage.onload = () => {
        // set image only when it is loaded
        this.bgImage = bgImage
        this.bgImageX = this.stageSize.width / 2,
        this.bgImageY = this.stageSize.height / 2
      }

      /*
      const image = new window.Image()
      image.src = "https://firebasestorage.googleapis.com/v0/b/t-shirts-design.appspot.com/o/images%2Fmosaic_profile2.jpeg?alt=media&token=20d75892-6356-4abc-a428-659411ff82b4"
      image.width = 200
      image.height = 100
      image.onload = () => {
        // set image only when it is loaded
        this.image = image
      }
      */
    }
  },
  watch: {
    imageURL: function (newVal, oldVal) {
      if (!newVal) {
        return
      }
      const image = new window.Image()
      image.src = newVal
      image.width = 200
      image.height = 100
      image.onload = () => {
        // set image only when it is loaded
        this.image = image
      }
    }
  },
  methods: {
    downloadURI(uri) {
      var link = document.createElement("a")
      link.href = uri
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
    },
    getDownloadURL () {
      const stage = this.$refs.stage.getNode()
      return stage.toDataURL()
    }
  }
}
</script>
