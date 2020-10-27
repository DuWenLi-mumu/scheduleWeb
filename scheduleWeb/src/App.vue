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
    <button class="el-button" @click="start">开始</button>
    <button class="el-button" @click="end">结束</button>

    <div id="main" style="width: 600px;height: 400px;"></div>
  </div>
</template>

<script>
    import echarts from 'echarts'
    export default {
        created() {
            this.timer()
        },
        name: 'App',
        data() {
            return {
                SJF: [2, 2, 1, 1, 1, 3, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                FIFO: [1, 1, 1, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 3, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                Priority: [2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 1, 1, 1, 3, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 4, 4, 4, 4, 4, 4, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                EDF: [2, 2, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 3, 4, 4, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 4, 4, 4, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, -1, -1, -1, -1, -1, -1, -1, -1, -1],
                carCoordinate: [20, 10],
                peopleCoordinate: [200, 200],
            }
        },
        methods: {
            drawPie(id){
                this.charts = echarts.init(document.getElementById(id));
                let xAxis = [];
                for(let i=1;i<=this.SJF.length;i++){
                    xAxis.push(i);
                }
                this.charts.setOption({
                    title: {
                        text: 'Task Execution'
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
                            data: this.SJF
                        }
                    ]
                })
            },

            start: function () {
                this.drawPie("main");
                var times = 0;
                while (times < 10) {
                    setTimeout(this.funTmp, 2000);
                    this.carMove();
                    times++;
                }
            },
            end: function () {

            },
            carMove: function () {
                var carDiv = document.getElementById('divCarId');
                this.carCoordinate[0] = (this.carCoordinate[0] + 20) % 1590;
                carDiv.style.left = this.carCoordinate[0] + 'px';
            },
            sleep: function sleep(time) {
                return new Promise(resolve => {
                    setTimeout(() => {
                        resolve();
                    }, time);
                });
            },
            timer() {
                return setInterval(() => {
                    this.carMove()
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
