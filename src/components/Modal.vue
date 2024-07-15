<template>
  <div class="modal" v-if="isVisible">
    <div class="modal-content">
      <h2>导入</h2>
      <input type="file" id="input" @change="handleFileChange" />
      <h2>下载</h2>
      <Downloader source="http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4"
    vid="a175d6b01b4271ee80980764b3ec0102" playauth="" :url="url" :actionflow_id="actionflow_id"
    :global-data="globalData" :set-global-data="setGlobalData" :course_log_pk="0" />
      <button @click="$emit('close')">关闭</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, defineProps, defineEmits } from 'vue'
import Downloader from './Downloader.vue'
import readXlsxFile from 'read-excel-file'

const eMap = ref(new Map())

const handleFileChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    readXlsxFile(file).then((rows) => {
      const map = new Map();
      const arr1 = [];
      const arr2 = [];
      const arr3 = [];

      // Skip the header row and process the data
      for (let i = 1; i < rows.length; i++) {
        arr1.push(rows[i][0]);
        arr2.push(rows[i][1]);
        arr3.push(rows[i][2]);
      }

      // Validate and assign arr1
      arr1.forEach((element, index) => {
        if (isNaN(element)) {
          throw new TypeError(`索引 [${index}] 元素必须是数字类型!`);
        }
      });
      map.set(rows[0][0], arr1);

      // Validate and assign arr2
      arr2.forEach((element, index) => {
        if (isNaN(element)) {
          throw new TypeError(`索引 [${index}] 元素必须是数字类型!`);
        }
      });
      map.set(rows[0][1], arr2);

      // Assign arr3 without validation
      map.set(rows[0][2], arr3);

      eMap.value = map;
      console.log(Array.from(eMap.value.entries()));
    });
  }
}

defineProps({
  isVisible: Boolean,
  title: String,
  content: String
})

defineEmits(['close'])

const globalData = reactive({ value: "http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4" })
const setGlobalData = (obj) => {
  globalData.value = obj.value;
}

const url = ref("");
const actionflow_id = ref("39b8c22f-5d78-446b-a046-a2967986da5c");
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
}
</style>