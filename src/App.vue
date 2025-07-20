<script setup>
import Debugger from './components/debugger.vue'
import image_drop from './components/image_drop.vue';
import 'element-plus/dist/index.css'
import { ref, provide } from 'vue'
import { Search } from '@element-plus/icons-vue'
import { useDark, useToggle } from '@vueuse/core'

const isDark = useDark()
const toggleDark = useToggle(isDark)
const base_url = ref("api.siliconflow.cn")
const api_key = ref("")

provide('base_url', base_url)
provide('api_key', api_key)


</script>
<template>
  <div class="common-layout flex items-center justify-center aspect-16-9-80 dark:bg-dark bg-[#efefef] rounded-2xl shadow-lg">
    <el-container class=" h-full flex flex-col rounded-2xl">
      <el-header class="h-[5%] flex items-center justify-center my-5">
        <div id="title" class="flex w-full h-full rounded text-l items-center">
          <div id="logo" class="text-3xl w-[20%] flex flex-row text-center font-display justify-center items-center gap-4">
            <a href="https://github.com/Dafeigy/Chart-VLM.git" target="_blank" class="text-3xl w-full flex flex-row text-center font-display justify-center items-center gap-4">
              <el-icon><ChatDotSquare /></el-icon><p>Chart-VLM</p></a>
          </div>
          <div class="flex items-center justify-center mx-2">Base URL</div>
          <div  id='baseurl' class="w-[25%] items-center">
            <el-input
              placeholder="Input Siliconflow"
              style="width: 100%"
              v-model="base_url"
            >
              <template #prepend>https://</template>
              <template #append>/v1/chat/completions</template>
            </el-input>
          </div>
          <div class="flex items-center justify-center mx-2"> API KEY</div>
          <div id="apikey" class="flex items-center w-[30%]">
              <el-input
              v-model="api_key"
              style="width: 90%"
              type="password"
              placeholder="Type your Siliconflow API Keys"
              show-password
            />
            <el-button  class="ml-2" type="success" ><a href="https://cloud.siliconflow.cn/i/9CZQzRc8" target="_blank">Get API keys</a></el-button>
          </div>
        </div>
        
          <el-button @click="toggleDark()">
             <el-icon v-if='isDark'><Moon />
             </el-icon>
             <el-icon v-else><Sunny />
             </el-icon>
            <span class="ml-2">{{ isDark ? 'Dark' : 'Light' }}</span>
          </el-button>
      </el-header>
      <el-main class="h-[95%] w-[100%] flex flex-row justify-center items-center" >
        <div id="container" class="h-full w-full flex flex-row">
          <Debugger/>
          <image_drop/>
        </div>
          
      </el-main>
    </el-container>
  </div>
</template>


<style>
.input-with-select .el-input-group__prepend {
  background-color: var(--el-fill-color-blank);
}
</style>