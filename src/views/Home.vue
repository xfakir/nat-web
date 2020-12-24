<template>
  <div class="home">
    <div class="main">
      <div class="info">
        <div class="router-info" v-if="show === 0">
          <div class="interface">
            <div style="text-align: left">
              <span>Interface Configure</span>
            </div>
            <el-table
              :data="interfaceList"
              class="tb-edit"
              style="width: 100%"
              highlight-current-row
              @row-click="handleCurrentChange"
            >
              <el-table-column label="interface" width="80">
                <template scope="scope">
                  <span>{{ scope.row.interface }}</span>
                </template>
              </el-table-column>
              <el-table-column label="ip" width="100">
                <template scope="scope">
                  <el-input
                    size="small"
                    v-model="scope.row.ip"
                    placeholder="请输入内容"
                    @change="handleEdit(scope.$index, scope.row)"
                  ></el-input>
                  <span>{{ scope.row.ip }}</span>
                </template>
              </el-table-column>
              <el-table-column label="netmask" width="100">
                <template scope="scope">
                  <el-input
                    size="small"
                    v-model="scope.row.netmask"
                    placeholder="请输入内容"
                    @change="handleEdit(scope.$index, scope.row)"
                  ></el-input>
                  <span>{{ scope.row.netmask }}</span>
                </template>
              </el-table-column>
              <el-table-column label="On/Off" width="70">
                <template slot-scope="scope">
                  <el-switch
                    v-model="scope.row.status"
                    :active-value="0"
                    :inactive-value="1"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                    @change="changeSwitch(scope.row)"
                  />
                </template>
              </el-table-column>
              <el-table-column label="操作" width="70">
                <template scope="scope">
                  <el-button
                    size="small"
                    type="primary"
                    @click="handleDelete(scope.$index, scope.row)"
                    >提交</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="nat">
            <div style="text-align: left">
              <span>NAT Configure</span>
            </div>
            <div style="padding: 3px">
              <el-form
                label-position="left"
                label-width="80px"
                :model="natConfig"
                size="small"
              >
                <el-form-item label="network">
                  <el-input v-model="natConfig.network"></el-input>
                </el-form-item>
                <el-form-item label="total">
                  <el-input v-model.number="natConfig.total"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit">提交</el-button>
                </el-form-item>
              </el-form>
            </div>
          </div>
        </div>
        <div class="pc-info" v-else-if="show === 1 || show === 2 || show === 3">
          <div>
            <span>PC{{ showPC }} Info</span>
          </div>
          <div class="info-box">
            <span>IP: 10.0.0.2</span>
          </div>
          <div class="info-box">
            <span>netmask: 255.0.0.0</span>
          </div>
          <div class="info-box">
            <span>gateway: 10.0.0.1</span>
          </div>
          <div class="ping-box">
            <el-form :inline="true">
              <el-form-item label="ping">
                <el-input v-model="pingIp"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onSubmit">ping</el-button>
              </el-form-item>
            </el-form>
          </div>
        </div>
      </div>
      <div class="topology">
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/1.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_luyouqi.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/2.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_luyouqi.png')"
            style="width: 50px;height: 50px;"
            @mouseover="mouseOver(0)"
            :style="active === 0 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(0)"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/3.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/switch.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/4.png')"
            style="width: 50px;height: 50px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 50px;height: 50px;"
            @mouseover="mouseOver(1)"
            :style="active === 1 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(1)"
          ></el-image>
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 50px;height: 50px;"
            @mouseover="mouseOver(2)"
            :style="active === 2 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(2)"
          ></el-image>
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 50px;height: 50px;"
            @mouseover="mouseOver(3)"
            :style="active === 3 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(3)"
          ></el-image>
        </div>
      </div>
    </div>
    <!--<el-button v-if="this.step === 1" type="primary" @click="connect"
      >connect</el-button
    >
    <div v-else-if="this.step === 2">
      username:<el-input v-model="username"></el-input>
      <el-button type="primary" @click="sendUsername">send</el-button>
    </div>
    <div v-else-if="this.step === 3">
      password:<el-input v-model="password"></el-input>
      <el-button type="primary" @click="sendPassword"
        >send</el-button
      >
    </div>-->
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  data() {
    return {
      msg: "",
      step: 1,
      username: "",
      password: "",
      interfaceList: [
        {
          interface: "f0/0",
          ip: "10.0.0.1",
          netmask: "10.0.0.0",
          status: 1
        },
        {
          interface: "f0/1",
          ip: "10.0.0.2",
          netmask: "10.0.0.0",
          status: 0
        },
        {
          interface: "f0/1",
          ip: "10.0.0.2",
          netmask: "10.0.0.0",
          status: 1
        },
        {
          interface: "f0/1",
          ip: "10.0.0.2",
          netmask: "10.0.0.0",
          status: 0
        }
      ],
      inputStatus: "none",
      natConfig: {
        network: "",
        total: 0
      },
      show: -1,
      pingIp: "",
      active: -1,
      showPC: 0
    };
  },
  /*created() {
    this.axios.get("/command/test").then(response => {
      this.msg = response.data;
    });
  },*/
  components: {},
  methods: {
    connect() {
      this.axios.get("/command/connect");
      this.step = 2;
    },
    sendUsername() {
      this.axios.get("/command/username", {
        params: {
          username: this.username
        }
      });
      this.step = 3;
    },
    sendPassword() {
      this.axios.get("/command/password", {
        params: {
          password: this.password
        }
      });
    },
    handleCurrentChange(row, event, column) {
      console.log(row, event, column, event.currentTarget);
    },
    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    changeSwitch(row) {
      // eslint-disable-next-line no-unused-vars
      const data = {
        id: row.id,
        status: row.status
      };
    },
    onSubmit() {},
    mouseOver(index) {
      //改变样式
      //this.active} = "cursor: pointer";
      this.active = index;
    },
    mouseLeave() {
      //清空样式
      this.active = -1;
    },
    showCard(index) {
      this.show = index;
      if (index !== 0) {
        this.showPC = index;
      }
    }
  }
};
</script>

<style>
.home {
  margin-top: 80px;
  margin-left: 250px;
}
.main {
  width: 1000px;
  height: 550px;
  border: #2c3e50 1px solid;
}
.info {
  width: 450px;
  height: 500px;
  float: left;
  border: #2c3e50 1px solid;
  margin-left: 60px;
  margin-top: 25px;
}
.topology {
  width: 400px;
  height: 500px;
  float: left;
  border: #2c3e50 1px solid;
  margin-left: 40px;
  margin-top: 25px;
}
.floor {
  height: 50px;
}

.interface {
  width: 430px;
  height: 280px;
  margin-top: 10px;
  margin-left: 10px;
  padding: 3px;
}
.nat {
  width: 380px;
  height: 180px;
  margin-top: 30px;
  margin-left: 10px;
}
.tb-edit .el-input {
  display: none;
}
.tb-edit .current-row .el-input {
  display: block;
}
.tb-edit .current-row .el-input + span {
  display: none;
}

.pc-info {
  text-align: left;
}
.info-box {
  height: 30px;
  align-content: center;
  padding: 10px;
  margin-top: 5px;
}
.ping-box {
  margin-top: 10px;
  margin-left: 10px;
}
</style>
