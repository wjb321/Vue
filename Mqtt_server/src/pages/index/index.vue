<template>
  <div class="wrapper">
    <div class="header"></div>
    <!-- header part-->
    <div class="header-wrapper">
      <div class="header-title">
        <span> Air Quality - good</span>
        <span> Freiburg </span>
      </div>
      <div class="header-text">
        <span> 25</span>
        <span> clear</span>
      </div>
      <div class="weather-advice">
        The temperature is high, please reduce the number of trips
      </div>
    </div>
    <!--father body part 1-->
    <div class="body-wrapper">
      <div class="body">
        <div class="data-wrapper">
          <!-- child body part 1-->
          <div class="data">
            <img class="data-logo" src="/static/images/motor.png" />
            <div class="data-text">
              <div class="data-text">Speed</div>
              <div class="data-value">{{ MotorSpeed }}r/m</div>
            </div>
          </div>
          <!--child body part 2-->
          <div class="data">
            <img class="data-logo" src="/static/images/led.png" />
            <div class="data-text">
              <div class="data-text">LED</div>
              <div class="data-value">
                <switch @change="onLedChange" :checked="Led" color="#3d7ef9" />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!--father body part 2 -->
    <div class="body-wrapper">
      <div class="body">
        <div class="data-wrapper">
          <!-- child body part 1-->
          <div class="data">
            <img class="data-logo" src="/static/images/temp.png" />
            <div class="data-text">
              <div class="data-text">Temp</div>
              <div class="data-value">{{ Temp }}℃</div>
            </div>
          </div>
          <!-- child body part 2-->
          <div class="data">
            <img class="data-logo" src="/static/images/beep.png" />
            <div class="data-text">
              <div class="data-text">Beep</div>
              <div class="data-value">
                <switch
                  @change="onBeepChange"
                  :checked="Beep"
                  color="#3d7ef9"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!--*  -->
    <!--father body part 3 -->
    <div class="tail-wrapper">
      <div class="body">
        <div class="data-wrapper">
          <div class="data">
            <img class="data-logo" src="/static/images/impulse1.png" />
            <!--<img class = "data-logo" src = "/static/images/Hum.png"/> -->
            <div class="data-text">
              <!--<div class = "data-text">Humidity</div>
                         <div class = "data-value">{{Hum}}%</div> 
                          <button class='btn1' @change = "onPWMChange" :checked = "SpeedAcc">test</button>-->
              <div class="data-text">PWM</div>
              <div class="data-value">
                <slider
                  name="slider"
                  @change="slider4change"
                  min="100"
                  max="2000"
                  step="20"
                  block-size="28"
                  show-value
                />
                <!-- <view style="font-size:{{fs}}rpx;">
                                 这是一段可以控制大小的文本，神奇吧？  </view>
                                -->
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="s_view">
      <div class="data">
        <button class="Cele" @click='celeration' > Cele </button>
      </div>
      <div class="data">
        <button class="Cele_plus" @click="celeration_plus" > Cele+ </button>
      </div>
      <div class="data">
        <button class="Dece" @click="deceleration" >Dece</button>
      </div>
      <div class="data">
        <button class="Dece_minus" @click="deceleration_minus">Dece-</button>
      </div>
    </div>
  </div>
</template>

<script>
//import '../css/style.scss'
//src="http://code.jquery.com/jquery-1.11.0.min.js" is for $
import { connect } from "mqtt/dist/mqtt.js"; //"mqtt/dist/mqtt.js"

const mqttUrl = "wxs://mqtt.jiabinwang.xyz:8084/mqtt"; //
var global_pwm = 0;
var change_slider = 0;
// here is the configured safe server
// the details are in npmjs.com/package/mqtt#weapp
export default {
  data() {
    return {
      // return a client
      client: {},
      Temp: 69,
      Hum: 0,
      MotorSpeed: 0,
      Beep: false,
      Led: false,
      //led0pwmval: 100
    };
  },

  components: {
    //su
  },
  methods: {
   // global_pwmVal: 0,
    celeration(e){
      var that = this;
      global_pwm =  global_pwm - 20;
      if(global_pwm< 40  || global_pwm == 40)
      {global_pwm = 40}
      //change_slider =  global_pwm + 20;
      let times = global_pwm / 256;
      const buf4 = Buffer.from([times, global_pwm]);
      const json5 = JSON.stringify(buf4);
      console.log(json5);
      var topic = "stm32_sub";
      if (global_pwm) {
        that.client.publish(topic, json5, function (err) {
          if (!err) {
            console.log("celeration： speedinfo is", global_pwm);
          }
        });
      } else {
        that.client.publish(topic, json5,function (err) {
            if (!err) {
              console.log("global_pwm != 0 ");
            }
          }
        );
      }
      console.log(e);
      console.log("speedinfo is", global_pwm);
    },
    celeration_plus :function(e){
      var that = this;
      global_pwm =  global_pwm - 40;
      if(global_pwm< 40  || global_pwm == 40)
      {global_pwm = 40}
      let times = global_pwm / 256;
      const buf4 = Buffer.from([times, global_pwm]);
      const json5 = JSON.stringify(buf4);
      console.log(json5);
      var topic = "stm32_sub";
      if (global_pwm) {
        that.client.publish(topic, json5, function (err) {
          if (!err) {
            console.log("celeration_plus speedinfo is", global_pwm);
          }
        });
      } else {
        that.client.publish(topic, json5,function (err) {
            if (!err) {
              console.log("global_pwm != 0 ");
            }
          }
        );
      }
      console.log(e);
      console.log("speedinfo is", global_pwm);
    },
   deceleration :function(e){
      var that = this;
      global_pwm =  global_pwm + 20;
      if(global_pwm> 2000 || global_pwm == 2000)
      {global_pwm =2000}
      let times = global_pwm / 256;
      const buf4 = Buffer.from([times, global_pwm]);
      const json5 = JSON.stringify(buf4);
      console.log(json5);
      var topic = "stm32_sub";
      if (global_pwm) {
        that.client.publish(topic, json5, function (err) {
          if (!err) {
            console.log("deceleration speedinfo is", global_pwm);
          }
        });
      } else {
        that.client.publish(topic, json5,function (err) {
            if (!err) {
              console.log("global_pwm != 0 ");
            }
          }
        );
      }
      console.log(e);
      console.log("speedinfo is", global_pwm);
    },
    deceleration_minus :function(e){
      var that = this;
      global_pwm =  global_pwm + 40;
      if(global_pwm> 2000  || global_pwm == 2000)
      {global_pwm =2000}
      let times = global_pwm / 256;
      const buf4 = Buffer.from([times, global_pwm]);
      const json5 = JSON.stringify(buf4);
      console.log(json5);
      var topic = "stm32_sub";
      if (global_pwm) {
        that.client.publish(topic, json5, function (err) {
          if (!err) {
            console.log("deceleration_minus speedinfo is", global_pwm);
          }
        });
      } else {
        that.client.publish(topic, json5,function (err) {
            if (!err) {
              console.log("global_pwm != 0 ");
            }
          }
        );
      }
      console.log(e);
      console.log("speedinfo is", global_pwm);
    },
    slider4change(event) {
      var that = this;
      //console.log(event.mp.detail)
      //let pwm = event.mp.detail.value; //si means speed info
       global_pwm = event.mp.detail.value;
      
      // the array has the range:255, so the value range is higher
      // than the value 255,should do another operation
      //1. how many times higher than 256, then multiply the times, this times
      //   should be sent also to by json code
      //2. directly change to string and sent
      //details for buffer function: https://www.runoob.com/nodejs/nodejs-buffer.html
      //const buf1 = Buffer.allocUnsafe(10, 8);
      //const buf1 = Buffer.alloc(1, pwm);
      //pwm = toString(pwm)
      //const buf4 = Buffer.from([pwm]); // the parameter is array
      let times = global_pwm / 256;
      const buf4 = Buffer.from([times, global_pwm]);
      const json5 = JSON.stringify(buf4);
      console.log(json5);
      //var json5 =  json5.map(o=>{return{target:o.id, value:o.name}});
      /*    that.led0pwmval = pwm
       var message=pwm; */
      var topic = "stm32_sub";
      // console.log(SpeedAcc)
      if (global_pwm) {
        that.client.publish(topic, json5, function (err) {
          if (!err) {
            // let led0pwmval = event.mp.detail.value
            //this.setData({"led0pwmval": event.mp.detail.value});
            console.log("speedinfo is", global_pwm);
            //console.log("led0pwmval is",  led0pwmval);
          }
        });
      } else {
        that.client.publish(topic, json5,function (err) {
            if (!err) {
              console.log("successfully send command --- turn off lights ");
            }
          }
        );
      }
    },
    /*      {
     var that = this
     console.log(event.detail) //.mp.detail
     let rv = event.detail.value
     console.log(this.setData({ value: value }))
     that.SpeedAcc = rv
     //console.log(sw)
     } */
    onLedChange(event) {
      var that = this;
      console.log(event.mp.detail);
      let sw = event.mp.detail.value;
      that.Led = sw;
      //console.log(sw)
      if (sw) {
        that.client.publish(
          "stm32_sub",
          '{"target":"Led","value":1}',
          function (err) {
            if (!err) {
              console.log("successfully send command --- turn on lights ");
            }
          }
        );
      } else {
        that.client.publish(
          "stm32_sub",
          '{"target":"Led","value":0}',
          function (err) {
            if (!err) {
              console.log("successfully send command --- turn off lights ");
            }
          }
        );
      }
    },
    onBeepChange(event) {
      var that = this;
      console.log(event.mp.detail);
      let sw = event.mp.detail.value;
      that.Beep = sw;
      //console.log(sw)
      if (sw) {
        that.client.publish(
          "stm32_sub",
          '{"target":"Beep","value":1}',
          function (err) {
            if (!err) {
              console.log("successfully send command --- turn on Beep ");
            }
          }
        );
      } else {
        that.client.publish(
          "stm32_sub",
          '{"target":"Beep","value":0}',
          function (err) {
            if (!err) {
              console.log("successfully send command --- turn off Beep ");
            }
          }
        );
      }
    },
  },
  onShow() {
    // 小程序的生命周期
    var that = this; // this means all the index vue component
    that.client = connect(mqttUrl);
    that.client.on("connect", function () {
      console.log("successfully connect mqtt server");
      that.client.subscribe("stm32_pub", function (err) {
        if (!err) {
          console.log("successfully subscribe device topic!");
        }
      });
    });

    that.client.on("message", function (topic, message) {
      console.log(topic);
      console.log(message); // here the topic is a data flow: uint8array(s) [104, 101, 108, 111]
      //message is 0xxx buf
      let dataFromDev = {};
      dataFromDev = JSON.parse(message);
      console.log(dataFromDev); // in this place, if send value from server with json code, the value can
      //be successfully received.
      that.Temp = dataFromDev.Temp;
      that.Hum = dataFromDev.Hum;
      that.Led = dataFromDev.Led;
      that.Beep = dataFromDev.Beep;
      that.MotorSpeed = dataFromDev.MotorSpeed;
      //that.led0pwmval = dataFromDev.led0pwmval
    });
  },
};
</script>

<style lang = "scss" scoped >
.wrapper {
  padding: 15px;
  .header-wrapper {
    background-color: rgb(27, 112, 187);
    border-radius: 20px;
    color: rgb(185, 154, 154);
    box-shadow: #d6d6d6;
    padding: 15px 30px;
    .header-title {
      display: flex;
      justify-content: space-between;
    }
  }
  .header-text {
    font-size: 32px;
    font-weight: 800px;
    display: flex;
    justify-content: space-between;
  }

  .weather-advice {
    margin-top: 20px;
    font-size: 12px;
  }

  .data-wrapper {
    margin-right: 10;
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
    .data {
      background-color: azure;
      width: 150px;
      height: 80px;
      border-radius: 20px;
      display: flex;
      justify-content: space-around;
      padding: 0 8px;
      box-shadow: #d6d6d6 0px 0px 5px;
      .data-logo {
        height: 36px;
        width: 36px;
        margin-top: 15px;
      }
      .data-text {
        margin-top: 10px;
        color: #7f7f7f;
        /*   text-align: right; */
        .data-value {
          font-size: 20px;
        }
      }
    }
  }
  .tail-wrapper {
    /* margin-top: 2px; */
    display: flex;
    justify-content: space-between;
    .data {
      /*  background-color: write;
    border-radius: 20px;
    color: rgb(231, 210, 210);
    box-shadow: #ffffff;
    padding: 15px 30px; */
      background-color: azure;
      width: 330px;
      height: 80px;
      border-radius: 20px;
      display: flex;
      justify-content: space-around;
      padding: 0 8px;
      box-shadow: #d6d6d6 0px 0px 5px;
      /*     .data-logo{
        height: 20px;
        width: 20px;
        margin-top: 5px;
      } */
      .data-text {
        text-align: center;
        margin-top: 10px;
        color: #7f7f7f;
        .data-value {
          font-size: 20px;
          width: 345px; /* change the size of slider */
        }
      }
      /*     .tail-title{
      display: flex; 
      justify-content: space-between;
         .tail-text{
          text-align: right;
        }
    } */
      /*     .tail-value{
      font-size: 32px;
    } */
    }
    /*.tail-value{
      width:345px; change the length of the slider  
      }
   .tail-text{
    margin-top: 10px;
    color: #7f7f7f;
    font-size: 20px;
    font-weight: 20px; 
    display: flex;
    justify-content: space-between;
    } */
  }

 .s_view {
    margin-top: 15px;
    margin-right: 10;
   /*  margin-top: 20px; */
    display: flex;
    justify-content: space-between;
    top: 10rpx;
/*     bottom: 0rpx;
     
    position: center;
    width: 100%; */
  .data{
      background-color: azure;
      color: #7f7f7f;
      width: 70px;
      height:70px;
      border-radius: 20px;
      display: flex;
      justify-content: space-around;
      padding:8px;
      box-shadow: #d6d6d6 0px 0px 5px; 
 .Dece_minus {
/*      background-color: azure;
     color:   #7f7f7f; */
    /* width: 25% !important; */
    /*  float:right; */
  }  
  .Dece {
  /*    background-color: azure;
     color:   #7f7f7f; */
    /* width: 25% !important; */
    /* float:left; */
    font-weight: 100;
  }

  .Cece {
   /*   background-color: azure;
     color:   #7f7f7f; */
    /* width: 25% !important; */
    /*  float:left; */
    font-weight: 100;
  }
  .Cece_plus {
    /*  background-color: azure;
     color:   #7f7f7f; */
   /*  width: 2% !important; */
    /*  float:right; */
   }
  }
  
    
  }
}
</style>

     