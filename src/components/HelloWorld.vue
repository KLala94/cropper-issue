<template>
  <main>
    <Cropper
      class="cropper"
        ref="cropper"
      :src="image"
      :default-size="getMaxCroppedSize"
      :stencil-props="{ aspectRatio }"
    />

    <div>
      <div >
        <input
          id="height" type="number"
          @input="changeHeight($event.target.value);"
          @change="resize();"
          placeholder="3000" min="100" max="3000"
        />
      </div>
      <div  >
        <div v-for="(item, index) in [1,2,3]" :key="index">
          <input
            id="width" type="number"
            @input="changeIndexedWidth(index, $event.target.value); resize();"
            placeholder="1500" min="100" max="1500"
          />
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import {
  defineComponent,
  computed,
  ref,
  reactive,
} from "vue";
import { Cropper } from "vue-advanced-cropper";
import "vue-advanced-cropper/dist/style.css";
import debounce from "debounce";

export default defineComponent({
  components: {
    Cropper,
  },
  data() {
    return {
      image:
        "https://images.pexels.com/photos/6160430/pexels-photo-6160430.jpeg?auto=compress&cs=tinysrgb&h=1200&w=1800",
    };
  },
    created() {
    this.resize = debounce(this.resize, 500);
  },
  setup() {
    const widths = ref([1500, 0, 0]);
    const height = ref(3000);
    const imageSelection = reactive({
      width : 0,
      height: 0,
      crop: {
        width : 0,
        height: 0,
        left  : 0,
        top   : 0,
      }
    })

    const changeHeight = (val) => {
      // check for limits, if ok then set value
      height.value = mmToPx(val);
    };
    // width
    let changeIndexedWidth = (index, val) => {
      // check for limits, if ok then set value
      widths.value[index] = mmToPx(val);
    };
    const change = ({ image }) => {
      imageSelection.width = image.width;
      imageSelection.height = image.height;
    };
    // Functions //////////////////////////////////////////////////////
    function mmToPx(distance) {
      return distance / 10;
    }

    function sumArray(array) {
      return array.reduce((acc, inc) => acc + inc, 0)
    }
    // Functions end //////////////////////////////////////////////////

    const aspectRatio = computed(() => {
      return String(sumArray(widths.value) / height.value);
    });

    return {
      change,
      changeHeight,
      changeIndexedWidth,
      widths,
      height,
      aspectRatio,
      sumArray,
    };
  },
  methods: {
    getMaxCroppedSize({ imageSize }) {
      const  imageRatio = imageSize.width / imageSize.height;
      const aspectRatio = Number(this.aspectRatio);
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

      this.$refs.cropper.resetVisibleArea();

      this.$refs.cropper.setCoordinates(({  imageSize }) => {
          const { width, height } = this.getMaxCroppedSize({ imageSize });
          return {
            width,
            height,
            left: imageSize.width / 2 - width / 2,
            top: imageSize.height / 2 - height / 2,
          };
        }
      );
    },
  }
});
</script>

<style lang="scss" scoped>
  .cropper {
    height: 600px;
  }
</style>
