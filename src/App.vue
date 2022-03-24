<template>
  <div class="container-lg">
    <input type="file" accept="image/jpeg, image/png" @change="onFileChange" />

    <div class="wrapper pt-4">
      <div id="preview" v-if="image" >
        <img :src="image" />
        <div v-for="(bb, index) in bounding_box" :key="index"
          :style="{
            position: 'absolute',
            border: '3px solid #8AC007',
            top: bb.top,
            left: bb.left,
            height: bb.height,
            width: bb.width,
          }" >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.min.css";
import nvision from "@nipacloud/nvision";

export default {
  name: 'App',
  components: {
  },
  data() {
    return { 
      image: '',
      bounding_box: []
    }
  },
  methods: {
    onFileChange(event) {
      const input = event.target;
      if (input.files && input.files[0]) {
        const reader = new FileReader();
        reader.onload = (e) => {
          const img = e.target.result
          nvision.objectDetection({
            apiKey: 'cdb29f355cb4059995e05420dc8d963f657898bf3a5f2f5e7a88c58279f5e4a0a1c4c4cf874594b42e413fc45c425425ac'
          })
          .predict({
            rawData: img.replace(/data:.+;base64,/, ""),
          })
          .then((data) => {
            const bb = data.detected_objects.map((key) => {
              return {
                top: key.bounding_box.top +'px',
                left: key.bounding_box.left +'px',
                height: key.bounding_box.bottom-key.bounding_box.top +'px',
                width: key.bounding_box.right-key.bounding_box.left +'px',
              }
            })

            this.bounding_box = bb;
            this.image = img;
          });
        }
        reader.readAsDataURL(input.files[0]);
      }
    },
    call() {
      console.log('first')

      nvision.objectDetection({
        apiKey: 'cdb29f355cb4059995e05420dc8d963f657898bf3a5f2f5e7a88c58279f5e4a0a1c4c4cf874594b42e413fc45c425425ac'
      })
      .predict({
        rawData: this.image.replace(/data:.+;base64,/, ""),
      })
      .then((data) => {
        console.log(data)
      });
    }
  }
}
</script>

<style>
body {
  background-color: #e2e2e2;
}

#app {
  padding: 20px;
}

.wrapper{
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-flex-direction: column;
  -ms-flex-direction: column;
  flex-direction: column;
  -webkit-align-items: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
}

#preview {
  /* min-width: 200px;
  height: 700px; */
  border: 3px solid #fff;
  border-radius: 5px;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-align-items: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  position: relative;
}

#preview img {
  height: 100%;
}

.tt {
  position: absolute;
  border: 3px solid #8AC007;
  top: 2.3px;
  left: 2px;
  height: 187px;
  width: 282px;
  /* bottom: 96px; */
  /* right: 288px; */
} 
</style>
