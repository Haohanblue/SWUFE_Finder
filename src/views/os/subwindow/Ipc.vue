<template>
  <div id="app-base-subwindow-ipc">
   <div class="sunwinbox">
      <div class="one-block-1">
        <span>
          1. 发送异步消息
        </span>
      </div>  
      <div class="one-block-2">
        <a-space>
          <a-button @click="handleInvoke">发送 - 回调</a-button>
          结果：{{ message1 }}
        </a-space>
        <p></p>
        <a-space>
          <a-button @click="handleInvoke2">发送 - async/await</a-button>
          结果：{{ message2 }}
        </a-space>            
      </div>   
      <div class="one-block-1">
        <span>
          <!-- 尽量不要使用，任何错误都容易引起卡死 -->
          2. 同步消息（不推荐，阻塞执行）
        </span>
      </div>  
      <div class="one-block-2">
        <a-space>
          <a-button @click="handleSendSync">同步消息</a-button>
          结果：{{ message3 }}
        </a-space>   
      </div>        
      <div class="one-block-1">
        <span>
          3. 长消息： 服务端持续向前端页面发消息
        </span>
      </div>  
      <div class="one-block-2">
        <a-space>
          <a-button @click="sendMsgStart">开始</a-button>
          <a-button @click="sendMsgStop">结束</a-button>
          结果：{{ messageString }}
        </a-space>
      </div>
      <div class="one-block-1">
        <span>
          4. 多窗口通信：窗口之间互相通信
        </span>
      </div>  
      <div class="one-block-2">
        <a-space>
          <a-button type="primary" @click="sendToMainWindow()">向主窗口发消息</a-button>
        </a-space>
      </div>  
   </div>     
  </div>
</template>
<script>
import { ipcApiRoute, specialIpcRoute } from '@/utils/ipcMainApi';
import { ipc } from '@/utils/ipcRenderer';

export default {
  data() {
    return {
      messageString: '',
      message1: '',
      message2: '',
      message3: '',
      mainWCid: 0,
    }
  },
  mounted () {
    this.init();
  },
  methods: {
    init () {
      // 避免重复监听，或者将 on 功能写到一个统一的地方，只加载一次
      ipc.removeAllListeners(ipcApiRoute.ipcSendMsg);
      ipc.on(ipcApiRoute.ipcSendMsg, (event, result) => {
        console.log('[ipcRenderer] [socketMsgStart] result:', result);

        this.messageString = result;
        // 调用后端的另一个接口
        event.sender.send(ipcApiRoute.hello, 'electron-egg');
      })

      // 监听主窗口发来的消息
      ipc.removeAllListeners(specialIpcRoute.window1ToWindow2);
      ipc.on(specialIpcRoute.window1ToWindow2, (event, arg) => {
          this.$message.info(arg);
      })
    },
    sendMsgStart() {
      const params = {
        type: 'start',
        content: '开始'
      }
      ipc.send(ipcApiRoute.ipcSendMsg, params)
    },
    sendMsgStop() {
      const params = {
        type: 'end',
        content: ''
      }
      ipc.send(ipcApiRoute.ipcSendMsg, params)
    },
    handleInvoke () {
      ipc.invoke(ipcApiRoute.ipcInvokeMsg, '异步-回调').then(r => {
        console.log('r:', r);
        this.message1 = r;
      });
    },
    async handleInvoke2 () {
      const msg = await ipc.invoke(ipcApiRoute.ipcInvokeMsg, '异步');
      console.log('msg:', msg);
      this.message2 = msg;
    },
    handleSendSync () {
      const msg = ipc.sendSync(ipcApiRoute.ipcSendSyncMsg, '同步');
      this.message3 = msg;
    },
    sendToMainWindow () {
      // 获取主窗口id=1
      ipc.invoke(ipcApiRoute.getWCid, 'main').then(id => {
        this.mainWCid = 1;
        console.log("获取主窗口id",id)
        ipc.sendTo(this.mainWCid, specialIpcRoute.window2ToWindow1, '窗口2 通过 sendTo 给主窗口发送消息');
      });
    },
  }
}
</script>
<style lang="less" scoped>
#app-base-subwindow-ipc {
  text-align: left;
  width: 100%;
  height: 100%;
  background-color: #f0f2f5;
  .sunwinbox{
    padding: 20px;
  }
  .one-block-1 {
    font-size: 16px;
    padding-top: 10px;
  }
  .one-block-2 {
    padding-top: 10px;
  }
}
</style>
