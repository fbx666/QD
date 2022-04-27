<template>
<!--  <div class="dashboard-container">-->
<!--    <div class="dashboard-text">name: {{ name }}</div>-->
<!--  </div>-->
    <div id='div1'style="padding:20px;">
      <h3>录音上传</h3>

      <div style="font-size:14px">
        <h3>录音时长：{{ recorder && recorder.duration.toFixed(4) }}</h3>
        <br />
        <el-button type="primary" @click="handleStart">开始录音</el-button>
        <el-button type="warning" @click="handleStop">停止录音</el-button>
        <br />
        <br />
        <h3>
          播放时长：{{
          recorder &&
          (playTime > recorder.duration
          ? recorder.duration.toFixed(4)
          : playTime.toFixed(4))
          }}
        </h3>
        <br />
        <el-button type="primary" @click="handlePlay">播放录音</el-button>
        <el-button type="warning" @click="handleStopPlay">停止播放</el-button>
        <el-button type="error" @click="handleDestroy">销毁录音</el-button>
        <el-button type="warning" @click="download">下载录音</el-button>
        <el-button type="primary" @click="uploadRecord">上传</el-button>
        <el-button type="primary" @click="getData">测试</el-button>
      </div>
      <div>

      </div>
    </div>
</template>

<script>
import { mapGetters } from 'vuex'
import axios from 'axios';
import Recorder from 'js-audio-recorder'  //报错的话 npm install --save js-audio-recorder
export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  },

  data() {
    return {
      recorder: null,
      playTime: 0,
      timer: null,
      src: null,
      ans: '识别结果',
    }
  },
  created() {
    // this.recorder = new Recorder()  # 创建录音对象
    this.recorder = new Recorder({
      // sampleBits: 16, // 采样位数，支持 8 或 16，默认是16
      sampleRate: 16000, // 采样率，支持 11025、16000、22050、24000、44100、48000，根据浏览器默认值，我的chrome是48000
      // numChannels: 1 // 声道，支持 1 或 2， 默认是1
      // compiling: false,(0.x版本中生效,1.x增加中)  // 是否边录边转换，默认是false
    })
  },
  methods: {
    // 开始录音
    handleStart() {
      this.recorder = new Recorder()
      Recorder.getPermission().then(() => {
        console.log('开始录音')
        this.recorder.start() // 开始录音
      }, (error) => {
        this.$message({
          message: '请先允许该网页使用麦克风',
          type: 'info'
        })
        console.log(`${error.name} : ${error.message}`)
      })
    },
    handleStop() {
      console.log('停止录音')
      this.recorder.stop() // 停止录音
    },
    handlePlay() {
      console.log('播放录音')
      console.log(this.recorder)
      this.recorder.play() // 播放录音

      // 播放时长
      this.timer = setInterval(() => {
        try {
          this.playTime = this.recorder.getPlayTime()
        } catch (error) {
          this.timer = null
        }
      }, 100)
    },
    handleStopPlay() {
      console.log('停止播放')
      this.recorder.stopPlay() // 停止播放

      // 播放时长
      this.playTime = this.recorder.getPlayTime()
      this.timer = null
    },
    download() {
      this.recorder.pause()  // 首先停止录音
      this.recorder.downloadWAV("下载录音.wav")
      // const blob = this.recorder.downloadWAV()// 获取wav格式音频数据
      // const newbolb = new Blob([blob], { type: 'audio/wav' })
      // const fileOfBlob = new File([newbolb], new Date().getTime() + '.wav')
      // recorder.downloadWAV(fileOfBlob);
    },
    handleDestroy() {
      console.log('销毁实例')
      this.recorder.destroy() // 毁实例
      this.timer = null
    },
    uploadRecord() {
      if (this.recorder == null || this.recorder.duration === 0) {
        this.$message({
          message: '请先录音',
          type: 'error'
        })
        return false
      }
      this.recorder.pause() // 暂停录音
      this.timer = null
      // console.log('上传录音');// 上传录音
      //
      // const formData = new FormData();
      // const blob = this.recorder.getWAVBlob();// 获取wav格式音频数据
      // // 此处获取到blob对象后需要设置fileName满足当前项目上传需求，其它项目可直接传把blob作为file塞入formData
      // const newblob = new Blob([blob], { type: 'audio/wav' });
      // const file = new File([newblob], new Date().getTime() + '.wav')
      // formData.append('file', file);
      // const url = window.URL.createObjectURL(file);
      // 对获取的文件进行保存
      // let a =document.createElement('a');
      // a.setAttribute('href',url);
      // a.setAttribute('download','test.wav');
      // a.click();

      this.src = url

      const path = 'http://127.0.0.1:5000/shibie';
      axios.get(path).then(res => {
        // 这里服务器返回response为一个json对象
        // 通过.data来访返回的数据，然后在通过.变量名进行访问
        // 可以直接通过response.data取得key-value
        var msg = res.data.msg;
        this.ans = msg
        this.serverResponse = msg; // 因为不能直接使用this作为指针，因此在这之前将this赋给了then指针
        alter('Success' + response.status + ',' + response.data + ',' + msg); // 成功后显示提示
      }).catch(error => {
        console.error(error);
      });
      // const axios = require('axios')
      // axios.post(url, formData).then(res => {
      // console.log(res.data.data[0].url)
      // })
    },
    getData() {
      this.recorder.downloadWAV("test")  //需要把文件先保存下来
      // 设置对应python的接口，这里使用的是localhost:5000
      const path = 'http://127.0.0.1:5000/shibie';
      axios.get(path).then(res => {
        // 这里服务器返回response为一个json对象
        // 通过.data来访返回的数据，然后在通过.变量名进行访问
        // 可以直接通过response.data取得key-value
        var msg = res.data.msg;
        this.ans = msg; // 因为不能直接使用this作为指针，因此在这之前将this赋给了then指针
        alter('Success' + response.status + ',' + response.data + ',' + msg); // 成功后显示提示
      }).catch(error => {
        console.error(error);
      });
    }
  }
}
  // 之后为方法实现

</script>

<style lang="scss" scoped>
#div1{
  height:600px;
  background-color: aquamarine;
  background-image: url("../../assets/pic/4.jpeg");
  background-repeat: no-repeat;
  background-size: cover;
}
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
</style>
