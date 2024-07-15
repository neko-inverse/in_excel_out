<script setup lang="ts">
// import Downloader from "./components/Downloader.vue";
import { ref } from "vue";
import Modal from './components/Modal.vue'

const isModalVisible = ref(false)

const openModal = () => {
  isModalVisible.value = true
}

const closeModal = () => {
  isModalVisible.value = false
}

//const globalData = reactive({ value: "http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4" });
// const setGlobalData = (obj: any) => {
//   globalData.value = obj.value;
// }

// const url = ref("");
// const actionflow_id = ref("39b8c22f-5d78-446b-a046-a2967986da5c");
</script>

<template>
  <div id="app">
    <button @click="openModal">打开弹窗</button>
    <Modal 
      :isVisible="isModalVisible"
      @close="closeModal"
    ></Modal>
    <!-- <Downloader source="http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4"
      vid="a175d6b01b4271ee80980764b3ec0102" playauth="" :url="url" :actionflow_id="actionflow_id"
      :global-data="globalData" :set-global-data="setGlobalData" :course_log_pk="0" /> -->
  </div>
  
</template>

<style>
#app {
  height: 60vh;
}
</style>
