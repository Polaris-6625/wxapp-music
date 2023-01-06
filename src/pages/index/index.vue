<template>
  <view class="main">
    <h1>音乐列表</h1>
    <h2>
      这是一款完全免费的音乐播放器
    </h2>
    <view>
      <view>
        <h4>音乐列表</h4>
        <p v-for="it in menuList" @click="sendIdTo(it.name)">
          {{it.id}}-{{it.name}}----{{it.author}}
        </p>
      </view>
      <!-- <view class="page_index">
        <nut-pagination  v-model="currentPage2" :total-items="125" :show-page-size="3"  @change="changePaging"  force-ellipses/>
      </view> -->
      <view class="center">
        <ul class="pagination">
          <li>«</li>
          <li v-for="it in menuPage" @click="changePaging(it)">
            {{it}}
          </li>
          <li>»</li>
        </ul>
      </view>
    </view>
  </view>
</template>

<script setup>
    import { ref , reactive ,  getCurrentInstance , onBeforeMount , ComponentInternalInstance , Ref } from "vue";
    import Taro from '@tarojs/taro';
    const data = reactive({
        muted: false,
        autoplay: false
      });
    const innerAudioContext = Taro.createInnerAudioContext();
    let menuPage = ref([1,2,3,4,5,6]);
    let currentPage2 = ref(1);
    // interface MenuList {
    //   id:number | null;
    //   name:string | undefined;
    //   author:string
    // }
    let menuList = ref([{
        id:null,
        name:'',
        author:''
    }]);
    let playOrnot = ref(false);
    // 
    function alertTitle(){
        alert("点击列表中的歌曲名字开始播放");
    }
    let bURL = "http://127.0.0.1:8083";
    let realURL = "http://polaris.lyyfsq.club:8083";
    function pageChange(value) {
      console.log(value);
    }
    const playing = ref(false);
  
    const fastBack = () => {
        console.log('倒退');
  };
  
      const forward = (progress) => {
        console.log('快进', '当前时间' + progress);
      };
  
      const changeStatus = (status) => {
        console.log('当前播放状态', status);
        playing.value = status;
        data.autoplay = false;
      };
  
      const ended = () => {
        console.log('播放结束');
      };
  
      const changeProgress = (val) => {
        console.log('改变进度条', val);
      };
    // 
    function changePaging(value) {
      // proxy.defaults.withCredentials = true;
      let val_flag = value.toString();
      // let param = new URLSearchParams();
      // param.append("val_flag",val_flag);
      //调用方法
      Taro.request({
          url:"http://43.138.51.229:8083/getPaging",
          method:"POST",
          header:{
            'content-type':'multipart/form-data; boundary=XXX'
          },
          data:'\r\n--XXX' +
            '\r\nContent-Disposition: form-data; name="val_flag"' +
            '\r\n' +
            '\r\n'+val_flag+'\r\n--XXX--',
          success:function(resp) {
            //console.log(resp.data);
            let c = resp.data;
            menuList.value = c;
            console.log(menuList);
          }
        })
    }
    onBeforeMount(() => {
      // proxy.defaults.withCredentials = true;
      // let param = new URLSearchParams();
      // param.append("val_flag","1");
      Taro.request({
          url:"http://polaris.lyyfsq.club:8083/getPaging",
          method:"POST",
          header:{
            'content-type':'multipart/form-data; boundary=XXX'
          },
          data:'\r\n--XXX' +
            '\r\nContent-Disposition: form-data; name="val_flag"' +
            '\r\n' +
            '\r\n'+"1"+'\r\n--XXX--',
          success:function(resp) {
            //console.log(resp.data);
            let c = resp.data;
            menuList.value = c;
            console.log(menuList);
          }
        })
      //调用方法
      // proxy.$http
      //   .post(realURL+"/getPaging",param)
      //   .then(function(res) {
      //     console.log(res.data);
      //     menuList.value = res.data;
      //     console.log(menuList);
      //   })
      //   .catch(function(error) {
      //     console.log(error);
      //   });
    });
    let urlSend = ref("");
    let audioRef = ref(null)
    const sendIdTo = (n) => {
      innerAudioContext.autoplay = true
      innerAudioContext.src = "http://43.138.51.229:8083/music?musicName="+n;
      innerAudioContext.onPause(()=>{
        console.log("1");
      })
      innerAudioContext.onPlay(() => {
        console.log('开始播放')
      })
      innerAudioContext.onError((res) => {
        console.log(res.errMsg)
        console.log(res.errCode)
    })
  }
    
  </script>

<style>
.main {
  display: flex;
  justify-content: center;
  flex-direction: column;
  height: 100%;
  width: 100%;
  text-align: center;
}
.page_index {
  text-align: center;
  display: flex;
  justify-content: center;
}
* {
  font-family: cursive;
  margin-top: 30%;
}

ul.pagination {
  display: inline-block;
  padding: 0;
  margin: 0;
}

ul.pagination li {display: inline;}

ul.pagination li  {
  color: black;
  float: left;
  padding: 8px 16px;
  text-decoration: none;
  transition: background-color .3s;
  border: 1px solid #ddd;
}

ul.pagination li:active  {
  background-color: #4CAF50;
  color: white;
  border: 1px solid #4CAF50;
}

ul.pagination li:hover  {
  background-color: #4CAF50;
  color: white;
  border: 1px solid #4CAF50;
}

ul.pagination li a:hover:not(.active) {background-color: #ddd;}

div.center {text-align: center;}
</style>