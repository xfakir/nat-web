<template>
  <div class="home">
    <el-button v-if="this.step === 1" type="primary" @click="connect"
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
      password: ""
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
    }
  }
};
</script>
