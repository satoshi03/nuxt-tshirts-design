<template>
  <v-layout
    column
    justify-center
    align-center>
    <v-flex
      xs12
      >
      <div class="text-xs-center text-sm-center text-md-center text-lg-center">
        <v-stepper v-model="e1">
         <v-stepper-header>
           <v-stepper-step :complete="e1 > 1" step="1">元画像を選びましょう</v-stepper-step>
           <v-divider></v-divider>
           <v-stepper-step :complete="e1 > 2" step="2">画像をモザイク状にしましょう</v-stepper-step>
           <v-divider></v-divider>
           <v-stepper-step :complete="e1 > 3" step="3">完成です！</v-stepper-step>
         </v-stepper-header>

         <v-stepper-items>
           <v-stepper-content step="1">
            <v-text-field label="画像ファイルを選んでください" @click='pickFile' v-model='imageName' prepend-icon='attach_file'></v-text-field>
            <input
                type="file"
                style="display: none"
                ref="image"
                accept="image/*"
                @change="onFilePicked"
            />
            <img :src="imageUrl" class="uploaded-image" max-height="400px" min-height="200px"/>
            <v-card-actions>
              <v-spacer />
              <v-btn v-if="imageUrl" color="orange darken-4" dark round @click="saveImage()">次へ</v-btn>
            </v-card-actions>
           </v-stepper-content>

           <v-stepper-content step="2">
             <p>マウスポインタを画像の上で動かすと画像が鮮明になります</p>
             <mosaic-canvas ref="canvas" v-if="imageUrl" :imgURL="imageUrl"></mosaic-canvas>
             <p class="st2-description">画像が好みの鮮明度合いになったら次へを押してください</p>
             <v-card-actions>
               <v-btn v-if="imageUrl" color="orange darken-4" dark round @click="back()">戻る</v-btn>
               <v-spacer />
               <v-btn v-if="imageUrl" color="orange darken-4" dark round @click="saveCanvasImage()">次へ</v-btn>
             </v-card-actions>
           </v-stepper-content>

           <v-stepper-content step="3">
             <div v-show="mosaicImageURL">
               <t-shirts-canvas ref="tshirtsCanvas" :imageURL="getMosaicImageURL"></t-shirts-canvas>
             </div>
             <v-card-actions>
               <v-btn color="orange darken-4" dark round @click="back()">戻る</v-btn>
               <v-spacer />
             </v-card-actions>
           </v-stepper-content>
         </v-stepper-items>
        </v-stepper>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
import firebase from '~/plugins/firebase'
import MosaicCanvas from '~/components/MosaicCanvas.vue'
import TShirtsCanvas from '~/components/TShirtsCanvas.vue'

export default {
  components: {
    MosaicCanvas,
    TShirtsCanvas,
  },
  data () {
    return {
      e1: 1,
      imageName: '',
      imageUrl: '',
      imageFile: '',
      imageSaved: false,
      mosaicImageURL: '',
    }
  },
  computed: {
    getMosaicImageURL () {
      console.log(this.mosaicImageURL)
      return this.mosaicImageURL
    }
  },
  methods: {
    pickFile () {
      this.$refs.image.click()
    },
    onFilePicked (e) {
      const files = e.target.files
      if(files[0] !== undefined) {
        this.imageName = files[0].name
        if(this.imageName.lastIndexOf('.') <= 0) {
          return
        }
        const fr = new FileReader ()
        fr.readAsDataURL(files[0])
        fr.addEventListener('load', () => {
          this.imageUrl = fr.result
          this.imageFile = files[0] // this is an image file that can be sent to server...
        })
      } else {
        this.imageName = ''
        this.imageFile = ''
        this.imageUrl = ''
      }
    },
    saveImage () {
      const uploaded = this.saveOnCloudStorage()
      this.e1 = 2
      if (uploaded) {
        this.e1 = 2
      }
    },
    saveOnCloudStorage () {
      const ref = firebase.storage().ref()
      const imageRef = ref.child('images/' + this.imageName)
      imageRef.put(this.imageFile).then(function(snapshot) {
        return true
      })
      return false
    },
    saveCanvasImage () {
      const image = this.$refs.canvas.getMosaicImage()
      if (!image) {
        return
      }

      const ref = firebase.storage().ref()
      const imageRef = ref.child('images/mosaic_' + this.imageName)

      const _this = this
      imageRef.putString(image, 'data_url').then(function(snapshot) {
        _this.imageSaved = true
        console.log(snapshot)
        snapshot.ref.getDownloadURL().then(function(downloadURL) {
          console.log('File available at', downloadURL);
          _this.mosaicImageURL = downloadURL
        })
      })
      this.e1 = 3
      console.log(this.mosaicImageURL)
    },
    getDownloadURL () {
      if (!this.$refs.tshirtsCanvas) {
        return ""
      }

      let url = this.$refs.tshirtsCanvas.getDownloadURL()
      // This can be downloaded directly:
      var xhr = new XMLHttpRequest();
      xhr.responseType = 'blob';
      xhr.onload = function(event) {
        var blob = xhr.response;
      };
      xhr.open('GET', url);
      xhr.send();
    },
    back () {
      this.e1 -= 1
    }
  }
}
</script>

<style>
.uploaded-image {
  min-height: 200px;
  max-height: 400px;
}

.st2-description {
  padding-top: 20px;
}
</style>
