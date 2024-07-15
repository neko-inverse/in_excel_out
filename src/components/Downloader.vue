<template>
  <div style="height: 100%;display: flex;justify-content: center;align-items: center;cursor: pointer;" :style="style"
    @click="downloadExcel()">
    {{ show_text || "下载excel模板" }}
  </div>
</template>
<script>
import writeXlsxFile from 'write-excel-file'
import zionMdapi from "zion-mdapi";
export default {
  name: "Downloader",
  props: {
    //全局参数
    globaldata: {
      type: [Object, String],
      default: ""
    },
    // 导出配置
    writeXlsxFileConfig: {
      type: [Object, String],
      default: '{"objects":[{"content":""}],"schema":[{"column":"积分数量","type":"String","value":"item => item.content"},{"column":"userID","type":"String","value":"item => item.content"},{"column":"赠送描述","type":"String","value":"item => item.content"}]}'
    },
    file_name: { type: String, default: "excel.xlsx" },
    // 导出按钮配置
    style: { type: String, default: "" }, // 样式
    show_text: { type: String, default: "导出excel" },
    // 导出行为配置
    callback_url: { type: String, default: "" },
    actionflow_name: { type: String, default: "" },
    actionflow_payload: { type: [Object, String], default: () => { return {} } },
  },
  data() {
    return {
      mdapi: null,
    }
  },
  methods: {
    async downloadExcel() {
      //1.获取导出行为配置
      const excelData = this.actionflow_name ? await this.queryDownloadTaskInfo() :
        (typeof this.writeXlsxFileConfig === 'string' && this.writeXlsxFileConfig ? JSON.parse(this.writeXlsxFileConfig) : this.writeXlsxFileConfig);


      console.log(excelData);
      if (typeof excelData.objects === 'string') excelData[`objects`] = JSON.parse(excelData.objects);
      if (typeof excelData.schema === 'string') excelData[`schema`] = JSON.parse(excelData.schema);
      excelData.schema = excelData.schema.map(res => {
        return {
          column: res.column,
          type: typeof res.type === 'string' ? eval(res.type) : res.type,
          value: typeof res.value === 'string' ? eval(res.value) : res.value,
        }
      })
      console.log(excelData);
      //2.导出
      const data = await writeXlsxFile(excelData.objects, {
        schema: excelData.schema,
        fileName: this.file_name
      });
      console.log(excelData, data);
    },
    async queryDownloadTaskInfo() {
      // 返回结构 {schema:[],objects:[]}
      return await this.mdapi.callActionflow({
        actionflow_name: this.actionflow_name,
        payload: typeof this.actionflow_payload === 'string' ? JSON.parse(this.actionflow_payload) : this.actionflow_payload
      })
    },
    async actionFlow() {
      return await mdapi.callActionflow({
        "actionflow_dir": "/后台管理/个人中心管理/",
        "actionflow_name": "个人中心管理_批量发放积分",
        "payload": {
          "data": [{
            "amount": 0, //赠送的积分数量
            "aim_user_pk": 0, //赠送userID
            "aim_describe": "" //赠送描述
          }]
        }
      })
    }
  },
  mounted() {
    console.log("props:", this.$props);
    //zionMdapi框架初始化
    this.mdapi = zionMdapi.init({
      callback_url: this.callback_url,
      env: "H5"
    })
    console.log(this.mdapi);
  },
}
</script>