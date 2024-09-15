<template>
    <div>
        <form id="myForm" method="post" target="form">

            <label for="serverScriptUrl">本专业选课界面URL：</label>
            <input type="text" id="serverScriptUrl" name="serverScriptUrl" required><br>
            <input type="hidden" name="__EVENTTARGET" value="Button1">
            <input type="hidden" name="__EVENTARGUMENT" value="">
            <input type="hidden" name="__VIEWSTATEGENERATOR" value="AC27D4D4">
            <label>是否订购教材：</label>
            <div class="radio-container">
                <label class="custom-radio">
                    <input type="radio" name="RadioButtonList1" value="1" checked>
                    <span class="radio-dot"></span> 是
                </label>
                <label class="custom-radio">
                    <input type="radio" name="RadioButtonList1" value="0">
                    <span class="radio-dot"></span> 否
                </label>
            </div>
            <label for="xkkh">选课代码(可以参考全校课表)：</label>
            <input type="text" name="xkkh" id="xkkh"  required><br>
            <button type="button" onclick="setFormAction()">提交</button>
            <label for="interval" style="margin-top: 10px; display: block;">自动抢课间隔（秒）：</label>
            <input type="number" id="interval" name="interval" min="1" value="1" style="padding: 8px; width: calc(100% - 20px); box-sizing: border-box; margin-bottom: 15px;">
            <button type="button" onclick="toggleAutoSubmit()" style="background-color: #4CAF50; color: white; border: none; border-radius: 4px; cursor: pointer; padding: 8px; width: calc(100% - 20px); box-sizing: border-box;">开始抢课/暂停</button>
            <div class="external-link">
                <p>参考链接：</p>
                <a href="https://www.swufe.edu.cn" target="_blank">使用教程</a>
                <a href="https://jwc.swufe.edu.cn/__local/0/23/7D/F5E900A5D7E5F21ACE60F03ED7E_FBE4EB5A_3EB81.xlsx" target="_blank">全校课表</a>
            </div>
        
        </form>
        
        <iframe name="form" id="form" style="display:none;"></iframe>
    </div>
</template>
<script>

export default {

   data() {
      return {


      }
   },
   created(){

   },
   computed:{

   },
   methods:{
 

   },
}
</script>
<style>

body {

    font-family: Arial, sans-serif;

    padding: 0;

    background-color: #f7f7f7;

    margin-top: 80px;

}

form {

    max-width: 400px;

    margin: 0 auto;

    background-color: #fff;

    padding: 20px;

    border-radius: 8px;

    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);

}

label {

    display: block;

    margin-bottom: 8px;

}

input[type="text"],

input[type="radio"],

button {

    margin-bottom: 15px;

    padding: 8px;

    width: calc(100% - 20px);

    box-sizing: border-box;

}

button {

    background-color: #4CAF50;

    color: white;

    border: none;

    border-radius: 4px;

    cursor: pointer;

}

button:hover {

    background-color: #45a049;

}

.radio-container {

    display: flex;

    align-items: center;

    margin-bottom: 15px;

}

.radio-container input[type="radio"] {

    display: none;

}

.custom-radio {

    position: relative;

    cursor: pointer;

    margin-right: 10px;

}

.custom-radio .radio-dot {

    height: 20px;

    width: 20px;

    background-color: #ddd;

    border-radius: 50%;

    display: inline-block;

    position: relative;

}

.custom-radio input[type="radio"]:checked ~ .radio-dot {

    background-color: #4CAF50;

}

.custom-radio .radio-dot::before {

    content: "";

    position: absolute;

    display: none;

    top: 6px;

    left: 6px;

    width: 8px;

    height: 8px;

    border-radius: 50%;

    background: white;

}

.custom-radio input[type="radio"]:checked ~ .radio-dot::before {

    display: block;

}

.custom-radio label {

    color: #333;

    cursor: pointer;

}

.external-link {

    display: block;

    margin-top: 20px;

    text-align: center;

}

.external-link a {

    color: blue;

    text-decoration: underline;

}

</style>
