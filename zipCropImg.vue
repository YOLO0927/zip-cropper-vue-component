<template lang="html">
  <div @click="handClick" class="zipCropImg">
    <slot></slot>
    <input v-if="!cropBoxShow" class="zipCropImg-input" ref="input" @change="upload($event)" type="file" accept="image/*">
    <div v-show="cropBoxShow" class="cropBox">
      <vue-cropper
          ref='cropper'
          :guides="true"
          :view-mode="1"
          drag-mode="move"
          :aspectRatio="cropBoxRatio"
          :minCropBoxWidth="minCropBoxWidth"
          :minCropBoxHeight="minCropBoxHeight"
          :cropBoxMovable="cropBoxMovable"
          :cropBoxResizable="cropBoxResizable"
          :background="true"
          :rotatable="true"
          :src="imgSrc"
          alt=" "
          :toggleDragModeOnDblclick="false"
          :img-style="{height: '100%'}"
          :container-style="{width: containerWidth + 'px', height: containerHeight + 'px'}">
      </vue-cropper>
      <div class="commands">
        <button @click.stop="rotateImg(90)" type="button" name="button">旋转图片</button>
        <button @click.stop="cropComplete" type="button" name="button">完成裁剪</button>
      </div>
    </div>
  </div>
</template>

<script>
import lrz from 'lrz' // 压缩插件 https://github.com/think2011/localResizeIMG
import VueCropper from 'vue-cropperjs' // 裁剪插件 https://github.com/Agontuk/vue-cropperjs

export default {
  data () {
    return {
      imgSrc: '',
      cropBoxShow: false
    }
  },
  props: {
    beforeUpload: {
      type: Function
    },
    isZip: {
      type: Boolean,
      default: true
    },
    isCrop: {
      type: Boolean,
      default: true
    },
    zipPercent: {
      type: Number,
      default: 0.7
    },
    containerWidth: {
      type: Number,
      default: 375
    },
    containerHeight: {
      type: Number,
      default: 400
    },
    minCropBoxWidth: {
      type: Number,
      default: 0
    },
    minCropBoxHeight: {
      type: Number,
      default: 0
    },
    cropBoxMovable: {
      type: Boolean,
      default: false
    },
    cropBoxResizable: {
      type: Boolean,
      default: false
    },
    cropBoxRatio: {
      type: Number
    },
    outputCanvasData: {
      type: Object
    }
  },
  methods: {
    handClick () {
      !this.cropBoxShow ? this.$refs.input.click() : 0
    },
    upload (e) {
      let file = e.target.files[0]
      let begin = this.beforeUpload(file)
      // 上传前回调，若传入函数返回 false 或 0 则中断上传，可用于上传前校验或检查
      if (!begin && begin !== undefined) {
        return 0
      }
      // 三种情况分别分发事件携带参数
      if (this.isZip && this.isCrop) {
        this.zipImg(file).then((zippedFile) => {
          this.cropImg(zippedFile.file)
        })
      } else if (this.isZip && !this.isCrop) {
        this.zipImg(file).then((zippedFile) => {
          this.$emit('success', zippedFile)
        })
      } else if (!this.isZip && this.isCrop) {
        this.cropImg(file)
      }
    },
    zipImg (file) {
      return lrz(file, {
        quality: this.zipPercent
      }).then((rst) => {
        // 压缩处理成功执行
        return rst
      }).catch((err) => {
        // 压缩处理失败执行
        this.$emit('error', err)
      })
    },
    cropImg (file) {
      this.cropBoxShow = true
      if (typeof FileReader === 'function') {
        const reader = new FileReader()
        reader.onload = (event) => {
          this.imgSrc = event.target.result
          // rebuild cropperjs with the updated source
          this.$refs.cropper.replace(event.target.result)
        }
        reader.readAsDataURL(file)
      } else {
        this.$emit('error', 'Sorry, FileReader API not supported')
      }
    },
    rotateImg (rotateDeg) {
      this.$refs.cropper.rotate(rotateDeg)
    },
    cropComplete () {
      this.$nextTick(() => {
        this.cropBoxShow = false
        this.$emit('success', this.$refs.cropper.getCroppedCanvas(this.outputCanvasData))
      })
    }
  },
  components: {
    'vue-cropper': VueCropper
  }
}
</script>

<style lang="css">
  .zipCropImg{
    display: inline-block;
  }
  .zipCropImg .zipCropImg-input{
    display: none;
  }
  .zipCropImg .cropBox{
    position: fixed;
    left: 0;
    top: 0;
    background-color: rgba(0,0,0,.7);
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
  }
  .zipCropImg .commands{
    bottom: 5%;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 100%;
    text-align: center;
  }
  .zipCropImg .commands button{
    margin: 0 10px;
    padding: 9px 15px;
    font-size: 12px;
    border-radius: 3px;
    color: #fff;
    background-color: #409eff;
    display: inline-block;
    line-height: 1;
    white-space: nowrap;
    cursor: pointer;
    border: 1px solid #409eff;
    -webkit-appearance: none;
    text-align: center;
    box-sizing: border-box;
    outline: none;
    font-weight: 500;
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
  }
</style>
