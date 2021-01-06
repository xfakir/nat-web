<template>
  <div class="home">
    <div class="main">
      <div class="info">
        <div class="router-info" v-if="isRouterConnect && show === 0">
          <div class="interface">
            <div style="text-align: left">
              <span>Interface Configure</span>
              <div style="float: right">
                <el-button type="primary" size="small" @click="disconnect"
                  >断开连接</el-button
                >
              </div>
            </div>
            <el-table
              :data="interfaceList"
              class="tb-edit"
              style="width: 100%"
              highlight-current-row
              @row-click="handleCurrentChange"
            >
              <el-table-column label="interface" width="120">
                <template scope="scope">
                  <span>{{ scope.row.name }}</span>
                </template>
              </el-table-column>
              <el-table-column label="ip" width="100">
                <template scope="scope">
                  <el-input
                    size="small"
                    v-model="scope.row.address"
                    placeholder="请输入内容"
                  ></el-input>
                  <span>{{ scope.row.address }}</span>
                </template>
              </el-table-column>
              <el-table-column label="netmask" width="100">
                <template scope="scope">
                  <el-input
                    size="small"
                    v-model="scope.row.netmask"
                    placeholder="请输入内容"
                  ></el-input>
                  <span>{{ scope.row.netmask }}</span>
                </template>
              </el-table-column>
              <el-table-column label="On/Off" width="70">
                <template slot-scope="scope">
                  <el-switch
                    v-model="scope.row.state"
                    active-value="1"
                    inactive-value="0"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                  />
                </template>
              </el-table-column>
              <el-table-column label="操作" width="70">
                <template scope="scope">
                  <el-button
                    size="small"
                    type="primary"
                    @click="handleEdit(scope.$index)"
                    >提交</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="nat" v-if="routerNum === 0">
            <div style="text-align: left">
              <span>NAT Configure</span>
            </div>
            <div style="padding: 3px;width: 420px">
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
                <el-form-item label="netmask">
                  <el-input v-model="natConfig.netmask"></el-input>
                </el-form-item>
                <el-form-item label="inside">
                  <el-checkbox-group v-model="natConfig.inside">
                    <el-checkbox
                      v-for="(radio, index) in radioArray"
                      :label="radio.name"
                      :disabled="radio.disable"
                      :key="index"
                    ></el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
                <el-form-item label="outside">
                  <el-checkbox-group v-model="natConfig.outside">
                    <el-checkbox
                      v-for="(radio, index) in radioArray"
                      :label="radio.name"
                      :disabled="radio.disable"
                      :key="index"
                    ></el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit">提交</el-button>
                </el-form-item>
              </el-form>
            </div>
          </div>
          <div class="ping-box" v-else>
            <el-form :inline="true">
              <el-form-item label="ping">
                <el-input v-model="pingIp"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="ping">ping</el-button>
              </el-form-item>
            </el-form>
          </div>
        </div>
        <div v-else-if="!isRouterConnect && show === 0">
          <el-form v-if="this.step === 1" :inline="true">
            <el-form-item label="hostName">
              <el-input v-model="hostName"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="connect">connect</el-button>
            </el-form-item>
          </el-form>
          <el-form v-if="this.step === 2" :inline="true">
            <el-form-item label="password">
              <el-input v-model="password"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="sendPassword">send</el-button>
            </el-form-item>
          </el-form>
        </div>
        <div
          class="pc-info"
          v-else-if="show === 1 || show === 2 || show === 3 || show === 4"
        >
          <div>
            <span>PC{{ showPC }} Info</span>
          </div>
          <div class="info-box">
            <span v-if="show !== 4">IP: 10.1.0.{{ show + 1 }}</span>
            <span v-else>IP: 192.168.3.2</span>
          </div>
          <div class="info-box">
            <span v-if="show !== 4">netmask: 255.255.0.0</span>
            <span v-else>netmask: 255.255.255.0</span>
          </div>
          <div class="info-box">
            <span v-if="show !== 4">gateway: 10.1.0.1</span>
            <span v-else>gateway: 192.168.3.1</span>
          </div>
        </div>
      </div>
      <div class="topology">
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 60px;height: 60px;"
            @mouseover="mouseOver(4)"
            :style="active === 4 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(4)"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/1.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/router.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/2.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <div style="width: 40%;height: 100%;float: left"></div>
          <div style="width: 20%;height: 100%;float: left">
            <el-image
              :src="require('../assets/router.png')"
              style="width: 60px;height: 60px;"
              @mouseover="mouseOver(0)"
              :style="active === 0 ? 'cursor: pointer' : ''"
              @mouseleave="mouseLeave"
              @click="showCard(0, 0)"
            ></el-image>
          </div>
          <div style="width: 20%;height: 100%;float: left">
            <el-image
              :src="require('../assets/5.png')"
              style="width: 60px;height: 60px;"
            ></el-image>
          </div>
          <div>
            <el-image
              :src="require('../assets/router.png')"
              style="width: 60px;height: 60px;"
              @mouseover="mouseOver(0)"
              :style="active === 0 ? 'cursor: pointer' : ''"
              @mouseleave="mouseLeave"
              @click="showCard(0, 1)"
            ></el-image>
          </div>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/3.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/switch.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/4.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 60px;height: 60px;"
            @mouseover="mouseOver(1)"
            :style="active === 1 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(1)"
          ></el-image>
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 60px;height: 60px;"
            @mouseover="mouseOver(2)"
            :style="active === 2 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(2)"
          ></el-image>
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 60px;height: 60px;"
            @mouseover="mouseOver(3)"
            :style="active === 3 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(3)"
          ></el-image>
        </div>
      </div>
    </div>
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
      interfaceList: [],
      inputStatus: "none",
      natConfig: {
        network: "",
        total: 0,
        netmask: "",
        inside: [],
        outside: []
      },
      show: -1,
      pingIp: "",
      active: -1,
      showPC: 0,
      isRouterConnect: false,
      hostName: "",
      radioArray: [
        {
          name: "f0/0",
          disable: false
        },
        {
          name: "f0/1",
          disable: false
        },
        {
          name: "s0/0/0",
          disable: false
        },
        {
          name: "s0/0/1",
          disable: false
        }
      ],
      insideArray: [false, false, false, false],
      outsideArray: [false, false, false, false],
      interfaceMap: new Map(),
      routerNum: -1
    };
  },
  /*created() {
    this.axios.get("/command/test").then(response => {
      this.msg = response.data;
    });
  },*/
  components: {},
  methods: {
    async connect() {
      await this.axios.get("/command/connectRouter", {
        params: {
          hostName: this.hostName,
          port: 23
        }
      });
      this.step = 2;
    },
    async sendPassword() {
      await this.axios
        .get("/command/password", {
          params: {
            password: this.password
          }
        })
        .then(response => {
          if (response.data.code === 200) {
            const list = response.data.data;
            this.interfaceList = list;
            this.isRouterConnect = true;
            for (let i = 0; i < list.length; i++) {
              this.radioArray[i].disable = list[i].state === "0";
              this.interfaceMap.set(list[i].name, i);
            }
            console.log(this.radioArray);
          }
        });
    },
    handleCurrentChange(row, event, column) {
      console.log(row, event, column, event.currentTarget);
    },
    handleEdit(index) {
      this.axios
        .post("/command/configInterface", this.interfaceList[index])
        .then(response => {
          if (response.data.code === 200) {
            this.$message({
              message: "修改成功",
              type: "success"
            });
          }
        });
      this.radioArray[index].disable = this.interfaceList[index].state === "0";
    },
    onSubmit() {
      console.log(this.natConfig);
      this.axios.post("/command/configNat", this.natConfig).then(response => {
        if (response.data.code === 200) {
          this.$message({
            message: "配置成功",
            type: "success"
          });
        }
      });
    },
    disconnect() {
      this.axios.post("/command/disconnect").then(response => {
        if (response.data.code === 200) {
          this.$message({
            message: "已断开连接",
            type: "success"
          });
          this.isRouterConnect = false;
          this.step = 1;
          this.hostName = "";
          this.password = "";
          this.show = -1;
        }
      });
    },
    clickInside(val) {
      for (let i = 0; i < this.outsideArray.length; i++) {
        this.outsideArray[i] = false;
      }
      const index = this.interfaceMap.get(val);
      this.outsideArray[index] = true;
      this.$forceUpdate();
    },
    clickOutside(val) {
      for (let i = 0; i < this.insideArray.length; i++) {
        this.insideArray[i] = false;
      }
      const index = this.interfaceMap.get(val);
      this.insideArray[index] = true;
      this.$forceUpdate();
    },
    mouseOver(index) {
      //改变样式
      //this.active} = "cursor: pointer";
      this.active = index;
    },
    mouseLeave() {
      //清空样式
      this.active = -1;
    },
    showCard(index, routerNum) {
      this.show = index;
      if (index === 0) {
        this.routerNum = routerNum;
      }
      if (index !== 0) {
        this.showPC = index;
      }
    },
    async ping() {
      await this.axios
        .get("/command/ping", {
          params: {
            ip: this.pingIp
          }
        })
        .then(response => {
          if (response.data.code === 200) {
            this.$message({
              message: "success",
              type: "success"
            });
          } else {
            this.$message({
              message: "error",
              type: "error"
            });
          }
        });
    }
  }
};
</script>

<style>
.home {
  margin-top: 20px;
  margin-left: 250px;
}
.main {
  width: 1000px;
  height: 660px;
  border: #2c3e50 1px solid;
}
.info {
  width: 500px;
  height: 640px;
  float: left;
  border: #2c3e50 1px solid;
  margin-left: 10px;
  margin-top: 10px;
}
.topology {
  width: 400px;
  height: 590px;
  float: left;
  border: #2c3e50 1px solid;
  margin-left: 40px;
  margin-top: 10px;
  padding-top: 50px;
}
.floor {
  height: 60px;
}
.interface {
  width: 480px;
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
.ping-box {
  width: 380px;
  height: 180px;
  margin-top: 30px;
  margin-left: 10px;
  padding-top: 50px;
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
