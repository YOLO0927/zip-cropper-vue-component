<template>
  <div id="app">
    <zip-crop-img
      :isZip="true"
      :isCrop="true"
      :cropBoxRatio="bodyWidth/200"
      :minCropBoxWidth="bodyWidth"
      :minCropBoxHeight="200"
      :containerWidth="bodyWidth"
      :beforeUpload="beforeUpload"
      :containerHeight="bodyHeight"
      @success="uploadSuccess">
      <button class="btn" type="button" name="button">点击上传</button>
    </zip-crop-img>
  </div>
</template>

<script>
import zipCropImg from '@/components/zipCropImg'

export default {
  name: 'app',
  data () {
    return {
    }
  },
  computed: {
    bodyWidth () {
      return document.body.clientWidth
    },
    bodyHeight () {
      return document.body.clientHeight
    }
  },
  methods: {
    beforeUpload (file) {
      console.log(file)
    },
    uploadSuccess (file) {
      console.log(file)
    }
  },
  components: {
    'zip-crop-img': zipCropImg
  }
}
</script>

<style>
  html,body{
    margin: 0 0;
    padding: 0 0;
    width: 100%;
    height: 100%;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 100%;
    height: 100%;
    /*margin-top: 60px;*/
  }
  .btn{
    padding: 9px 15px;
    font-size: 12px;
    border-radius: 3px;
    color: #fff;
    background-color: #409eff;
    border-color: #409eff;
    display: inline-block;
    line-height: 1;
    white-space: nowrap;
    cursor: pointer;
    border: 1px solid #dcdfe6;
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
