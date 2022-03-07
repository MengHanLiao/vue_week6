/* eslint-disable no-unused-vars */
<template>
  <div class="container my-5">
    <div class="row justify-content-center">
      <div class="col-6">
        <div class="card">
          <img
            :src="product.imageUrl"
            class="card-img-top img-fluid"
            :alt="product.title"
            style="height: 16rem"
          />
          <div class="card-body">
            <h5 class="card-title">{{ product.title }}</h5>
            <p class="card-text">
              {{ product.description }}
            </p>
            <p class="card-text">材料： {{ product.content }}</p>
            <template v-if="product.origin_price === product.price">
              <h4>價格: {{ product.price }}</h4>
            </template>
            <template v-else>
              <h4>
                價格: <del class="fs-5 me-2">{{ product.origin_price }}</del>
                {{ product.price }}
              </h4>
            </template>
            <button
              type="button"
              class="btn btn-danger w-25 mt-3"
              @click="addToCart(product.id)"
              :disabled="isLoading"
            >
              <span
                class="spinner-border spinner-border-sm text-light"
                :class="{ 'visually-hidden': !isLoading }"
              ></span>
              加入購物車
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      product: {},
      isLoading: false,
    };
  },
  methods: {
    getSpecificProduct() {
      const { id } = this.$route.params;
      this.$http(
        `${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/product/${id}`,
      ).then((res) => {
        console.log(res);
        this.product = res.data.product;
      }).catch((err) => {
        alert(err.response.data.message);
      });
    },
    addToCart(id, qty = 1) {
      this.isLoading = true;
      const data = {
        product_id: id,
        qty,
      };
      this.$http
        .post(
          `${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/cart`,
          { data },
        )
        .then(() => {
          alert('成功加入購物車');
          this.isLoading = false;
        })
        .catch((err) => {
          console.dir(err);
        });
    },
  },
  mounted() {
    this.getSpecificProduct();
  },
};
</script>
