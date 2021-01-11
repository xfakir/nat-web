<template>
  <div class="home">
    <div class="main">
      <div class="menu">
        <el-menu
          v-if="isRouterConnect"
          default-active="1"
          class="el-menu-vertical-demo"
          @select="handleSelect"
        >
          <el-menu-item index="1">
            <i class="el-icon-menu"></i>
            <span slot="title">接口列表</span>
          </el-menu-item>
          <el-menu-item index="2">
            <i class="el-icon-menu"></i>
            <span slot="title">路由配置</span>
          </el-menu-item>
          <el-menu-item index="3" v-if="routerNum === 2">
            <i class="el-icon-document"></i>
            <span slot="title">NAT配置</span>
          </el-menu-item>
          <el-menu-item index="4" v-if="routerNum === 2">
            <i class="el-icon-setting"></i>
            <span slot="title">NAT转换表</span>
          </el-menu-item>
          <el-menu-item index="5" v-if="routerNum === 3">
            <i class="el-icon-setting"></i>
            <span slot="title">测试连通</span>
          </el-menu-item>
        </el-menu>
      </div>
      <div class="info">
        <div
          class="router-info"
          v-if="
            isRouterConnect &&
              (routerNum === 1 || routerNum === 2 || routerNum === 3)
          "
        >
          <div
            style="text-align: left;margin-top: 30px;margin-left: 10px;margin-right: 10px"
          >
            <span>Router{{ routerNum }}</span>
            <div style="float: right">
              <el-button type="primary" size="small" @click="disconnect"
                >断开连接</el-button
              >
            </div>
            <div v-if="!isTelnetMode" style="float: right">
              <el-button type="primary" size="small" @click="showTelnet"
                >Telnet</el-button
              >
            </div>
            <div v-else style="float: right">
              <el-button type="primary" size="small" @click="returnRouter"
                >返回</el-button
              >
            </div>
          </div>
          <div class="interface" v-if="infoKey === '1'">
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
          <div class="router" v-if="infoKey === '2'">
            <div style="padding: 3px;width: 420px">
              <el-form
                label-position="left"
                label-width="80px"
                :model="routerConfig"
                size="small"
              >
                <el-form-item label="network">
                  <el-input v-model="routerConfig.network"></el-input>
                </el-form-item>
                <el-form-item label="mask">
                  <el-input v-model.number="routerConfig.mask"></el-input>
                </el-form-item>
                <el-form-item label="next hop">
                  <el-input v-model="routerConfig.nextHop"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="submitRouter"
                    >提交</el-button
                  >
                </el-form-item>
              </el-form>
            </div>
          </div>
          <div class="nat" v-if="infoKey === '3'">
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
          <div class="tran" v-if="infoKey === '4'">
            <el-table
              :data="natTranList"
              class="tb-edit"
              style="width: 100%"
              highlight-current-row
            >
              <el-table-column label="protocol" width="80">
                <template scope="scope">
                  <span>{{ scope.row.protocol }}</span>
                </template>
              </el-table-column>
              <el-table-column label="local address" width="120">
                <template scope="scope">
                  <span>{{ scope.row.localAddress }}</span>
                </template>
              </el-table-column>
              <el-table-column label="global address" width="120">
                <template scope="scope">
                  <span>{{ scope.row.globalAddress }}</span>
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="ping-box" v-if="infoKey === '5'">
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
      </div>
      <div class="topology">
        <div class="floor">
          <el-image
            :src="require('../assets/tuoputu_taishidiannao.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
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
            :src="require('../assets/1.png')"
            style="width: 60px;height: 60px;"
          ></el-image>
        </div>
        <div class="floor">
          <el-image
            :src="require('../assets/router.png')"
            style="width: 60px;height: 60px;"
            @mouseover="mouseOver(4)"
            :style="active === 4 ? 'cursor: pointer' : ''"
            @mouseleave="mouseLeave"
            @click="showCard(1)"
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
              @click="showCard(2)"
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
              @click="showCard(3)"
            ></el-image>
          </div>
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
      routeConfig: {
        network: "",
        mask: "",
        nextHop: ""
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
      routerNum: -1,
      infoKey: "1",
      natTranList: [
        {
          protocol: "",
          localAddress: "",
          globalAddress: ""
        }
      ],
      isTelnetMode: false,
      RouterA: "192.168.1.1",
      RouterB: "10.0.0.1",
      RouterC: "10.0.0.2"
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
      if (!this.isTelnetMode) {
        await this.axios.get("/command/connectRouter", {
          params: {
            hostName: this.hostName,
            port: 23
          }
        });
      } else {
        await this.axios.get("/command/telnet", {
          params: {
            hostName: this.hostName
          }
        });
      }
      this.step = 2;
    },
    async returnRouter() {
      await this.axios.get("/command/interfaceInfo").then(response => {
        if (response.data.code === 200) {
          const list = response.data.data;
          this.interfaceList = list;
          for (let i = 0; i < list.length; i++) {
            this.radioArray[i].disable = list[i].state === "0";
            this.interfaceMap.set(list[i].name, i);
          }
          this.isTelnetMode = false;
          this.routerNum = 2;
        }
      });
    },
    showTelnet() {
      this.isTelnetMode = true;
      this.isRouterConnect = false;
      this.step = 1;
      this.show = 0;
      console.log(
        (!this.isRouterConnect || this.isTelnetMode) && this.show === 0
      );
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
            this.show = 0;
            this.infoKey = "1";
            if (this.hostName === this.RouterA) {
              this.routerNum = 1;
            }
            if (this.hostName === this.RouterB) {
              this.routerNum = 2;
            }
            if (this.hostName === this.RouterC) {
              this.routerNum = 3;
            }
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
          this.natTranList = response.data.data;
        }
      });
    },
    submitRouter() {
      this.axios
        .post("/command/configRoute", this.routeConfig)
        .then(response => {
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
    showCard(index) {
      if (this.isRouterConnect) {
        this.$message({
          message: "请先断开当前连接",
          type: "error"
        });
      } else {
        this.show = 0;
        this.routerNum = index;
        this.infoKey = "1";
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
              message: "failure",
              type: "error"
            });
          }
        });
    },
    handleSelect(key) {
      console.log(typeof key);
      this.infoKey = key;
    }
  }
};
</script>

<style>
.home {
  margin-top: 20px;
  margin-left: 150px;
}
.main {
  width: 1200px;
  height: 660px;
  border: #2c3e50 1px solid;
}
.menu {
  width: 180px;
  float: left;
  margin-left: 10px;
  margin-top: 10px;
}
.info {
  width: 500px;
  height: 640px;
  float: left;
  border: #2c3e50 1px solid;
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
.tran {
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
.router {
  width: 380px;
  height: 180px;
  margin-top: 30px;
  margin-left: 10px;
}
.ping-box {
  width: 380px;
  height: 180px;
  margin-top: 10px;
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
</style>
