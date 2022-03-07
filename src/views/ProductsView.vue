<template>
  <div>
    <div class="container my-5">
      <ul
        class="
          row
          align-content-stretch
          row-cols-1 row-cols-md-2 row-cols-lg-4
          gy-4
        "
      >
        <li class="col" v-for="product in products" :key="product.id">
          <div class="card h-100">
            <img
              :src="product.imageUrl"
              class="card-img-top"
              :alt="product.title"
            />
            <div class="card-body">
              <h5 class="card-title">{{ product.title }}</h5>
              <p class="card-text">
                {{ product.description }}
              </p>
            </div>
            <div class="card-footer bg-transparent border-top-0">
              <router-link
                type="button"
                class="btn btn-outline-primary w-100 mb-2"
                :to="`/product/${product.id}`"
              >
                查看更多資訊
              </router-link>
              <button
                type="button"
                class="btn btn-danger w-100"
                @click="addToCart(product.id)"
                :disabled="product.id===isLoadingItem"
              >
                <span
                  class="spinner-border spinner-border-sm text-light"
                  :class="{'visually-hidden': product.id !== isLoadingItem}"
                ></span>
                加入購物車
              </button>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
.card-img-top {
  width: auto;
  height: 12rem;
  object-fit: cover;
}
</style>

<script>
export default {
  data() {
    return {
      products: [],
      isLoadingItem: '',
    };
  },
  methods: {
    getProductsData() {
      this.$http
        .get(
          `${process.env.VUE_APP_API_BASEURL}api/${process.env.VUE_APP_PATH}/products/all`,
        )
        .then((response) => {
          this.products = response.data.products;
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },
    addToCart(id, qty = 1) {
      this.isLoadingItem = id;
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
          this.isLoadingItem = '';
        })
        .catch((err) => {
          console.dir(err);
        });
    },
  },
  mounted() {
    this.getProductsData();
  },
};
</script>
