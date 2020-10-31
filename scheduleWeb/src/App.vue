<template>
  <div id="app">
    <div class="el-div">
      <img src="./assets/car.png" id="divCarId" class="el-div-car" />
      <img src="./assets/people1.png" class="el-people1" id="divPeople1Id" />
      <img src="./assets/people2.png" class="el-people2" id="divPeople2Id" />
      <img src="./assets/people3.png" class="el-people3" id="divPeople3Id" />
      <img src="./assets/people4.png" class="el-people4" id="divPeople4Id" />
      <img src="./assets/people5.png" class="el-people5" id="divPeople5Id" />
      <img src="./assets/dest1.png" class="el-dest1" id="dest1Id" />
      <img src="./assets/dest2.png" class="el-dest2" id="dest2Id" />
      <img src="./assets/dest3.png" class="el-dest3" id="dest3Id" />
      <img src="./assets/dest4.png" class="el-dest4" id="dest4Id" />
      <img src="./assets/dest5.png" class="el-dest5" id="dest5Id" />
    </div>
    <el-row>
      <el-col :span="14">
        <div class="el-divIllustrate">
          <el-table :data="tableData" size="small">
            <el-table-column label="乘客ID">
              <template slot-scope="scope">
                <span style="margin-left: 10px">乘客{{ scope.row.id }}</span>
              </template>
            </el-table-column>
            <el-table-column label="订单到达时间">
              <template slot-scope="scope">
                <el-input v-model="scope.row.arriveTime"></el-input>
              </template>
            </el-table-column>
            <el-table-column label="接乘客所需时间">
              <template slot-scope="scope">
                <el-input v-model="scope.row.executionTime1"></el-input>
              </template>
            </el-table-column>
            <el-table-column label="送乘客所需时间">
              <template slot-scope="scope">
                <el-input v-model="scope.row.executionTime2"></el-input>
              </template>
            </el-table-column>
            <el-table-column label="订单截止时间">
              <template slot-scope="scope">
                <el-input v-model="scope.row.deadline"></el-input>
              </template>
            </el-table-column>
            <el-table-column label="乘客优先级">
              <template slot-scope="scope">
                <el-input v-model="scope.row.priority"></el-input>
              </template>
            </el-table-column>
          </el-table>
          <div style="marginTop: 20px">
            <el-button class="el-button" @click="mySchedule('fifo')"
              >FIFO</el-button
            >
            <el-button class="el-button" @click="mySchedule('sjf')"
              >SJF</el-button
            >
            <el-button class="el-button" @click="mySchedule('edf')"
              >EDF</el-button
            >
            <el-button
              class="el-button"
              @click="mySchedule('priority')"
            >
              Priority
            </el-button>
          </div>
        </div>
      </el-col>
      <el-col :span="10">
        <div id="main" style="width: 550px; height: 250px"></div>
        <h5>调度过程：{{ peopleList }}</h5>
        <h5>当前接送的人：{{ currPeople }}</h5>
        <h5>接送完成：{{ finish }}</h5>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import echarts from "echarts";
const axios = require("axios");

export default {
  created() {},
  name: "App",
  data() {
    return {
      tableData: [
        {
          id: 1,
          arriveTime: 0,
          executionTime1: 3,
          executionTime2: 10,
          deadline: 30,
          priority: 1,
        },
        {
          id: 2,
          arriveTime: 0,
          executionTime1: 6,
          executionTime2: 8,
          deadline: 20,
          priority: 2,
        },
        {
          id: 3,
          arriveTime: 5,
          executionTime1: 1,
          executionTime2: 9,
          deadline: 40,
          priority: 1,
        },
        {
          id: 4,
          arriveTime: 10,
          executionTime1: 2,
          executionTime2: 6,
          deadline: 45,
          priority: 3,
        },
        {
          id: 5,
          arriveTime: 11,
          executionTime1: 5,
          executionTime2: 5,
          deadline: 60,
          priority: 1,
        },
      ],
      carCoordinate: [20, 40],
      people1: [600, 80],
      people2: [700, 200],
      people3: [1000, 150],
      people4: [50, 250],
      people5: [920, 300],
      people1Destination: [500, 100],
      people2Destination: [100, 200],
      people3Destination: [800, 150],
      people4Destination: [1100, 90],
      people5Destination: [400, 100],
      peopleList: [],
      currPeople: 1,
      finish: [],
    };
  },
  methods: {
    getInputData: function () {
      let completeData = true;
      let orders = [];
      for (let i = 1; i <= 5; i++) {
        let tmpArrive = document.getElementById("people" + i + "Arrive").value;
        let tmpAccess = document.getElementById("people" + i + "Access").value;
        let tmpSend = document.getElementById("people" + i + "Send").value;
        let tmpPri = document.getElementById("people" + i + "Pri").value;
        let tmpDDL = document.getElementById("people" + i + "DDL").value;
        if (tmpAccess == "" || tmpArrive == "" || tmpSend == "") {
          alert("请输入完整数据");
          completeData = false;
          break;
        } else {
          let tmpPeople = [];
          tmpPeople.push(
            { id: i },
            { arriveTime: tmpArrive },
            { executionTime1: tmpAccess },
            { executionTime2: tmpSend },
            { deadline: tmpDDL },
            { priority: tmpPri }
          );
          orders.push(tmpPeople);
        }
      }
      return orders;
    },
    drawPie: function (id, name, arr) {
      this.charts = echarts.init(document.getElementById(id));
      let xAxis = [];
      for (let i = 1; i <= arr.length; i++) {
        xAxis.push(i);
      }
      this.charts.setOption({
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true,
        },
        xAxis: {
          type: "category",
          data: xAxis,
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            name: "Step Start",
            type: "line",
            step: "start",
            data: arr,
          },
        ],
      });
    },
    setOrderVisible: function (timesRun) {
      for(let i = 0; i < this.tableData.length; i++){
        if(this.tableData[i].arriveTime == timesRun){
            var currPeople = document.getElementById(
              "divPeople" + (i+1) + "Id"
            );
            var currDest = document.getElementById(
              "dest" + (i+1) + "Id"
            );
            currPeople.style.visibility = "visible";
            currDest.style.visibility = "visible";
        }
      }
    },
    mySchedule: function (name) {
      let responseData = [];

      var that = this;
      axios.post("/"+name, {"orders": this.tableData}).then(function (res) {
        that.finish = []
        that.peopleList = []
        res = res.data

        let peopleIndex = [];
        var timesRun = 0;
        var interval = setInterval(function () {
          that.setOrderVisible(timesRun);
          if (res[timesRun] > 0) {
            that.peopleList.push(res[timesRun]);
            that.currPeople = res[timesRun];
          } else {
            that.peopleList.push(res[timesRun] * -1);
            that.currPeople = res[timesRun] * -1;
          }
          switch (res[timesRun]) {
            case 1:
              peopleIndex = that.people1;
              break;
            case 2:
              peopleIndex = that.people2;
              break;
            case 3:
              peopleIndex = that.people3;
              break;
            case 4:
              peopleIndex = that.people4;
              break;
            case -1:
              peopleIndex = that.people1Destination;
              break;
            case -2:
              peopleIndex = that.people2Destination;
              break;
            case -3:
              peopleIndex = that.people3Destination;
              break;
            case -4:
              peopleIndex = that.people4Destination;
              break;
            case -5:
              peopleIndex = that.people5Destination;
              break;
            default:
              peopleIndex = that.people5;
              break;
          }
          var tmp = that.countTimeSlice(res, timesRun);
          var times = tmp[0];
          var stepX = (peopleIndex[0] - that.carCoordinate[0]) / times;
          var stepY = (peopleIndex[1] - that.carCoordinate[1]) / times;
          var carDiv = document.getElementById("divCarId");
          that.carCoordinate[0] = that.carCoordinate[0] + stepX;
          that.carCoordinate[1] = that.carCoordinate[1] + stepY;
          carDiv.style.left = that.carCoordinate[0] + "px";
          carDiv.style.top = that.carCoordinate[1] + "px";
          if (timesRun >= res.length) {
            var currPeople = document.getElementById(
              "divPeople" + res[timesRun - 1] * -1 + "Id"
            );
            var currDest = document.getElementById(
              "dest" + res[timesRun - 1] * -1 + "Id"
            );
            currPeople.style.visibility = "hidden";
            currDest.style.visibility = "hidden";
            clearInterval(interval);
          }
          if (timesRun - 1 >= 0 && res[timesRun] != res[timesRun - 1]) {
            if (res[timesRun - 1] < 0) {
              var currDest = document.getElementById(
                "dest" + res[timesRun - 1] * -1 + "Id"
              );
              currDest.style.visibility = "hidden";
              that.finish.push(res[timesRun - 1] * -1);
            } else {
              var currPeople = document.getElementById(
                "divPeople" + res[timesRun - 1] + "Id"
              );
              currPeople.style.visibility = "hidden";
            }
          }

          timesRun += 1;
        }, 500);

        that.drawPie("main", name, res);
      });
    },
    countTimeSlice: function (arr, index) {
      var curr = arr[index];
      var count = 1;
      var i = index + 1;
      for (; i < arr.length; i++) {
        if (curr == arr[i]) {
          count++;
        } else {
          break;
        }
      }
      return [count, i];
    },
    sleep: function sleep(time) {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve();
        }, time);
      });
    }
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.el-div-car {
  /*background-color: #42b983;*/
  width: 100px;
  height: 100px;
  position: absolute;
  left: 20px;
  top: 40px;
}

/* 更改element-UI按钮样式 */
.el-div {
  /*background-color: aquamarine;*/
  /* width: 1590px; */
  height: 330px;
}

.el-people1 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 600px;
  top: 80px;
}

.el-people2 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 700px;
  top: 200px;
}

.el-people3 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 1000px;
  top: 150px;
}

.el-people4 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 50px;
  top: 250px;
}

.el-people5 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 920px;
  top: 300px;
}

.el-dest1 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 500px;
  top: 100px;
}

.el-dest2 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 100px;
  top: 200px;
}

.el-dest3 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 800px;
  top: 150px;
}

.el-dest4 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 1100px;
  top: 90px;
}

.el-dest5 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 400px;
  top: 100px;
}
</style>
