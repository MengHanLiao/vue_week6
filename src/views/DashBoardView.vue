<template>
  <div>
    <nav class="navbar navbar-expand-md navbar-dark bg-dark">
      <div class="container-fluid flex flex-md-column">
        <router-link class="navbar-brand" to="/">後臺</router-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNavAltMarkup"
          aria-controls="navbarNavAltMarkup"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
          <div class="navbar-nav">
            <router-link class="nav-link" to="/admin/products">產品</router-link>
            <router-link class="nav-link" to="/admin/coupons">優惠卷</router-link>
            <router-link class="nav-link" to="/admin/orders">訂單</router-link>
            <button type="button" class="btn btn-primary ms-2" @click="signout">登出</button>
          </div>
        </div>
      </div>
    </nav>
    <router-view v-if="isLogin"/>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isLogin: false,
    };
  },
  methods: {
    checkLogin() {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)vueWeekSixToken\s*=\s*([^;]*).*$)|^.*$/, '$1');
      if (token) {
        this.$http.defaults.headers.common.Authorization = token;
        this.$http.post(`${process.env.VUE_APP_API_BASEURL}api/user/check`).then(() => {
          this.isLogin = true;
        }).catch((err) => {
          alert(err.response.data.message);
        });
      } else {
        this.$router.push('/login');
        alert('請先登入');
      }
    },
    signout() {
      this.$http.post(`${process.env.VUE_APP_API_BASEURL}logout`).then(() => {
        document.cookie = 'vueWeekSixToken=;expires=;';
        alert('成功登出');
        this.$router.push('/');
      }).catch((err) => {
        console.dir(err);
      });
    },
  },
  created() {
    this.checkLogin();
  },
};
</script>
