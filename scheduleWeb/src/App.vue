<template>
  <div id="app" class="el-div">
    <div class="el-div-car" id="divCarId">
      <img src="./assets/car.png" class="el-car">
    </div>
    <div class="el-people1" id="divPeople1Id">
      <img src="./assets/people1.png" class="el-people1">
    </div>
    <div class="el-people2" id="divPeople2Id">
      <img src="./assets/people2.png" class="el-people2">
    </div>
    <div class="el-people3" id="divPeople3Id">
      <img src="./assets/people3.png" class="el-people3">
    </div>
    <div class="el-people4" id="divPeople4Id">
      <img src="./assets/people4.png" class="el-people4">
    </div>
    <div class="el-people5" id="divPeople5Id">
      <img src="./assets/people5.png" class="el-people5">
    </div>

    <h1>{{carCoordinate}}</h1>
    <button class="el-button" @click="this.FIFO">FIFO</button>
    <button class="el-button" @click="this.SJF">SJF</button>
    <button class="el-button" @click="this.EDF">EDF</button>
    <button class="el-button" @click="this.Priority">Priority</button>
    <div id="main" style="width: 600px;height: 400px;"></div>
  </div>
</template>

<script>
    import echarts from 'echarts'

    export default {
        created() {
        },
        name: 'App',
        data() {
            return {
                SJFArr: [2, 2, 1, 1, 1, 3, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                FIFOArr: [1, 1, 1, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 3, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                PriorityArr: [2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 1, 1, 1, 3, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 4, 4, 4, 4, 4, 4, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                EDFArr: [2, 2, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 3, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                carCoordinate: [20, 10],
                peopleCoordinate: [200, 200],
            }
        },
        methods: {
            drawPie(id, name, arr) {
                this.charts = echarts.init(document.getElementById(id));
                let xAxis = [];
                for (let i = 1; i <= arr.length; i++) {
                    xAxis.push(i);
                }
                this.charts.setOption({
                    title: {
                        text: name
                    },
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: {
                        type: 'category',
                        data: xAxis
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [
                        {
                            name: 'Step Start',
                            type: 'line',
                            step: 'start',
                            data: arr
                        }
                    ]
                })
            },

            FIFO: function () {
                var index = 0;
                while (index < this.FIFOArr.length){
                    var tmp = this.countTimeSlice(this.FIFOArr,index);
                    var times = tmp[0];
                    index = tmp[1];
                    var stepX = 1;
                    var stepY = 1;
                    for (var i = 0; i < times ; i++) {

                        this.timer(stepX,stepY);
                    }

                }
                this.drawPie('main','FIFO', this.FIFOArr);

            },
            EDF: function () {
                this.drawPie('main','EDF',this.EDFArr);
            },
            Priority: function () {
                this.drawPie('main','Priority',this.PriorityArr);
            },
            SJF: function () {
                this.drawPie('main','SJF',this.SJFArr);
            },
            countTimeSlice(arr,index){
                var curr = arr[index];
                var count = 1;
                var i = index + 1;
                for (; i < arr.length ; i++) {
                    if (curr == arr[i]){
                        count++;
                    }else {
                        break;
                    }
                }
                return [count,i];
            },
            carMove: function (stepx,stepy) {
                var carDiv = document.getElementById('divCarId');
                this.carCoordinate[0] = (this.carCoordinate[0] + stepx) % 1000;
                this.carCoordinate[1] = (this.carCoordinate[1] + stepy) % 500;
                carDiv.style.left = this.carCoordinate[0] + 'px';
                carDiv.style.top = this.carCoordinate[1] + 'px';
            },
            sleep: function sleep(time) {
                return new Promise(resolve => {
                    setTimeout(() => {
                        resolve();
                    }, time);
                });
            },
            timer(stepx,stepy) {
                return setInterval(() => {
                    this.carMove(stepx,stepy)
                }, 500)
            }
        }

    }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
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
  }

  /* 更改element-UI按钮样式 */
  .el-div {
    background-color: aquamarine;
    width: 1590px;
    height: 600px;
  }

  .el-car {
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }


  .el-button {
    color: #fff;
    background-color: #3D9DD9;
    border-color: #5569B4;
  }

  .el-button:hover {
    background-color: #3D9DD9;
    border-color: #6669B4;
  }

  .el-button:focus {
    background-color: #3D9DD9;
    border-color: #6669B4;
  }

  .el-people1 {
    width: 100px;
    height: 100px;
    visibility: visible;
    position: absolute;
    left: 20px;
    top: 30px;
  }

  .el-people2 {
    width: 100px;
    height: 100px;
    visibility: visible;
    position: absolute;
    left: 100px;
    top: 100px;
  }

  .el-people3 {
    width: 100px;
    height: 100px;
    visibility: visible;
    position: absolute;
    left: 300px;
    top: 100px;
  }

  .el-people4 {
    width: 100px;
    height: 100px;
    visibility: visible;
    position: absolute;
    left: 400px;
    top: 200px;
  }

  .el-people5 {
    width: 100px;
    height: 100px;
    visibility: visible;
    position: absolute;
    left: 500px;
    top: 300px;
  }


</style>
