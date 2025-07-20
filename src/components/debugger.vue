<template>

    <div id="debugger" class="flex h-[100%] w-[20%] flex-col items-center justify-center rounded ">
        <!-- <div id="debug-title" class="py-3 text-3xl text-theme">DEBUG INFO</div> -->
         
        <div id="info" class="p-4 w-[90%] h-[40%] my-4 rounded dark:bg-grey bg-[#fff] flex flex-col justify-center items-center ">
                <div id="image-origin" class="w-[98%] flex items-center justify-center rounded-l bg-[#fff] dark:bg-grey h-[96%]" v-if="!dialogVisible">
                    <el-upload
                        class="flex items-center flex-col w-full h-full justify-center"
                        :before-upload="handleBeforeUpload"

                        drag
                        action=""
                        :limit="1"
                    >
                        <el-icon class="el-icon--upload"><upload-filled /></el-icon>
                        <div class="el-upload__text">
                        Drop file here or <em>click to upload</em>
                        </div>
                        <template #tip>
                        <div class="el-upload__tip">
                            jpg/png files with a size less than 500kb
                        </div>
                        </template>
                    </el-upload>

                    
                </div>
                <div id="img-preview" class="w-[98%] h-full flex flex-col items-center justify-center" v-if="dialogVisible">
                        <img :src="dialogImageUrl" alt="Preview Image" />
                        <span>{{ filename }}</span>
                </div>
        </div>
        
        <div class="mb-0 w-[90%] h-[60%] my-4 flex items-center justify-center flex-col bg-[#fff] dark:bg-grey shadow-xl rounded ">
            <textarea
                class="p-4 overflow-y-auto [&::-webkit-scrollbar]:hidden [-ms-overflow-style:none] [scrollbar-width:none] w-full h-full resize-none rounded bg-[#fff] dark:bg-grey outline-0"
                placeholder="Echarts Json:"
                v-model="json_response"
                >
                
            </textarea>
            
            <div id="buttons" class="flex h-[10%] w-full justify-center px-1">
                <el-button class="mx-0" @click="setEchartsOptions" type="primary">
                    <el-icon><Picture /></el-icon><p>&nbsp;RawGEN</p>
                </el-button>
                <el-button class="mx-0" type="danger" @click="resetStatus">
                    <el-icon><CloseBold /></el-icon><p>&nbsp;Clear</p>
                </el-button>   
                <el-button class="mx-0" @click="sendVLMRequest" type="success">
                    <el-icon><View /></el-icon><p>&nbsp;VLM it!</p>
                </el-button>
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { ref , onMounted, inject} from 'vue'
import * as echarts from 'echarts';
import axios from 'axios';
const img_base64 = ref("")
const json_response = ref()
const myChart = ref()

import { ElDialog, UploadProps, UploadFile, ElMessage } from "element-plus"
const base_url = inject('base_url', ref("api.siliconflow.cn/v1/chat/completions"))
const api_key = inject('api_key', ref(""))
const PROMPT = `提取图表中的信息，包括但不限于图表的标题、横纵坐标、图例、数据的标注label等的信息。将提取的信息直接返回兼容5.5.1版本的ECharts options符合格式的JSON字符串,回复格式类似如下:
                {
                "title": {
                    "left": "center"
                    },
                "tooltip": {},
                "legend": {
                    "orient": "vertical",
                    "left": "left"
                },
                "series": []
            }，不需要说明解释返回内容，请严格遵循json语法返回。`

const dialogImageUrl = ref("")
const dialogVisible = ref(false)
const filename = ref("")

const resetStatus = () => {
    dialogVisible.value = false;
    img_base64.value = "";
    dialogImageUrl.value = "";
    json_response.value = ""
    myChart.value.clear();
}
const handleBeforeUpload = (file:File) => {
    return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    filename.value = file.name;
    // console.log(file.name)
    reader.onload = (e) => {
        const base64 = e.target.result;
        img_base64.value = base64.toString();
        // console.log(img_base64.value)
        
        dialogImageUrl.value = img_base64.value;
        dialogVisible.value = true;
        resolve(false); // 阻止默认上传逻辑
    };
    reader.onerror = (error) => {
        console.error('读取文件失败:', error);
        reject(false);
    };
    
    
    });
}

const sendVLMRequest = async () => {
    try {
        const response = await axios.post(
        "https://"+"api.siliconflow.cn/v1/chat/completions", 
        {
        "model":"Qwen/Qwen2-VL-72B-Instruct",
        "messages": [{
            "role": "user", 
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                    "url": `${img_base64.value}` 
                    }
                },
                {
                    "type": "text",
                        "text": PROMPT,
            }
            ]
        }],
        "temperature": 0.4,
        "top_p": 0.7,
        "max_tokens": 4096,
    }, 
        {
            headers: {
              'Authorization': `Bearer ${api_key.value}`,
              'Content-Type': 'application/json'
            }
        });
    console.log('Response:', response.data.choices[0].text);
    json_response.value = response.data.choices[0].message.content.replace("```json","").replace("```","")
    myChart.value.setOption(JSON.parse(json_response.value))
    } 
    catch (error) {
    console.error('Error:', error);
  }
};


onMounted(()=>{
    myChart.value = echarts.init(document.getElementById('echart-display'));  
    // 绘制图表
    
})


const setEchartsOptions = () =>{
    
    myChart.value.setOption(JSON.parse(json_response.value))
    }

</script>

