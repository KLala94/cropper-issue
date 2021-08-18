<template>
  <main>
    <div>
      <div >
        <div >
          <input
            id="height"
            type="number"
            v-model="valHeight[0]"
            @input="heightOnePlate(valHeight[0])"
            placeholder="3000"
            min="100"
            max="3000"
          />
        </div>
      </div>
      <div  >
        <div v-for="(item, index) in [1,2,3]" :key="index">
          <div>
            <input
              id="width"
              type="number"
              v-model="valWidth[index]"
              @input="widthMultiplePlates(index, valWidth[index])"
              placeholder="1500"
              min="100"
              max="1500"
            />
          </div>
        </div>
      </div>
    </div>
    <cropper
      class="cropper"
      boundingBoxClass="main-photo"
      :src="require('./DE17-02.jpg')"
      :default-size="defaultSize"
      minWidth="20"
      minHeight="20"
      :stencil-props="{
        aspectRatio: aspectRatio,
      }"
      @change="change"
    ></cropper>
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
// import Stencil from "./cropper/Stencil.vue";
export default defineComponent({
  components: {
    Cropper,
    // eslint-disable-next-line vue/no-unused-components
    // Stencil,
  },
  data() {
    return {
    };
  },
  setup() {
    const valWidth = reactive([undefined, undefined, undefined]);
    const valHeight = reactive([]);

    const platesNumberWidth = ref([1500, 0, 0]);
    const platesNumberHeight = ref(3000);
    const imagePlacement = reactive({
      width : 0,
      height: 0,
      crop: {
        width : 0,
        height: 0,
        left  : 0,
        top   : 0,
      }
    })
    // function limit(number, min, max)  {
    //   if(number < min) return min;
    //   return number > max ? max : number;
    // }
    // function ifNotValueThenMax(number, max){
    //   if(!number) return max
    //   return Number(number);
    // }
    const heightOnePlate = (val) => {
      valHeight[0] =  Number(val) || undefined;
      platesNumberHeight.value = val;
    };
    // width
    let widthMultiplePlates = (index, val) => {
      valWidth[index] = Number(val) || undefined;
      platesNumberWidth.value[index] = val;
    };
    const change = ({ image }) => {
      imagePlacement.width = image.width;
      imagePlacement.height = image.height;
    };

    const neededHeight = computed(() => {
      return platesNumberHeight.value / 10;
    })
    const neededWidth = computed(() => {
      return platesNumberWidth.value.reduce((acc, inc) => acc + inc, 0) / 10;
    })

    const aspectRatio = computed(() => {
      return String(neededWidth.value / neededHeight.value);
    });

    const defaultSize = computed(() =>  {
      let aspectRatio = Number(neededWidth.value/neededHeight.value) || 1;
      if (aspectRatio > imagePlacement.width / imagePlacement.height) {
        return {
          width: imagePlacement.width,
          height: imagePlacement.width / aspectRatio,
        };
      } else {
        return {
          width: imagePlacement.height * aspectRatio,
          height: imagePlacement.height,
        };
      }
    })
    return {
      defaultSize,
      change,
      heightOnePlate,
      widthMultiplePlates,
      platesNumberWidth,
      platesNumberHeight,
      valWidth,
      valHeight,
      aspectRatio,
    };
  },
});
</script>

<style lang="scss" scoped>
</style>
