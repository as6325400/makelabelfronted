<script setup>
import TheWelcome from '../components/TheWelcome.vue'
import axios from 'axios';
import { ref, onMounted } from 'vue';

const env = import.meta.env;
const responseData = ref('');
const path = "/Users/as6325400/Project/CCU-Verification-decode/photo/";
let response;
let defaultValue = ref('')
let FinishNumValue = ref('')

let api = env.VITE_SERVER

onMounted(() => {
  fetchData();
})

const fetchData = async () => {
  try {
    response = await axios.get(api + '/image');
    console.log(response)
    responseData.value = api + '/static/' + response.data[0]['filename'];
    defaultValue.value = response.data[0]['ocrlabel'];
    console.log(responseData.value)
  } catch (error) {
    console.error('Error:', error);
  }
};

const finishNum = async () => {
  try {
    let response = await axios.get(api + '/finishNum');
    console.log(response)
    FinishNumValue.value = response.data[0]["count(*)"];
  } catch (error) {
    console.error('Error:', error);
  }
};

const updateData = async () => {
  try {
    let filename = response.data[0]['filename'];
    let num = response.data[0]['times'] + 1;
    console.log(filename, num)
    response = await axios.get(api + '/updateTimes', {
      params:{
        filename:filename,
        value:defaultValue.value,
        num:num
      }
    });
    console.log(response)
  } catch (error) {
    console.error('Error:', error);
  }
};

const handleclick = async () => {
  await updateData();
  await fetchData();
  await finishNum();
}


const inputValue = ref(''); // 使用ref来创建响应式数据
</script>

<template>
  <img class = "mx-auto" :src="responseData">
  <div class="w-1/4 mx-auto">
    <input type="text" class = "border border-solid border-gray-400 p-2 w-full text-center"  v-model="defaultValue" placeholder="請輸入驗證碼">
  </div>
  <div class="w-1/4 mx-auto">
    <div class="w-1/4 text-center my-4 text-2xl bg-red-600 mx-auto text-white border-b hover:bg-red-200" @click = "handleclick"> 確認 </div>
    <div class="text-center"> 已完成數量 {{ FinishNumValue }}</div>
  </div>

</template>
