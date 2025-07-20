<template>
    <div id="image" class="h-full flex flex-col w-[80%] ">

        <div id="echarts" class="h-full  flex flex-col justify-center items-center">
            <div id="echart-display" class="w-[98%] flex items-center justify-center rounded-l shadow-xl h-[96%] dark:bg-grey rounded bg-[#fff] ">
                 
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

</script>

<style scoped>
.avatar-uploader .avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  text-align: center;
}
</style>