<template>
  <div id="app-base-powermonitor">
    <div class="one-block-1">
      <span>
        1. 监控电源状态
      </span>
    </div>  
    <div class="one-block-2">
      <a-space>
        <p>* 当前状态：{{ currentStatus }}</p>
      </a-space>
      <p>* 拔掉电源，使用电池供电</p>
      <p>* 接入电源</p>
      <p>* 锁屏</p>
      <p>* 解锁</p>
    </div>
  </div>
</template>
<script>
import { ipcApiRoute } from '@/utils/ipcMainApi';
import { ipc } from '@/utils/ipcRenderer';

export default {
  data() {
    return {
      currentStatus: '无',
    };
  },
  mounted () {
    this.init();
  },
  methods: {
    init () {
      ipc.removeAllListeners(ipcApiRoute.initPowerMonitor);
      ipc.on(ipcApiRoute.initPowerMonitor, (event, result) => {
        console.log("电源：",result)
        if (Object.prototype.toString.call(result) == '[object Object]') {
          console.log("电源2：",result)
          this.currentStatus = result.msg;
          this.$message.info(result.msg);
        }
      })
      ipc.send(ipcApiRoute.initPowerMonitor, '');
    }
  }
};
</script>
<style lang="less" scoped>
#app-base-powermonitor {
  padding: 0px 10px;
  text-align: left;
  width: 100%;
  .one-block-1 {
    font-size: 16px;
    padding-top: 10px;
  }
  .one-block-2 {
    padding-top: 10px;
  }
}
</style>
