<template>
  <div id="app-base-socket-ipc">
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
        4. 多窗口通信：子窗口与主进程通信，子窗口互相通信
      </span>
    </div>  
    <div class="one-block-2">
      <a-space>
        <a-button @click="createWindow(0)">打开新窗口2</a-button>
        <a-button @click="sendTosubWindow()">向新窗口2发消息</a-button>
      </a-space>
    </div>      
  </div>
</template>
<script>
import { ipcApiRoute, specialIpcRoute } from '@/utils/ipcMainApi';
import { ipc,cptext } from '@/utils/ipcRenderer';
import { toRaw } from 'vue';

export default {
  data() {
    return {
      messageString: '',
      message1: '',
      message2: '',
      message3: '',
      windowName: 'window-ipc',
      newWcId: 0,
      newsubwinContentsId:0,
      views: [
        {
          type: 'vue',
          content: '#/special/subwindow',
          windowName: 'window-ipc',
          windowTitle: 'ipc window'
        },    
      ],
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

      // 监听 窗口2 发来的消息
      ipc.removeAllListeners(specialIpcRoute.window2ToWindow1);
      ipc.on(specialIpcRoute.window2ToWindow1, (event, arg) => {
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
    handleInvoke() {
      ipc.invoke(ipcApiRoute.ipcInvokeMsg, '异步-回调OK').then(r => {
        console.log('r:', r);
        this.message1 = r;
      });
    },
    async handleInvoke2() {
      const msg = await ipc.invoke(ipcApiRoute.ipcInvokeMsg, '异步');
      console.log('msg:', msg);
      this.message2 = msg;
    },
    handleSendSync() {
      const msg = ipc.sendSync(ipcApiRoute.ipcSendSyncMsg, '同步');
      this.message3 = msg;
    },
    createWindow(index) {
      ipc.invoke(ipcApiRoute.createWindow, toRaw(this.views[index])).then(id => {
        this.newsubwinContentsId=id
        console.log('[createWindow] id:', id);
      })
      ipc.send('frommainwin', '主动发送我是渲染进程的test2');
      ipc.on('sendtosubwin', (event, value) => {
            console.log("接收主程序通讯：",value); // prints "我是主进程的test1"
            this.$message.info("接收主程序通讯："+value);
            event.sender.send("frommainwin", '收到主程序-回复主程序消息');
        })
    },
    async sendTosubWindow() {
      // 新窗口id  this.newsubwinContentsId
      ipc.sendTo( this.newsubwinContentsId, specialIpcRoute.window1ToWindow2, '窗口1通过 sendTo 给窗口2发送消息');
    },
  }
}
</script>
<style lang="less" scoped>
#app-base-socket-ipc {
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
