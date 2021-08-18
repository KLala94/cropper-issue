<template>
  <div id="app">
    <Cropper
      class="cropper"
      ref="cropper"
      :src="image"
      :default-size="getMaxCroppedSize"
      :stencil-props="{ aspectRatio }"
    />

    <div class="boutonsSelect slidecontainer" style="">
      <div class="dimslide">
        <label for="h"
          ><span class="label"
            >height:<br /><b>{{ height }}</b> cm</span
          ></label
        ><input
          type="range"
          min="50"
          max="1000"
          step="5"
          v-model="height"
          @change="resize()"
          id="h"
        />
      </div>
      <div class="dimslide">
        <label for="l"
          ><span class="label"
            >width:<br /><b>{{ width }}</b> cm</span
          ></label
        ><input
          type="range"
          min="50"
          max="1000"
          step="5"
          v-model="width"
          @change="resize()"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { Cropper } from "vue-advanced-cropper";
import debounce from "debounce";
import "vue-advanced-cropper/dist/style.css";

export default {
  name: "App",
  data() {
    return {
      width: 300,
      height: 200,
      aspectRatio: 300 / 200,
      image:
        "https://images.pexels.com/photos/6160430/pexels-photo-6160430.jpeg?auto=compress&cs=tinysrgb&h=1200&w=1800",
    };
  },
  components: {
    Cropper,
  },
  created() {
    this.resize = debounce(this.resize, 1000);
  },
  methods: {
    getMaxCroppedSize({ aspectRatio, imageSize }) {
      const  imageRatio = imageSize.width / imageSize.height;

      if (aspectRatio > imageRatio) {
        return {
          width: imageSize.width,
          height: imageSize.width / aspectRatio,
        };
      } else {
        return {
          width: imageSize.height * aspectRatio,
          height: imageSize.height,
        };
      }

    },
    resize() {
      let aspectRatio = this.width / this.height;

      this.$refs.cropper.resetVisibleArea();

      this.$refs.cropper.setCoordinates(({  imageSize }) => {
          const { width, height } = this.getMaxCroppedSize({ aspectRatio, imageSize });
          return {
            width,
            height,
            left: imageSize.width / 2 - width / 2,
            top: imageSize.height / 2 - height / 2,
          };
        }
      );
    },
  },
};
</script>

<style>
.label {
  width: 60px;
  display: inline-block;
}
.cropper {
  height: 600px;
}
</style>
