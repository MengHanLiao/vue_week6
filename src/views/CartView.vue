<template>
  <div>
    <div class="container">
      <div class="mt-4">
        <h3 class="text-center">購物車</h3>
        <table class="table align-middle">
          <thead>
            <tr>
              <th></th>
              <th>品名</th>
              <th style="width: 150px">數量/單位</th>
              <th class="text-end">單價</th>
            </tr>
          </thead>
          <tbody>
            <template v-if="carts.carts.length !== 0">
              <tr v-for="cart in carts.carts" :key="cart.id">
                <td>
                  <button
                    type="button" class="btn btn-outline-danger btn-sm"
                    @click="deleteItem(cart.id)"
                  >
                    <i class="fas fa-spinner fa-pulse"></i>
                    x
                  </button>
                </td>
                <td>
                  {{ cart.product.title }}
                </td>
                <td>
                  <div class="input-group input-group-sm">
                    <label for="productNum" class="input-group">
                      <input
                        min="1"
                        type="number"
                        class="form-control"
                        id="productNum"
                        v-model.number="cart.qty"
                        @blur="updateCart(cart.id, cart.product.id, cart.qty)"
                      />
                      <span class="input-group-text" id="basic-addon2">
                        {{ cart.product.unit }}
                      </span>
                    </label>
                  </div>
                </td>
                <td class="text-end">
                  <small class="text-success">折扣價：</small>
                  {{ cart.total }}
                </td>
              </tr>
            </template>
            <template v-else>
              <tr>
                <td colspan="4">
                  前往選購 <router-link to="/products" class="btn btn-primary">商品頁面</router-link>
                </td>
              </tr>
            </template>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="3" class="text-end">總計</td>
              <td class="text-end">NT$ {{ carts.final_total }}</td>
            </tr>
          </tfoot>
        </table>
      </div>
      <div class="my-5 row justify-content-center">
        <h3 class="text-center">購買者資訊</h3>
        <v-form ref="form" class="col-md-6" v-slot="{ errors }" @submit="submitOrder">
          <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <v-field
              id="email"
              name="email"
              type="email"
              class="form-control"
              :class="{ 'is-invalid': errors['email'] }"
              placeholder="請輸入 Email"
              rules="required|email"
              v-model="userForm.user.email"
            ></v-field>
            <error-message
              name="email"
              class="invalid-feedback"
            ></error-message>
          </div>
          <div class="mb-3">
            <label for="name" class="form-label">收件人姓名</label>
            <v-field
              id="name"
              name="姓名"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': errors['姓名'] }"
              placeholder="請輸入姓名"
              rules="required"
              v-model="userForm.user.name"
            ></v-field>
            <error-message name="姓名" class="invalid-feedback"></error-message>
          </div>
          <div class="mb-3">
            <label for="tel" class="form-label">收件人電話</label>
            <v-field
              id="tel"
              name="電話"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': errors['電話'] }"
              placeholder="請輸入電話"
              :rules="isPhone"
              v-model="userForm.user.tel"
            ></v-field>
            <error-message name="電話" class="invalid-feedback"></error-message>
          </div>
          <div class="mb-3">
            <label for="address" class="form-label">收件人地址</label>
            <v-field
              id="address"
              name="地址"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': errors['地址'] }"
              placeholder="請輸入地址"
              rules="required"
              v-model="userForm.user.address"
            ></v-field>
            <error-message name="地址" class="invalid-feedback"></error-message>
          </div>
          <div class="mb-3">
            <label for="message" class="form-label">留言</label>
            <textarea
              id="message"
              class="form-control"
              cols="30"
              rows="10"
              v-model="userForm.message"
            ></textarea>
          </div>
          <div class="text-end">
            <button
              type="submit"
              class="btn btn-danger"
              :disabled="carts.carts.length === 0 && Object.keys(errors).length === 0"
            >
              送出訂單
            </button>
          </div>
        </v-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      carts: {
        carts: [],
      },
      userForm: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: '',
        },
        message: '',
      },
    };
  },
  methods: {
    getCarts() {
      this.$http
        .get(`${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/cart`).then((res) => {
          this.carts = res.data.data;
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },
    updateCart(cartId, productId, qty = 1) {
      if (qty > 0 && Number.isInteger(qty)) {
        const data = {
          product_id: productId,
          qty,
        };
        this.$http
          .put(
            `${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/cart/${cartId}`,
            { data },
          )
          .then(() => {
            alert('成功更新商品數量');
            this.isLoading = false;
          })
          .catch((err) => {
            console.dir(err);
          });
      }
    },
    deleteItem(id) {
      this.$http.delete(`${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/cart/${id}`).then(() => {
        this.getCarts();
        alert('成功刪除商品');
      }).catch((err) => {
        alert(err.response.data.message);
      });
    },
    isPhone(value) {
      const phoneNumber = /^(09)[0-9]{8}$/;
      return phoneNumber.test(value) ? true : '需要正確的電話號碼';
    },
    submitOrder() {
      this.$http.post(`${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/order`, { data: this.userForm }).then(() => {
        alert('訂單成功送出');
        this.carts.carts = [];
        this.$refs.form.resetForm();
        this.message = '';
      }).catch((err) => {
        alert(err.response.data.message);
      });
    },
  },
  mounted() {
    this.getCarts();
  },
};
</script>
