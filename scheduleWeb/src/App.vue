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
            <el-button class="el-button" @click="mySchedule(FIFOArr, 'FIFO')">
              FIFO
            </el-button>
            <el-button class="el-button" @click="mySchedule(SJFArr, 'SJF')"
              >SJF
            </el-button>
            <el-button class="el-button" @click="mySchedule(EDFArr, 'EDF')"
              >EDF</el-button
            >
            <el-button
              class="el-button"
              @click="mySchedule(PriorityArr, 'Priority')"
            >
              Priority
            </el-button>
          </div>
        </div>
      </el-col>
      <el-col :span="10">
        <div id="main" style="width: 600px; height: 400px"></div>
        <h3>调度过程：{{ peopleList }}</h3>
        <h3>当前接送的人：{{ currPeople }}</h3>
        <h3>接送完成：{{ finish }}</h3>
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

      SJFArr: [
        2,
        2,
        1,
        1,
        1,
        3,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        4,
        4,
        4,
        4,
        4,
        4,
        4,
        4,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
      ],
      FIFOArr: [
        1,
        1,
        1,
        1,
        1,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        3,
        3,
        3,
        3,
        3,
        5,
        5,
        5,
        5,
        5,
        4,
        4,
        4,
        4,
        4,
        -1,
        -1,
        -1,
        -1,
        -2,
        -2,
        -2,
        -2,
        -3,
        -3,
        -3,
        -3,
        -3,
        -3,
        -4,
        -4,
        -4,
        -4,
        -4,
        -5,
        -5,
        -5,
        -5,
        -5,
      ],
      PriorityArr: [
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        1,
        1,
        1,
        3,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        4,
        4,
        4,
        4,
        4,
        4,
        4,
        4,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
      ],
      EDFArr: [
        2,
        2,
        1,
        1,
        1,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        2,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        1,
        3,
        4,
        4,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        3,
        4,
        4,
        4,
        4,
        4,
        4,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        5,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
        -1,
      ],
      carCoordinate: [20, 40],
      people1: [900, 80],
      people2: [800, 200],
      people3: [200, 150],
      people4: [50, 500],
      people5: [920, 450],
      people1Destination: [500, 50],
      people2Destination: [100, 500],
      people3Destination: [800, 600],
      people4Destination: [500, 300],
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
        title: {
          text: name,
        },
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
    mySchedule: function (arr, name) {
      // let data = this.getInputData();
      let responseData = [];
      let data = {
        orders: [
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
      };
      axios.post("/fifo", data).then(function (res) {
        responseData = res;
      });

      let peopleIndex = [];
      var that = this;
      var timesRun = 0;
      var interval = setInterval(function () {
        if (arr[timesRun] > 0) {
          var currPeople = document.getElementById(
            "divPeople" + arr[timesRun] + "Id"
          );
          var currDest = document.getElementById("dest" + arr[timesRun] + "Id");
          currPeople.style.visibility = "visible";
          currDest.style.visibility = "visible";
          that.peopleList.push(arr[timesRun]);
          that.currPeople = arr[timesRun];
        } else {
          that.currPeople = arr[timesRun] * -1;
        }
        switch (arr[timesRun]) {
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
        var tmp = that.countTimeSlice(arr, timesRun);
        var times = tmp[0];
        var stepX = (peopleIndex[0] - that.carCoordinate[0]) / times;
        var stepY = (peopleIndex[1] - that.carCoordinate[1]) / times;
        var carDiv = document.getElementById("divCarId");
        that.carCoordinate[0] = that.carCoordinate[0] + stepX;
        that.carCoordinate[1] = that.carCoordinate[1] + stepY;
        carDiv.style.left = that.carCoordinate[0] + "px";
        carDiv.style.top = that.carCoordinate[1] + "px";
        if (timesRun >= arr.length) {
          var currPeople = document.getElementById(
            "divPeople" + arr[timesRun - 1] * -1 + "Id"
          );
          var currDest = document.getElementById(
            "dest" + arr[timesRun - 1] * -1 + "Id"
          );
          currPeople.style.visibility = "hidden";
          currDest.style.visibility = "hidden";
          clearInterval(interval);
        }
        if (timesRun - 1 >= 0 && arr[timesRun] != arr[timesRun - 1]) {
          if (arr[timesRun - 1] < 0) {
            var currDest = document.getElementById(
              "dest" + arr[timesRun - 1] * -1 + "Id"
            );
            currDest.style.visibility = "hidden";
            that.finish.push(arr[timesRun - 1] * -1);
          } else {
            var currPeople = document.getElementById(
              "divPeople" + arr[timesRun - 1] + "Id"
            );
            currPeople.style.visibility = "hidden";
          }
        }

        timesRun += 1;
      }, 500);

      this.drawPie("main", name, arr);
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
    },

    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
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
  height: 600px;
}

.el-people1 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 900px;
  top: 80px;
}

.el-people2 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 800px;
  top: 200px;
}

.el-people3 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 200px;
  top: 150px;
}

.el-people4 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 50px;
  top: 500px;
}

.el-people5 {
  /*background-color: antiquewhite;*/
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 920px;
  top: 450px;
}

.el-dest1 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 500px;
  top: 50px;
}

.el-dest2 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 100px;
  top: 500px;
}

.el-dest3 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 800px;
  top: 600px;
}

.el-dest4 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 500px;
  top: 300px;
}

.el-dest5 {
  width: 100px;
  height: 100px;
  visibility: hidden;
  position: absolute;
  left: 400px;
  top: 100px;
}

.el-divIllustrate {
  height: 1000px;
  /* position: absolute; */
  /*background-color: #3D9DD9;*/
}
</style>
