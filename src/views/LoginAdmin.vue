<template>
  <div class="container my-5">
    <h2 class="text-center">管理者登入</h2>
    <div class="row justify-content-center">
      <v-form ref="form" class="col-md-4"
        v-slot="{ errors }" @submit="loginSubmit">
        <div class="mb-3">
          <label for="adminEmail" class="form-label">Email</label>
          <v-field
            id="adminEmail"
            name="adminEmail"
            type="email"
            class="form-control"
            :class="{ 'is-invalid': errors['adminEmail'] }"
            placeholder="請輸入 Email"
            rules="required|email"
            v-model="admin.username"
          ></v-field>
          <error-message
            name="adminEmail"
            class="invalid-feedback"
          ></error-message>
        </div>
        <div class="mb-3">
          <label for="adminPassword" class="form-label">密碼</label>
          <v-field
            id="adminPassword"
            name="adminPassword"
            type="password"
            class="form-control"
            :class="{ 'is-invalid': errors['adminPassword'] }"
            placeholder="請輸入密碼"
            rules="required"
            v-model="admin.password"
          ></v-field>
          <error-message
            name="adminPassword"
            class="invalid-feedback"
          ></error-message>
        </div>
        <button
          type="submit"
          class="btn btn-primary"
        >登入</button>
      </v-form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      admin: {
        username: '',
        password: '',
      },
    };
  },
  methods: {
    loginSubmit() {
      this.$http.post(`${process.env.VUE_APP_API_BASEURL}admin/signin`, this.admin).then((res) => {
        console.log(res);
        alert('成功登入');
        const { token, expired } = res.data;
        document.cookie = `vueWeekSixToken=${token};expires=${new Date(expired)};`;
        this.$router.push('/admin/products');
      }).catch((err) => {
        console.dir(err);
      });
    },
  },
};
</script>
