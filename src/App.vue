<template>
  <div class="container" id="app">
    <div class="row">
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <span class="navbar-brand">NoteBook</span>
        <router-link to="/" class="nav-item active nav-link">首页</router-link>
        <a
          v-if="!data.currentUser || !data.currentUser.name"
          class="my-2 my-lg-0"
          href="/login/oauth/github/auth"
        >登录</a>
        <span v-else class="my-2 my-lg-0">{{data.currentUser.name}}</span>
      </nav>
    </div>
    <router-view></router-view>
    <footer>
      <p>
        系统版本:{{data.systemInfo.version}}
        <br>
        启动时间: {{data.systemInfo.bootTime}}
      </p>
    </footer>
  </div>
</template>

<style>
a {
  color: -webkit-link;
}

div > a:visited {
  background: lightgray;
}

div > a:link {
  background: lightblue;
}
</style>
<script>
export default {
  data: function() {
    return {
      data: {
        currentUser: {},
        systemInfo: {}
      }
    };
  },
  created: function() {
    var that = this;
    var query = `
            {
            currentUser{
                name
                email
            }
            systemInfo{
                bootTime
                version
            }
            }
        `;
    fetch("/api/graphql", {
      method: "POST",
      credentials: "same-origin",
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        query: query
      })
    })
      .then(function(resp) {
        if (resp.ok) {
          return resp.json();
        } else {
          alert("请求失败:" + resp.status);
        }
      })
      .then(function(resp) {
        console.log(resp);
        console.log(that.data);
        if (resp && resp.data) {
          console.log(resp.data);
          that.data = resp.data;
        }
      });
  }
};
</script>

