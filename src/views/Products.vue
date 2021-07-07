<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <router-link class="nav-link" to="/">首頁</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/products"
              >前台產品列表</router-link
            >
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/carts"
              >前台購物車列表</router-link
            >
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/dashboard"
              >後台台購物車列表</router-link
            >
          </li>
        </ul>
      </div>
    </div>
  </nav>
  <h3>前台產品列表</h3>
  <div class="container">
    <table class="table align-middle">
      <thead>
        <tr>
          <th>圖片</th>
          <th>商品名稱</th>
          <th>價格</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div
              style="
                height: 100px;
                background-size: cover;
                background-position: center;
              "
              :style="{ backgroundImage: `url(${item.imageUrl})` }"
            ></div>
          </td>
          <td>
            {{ item.title }}
          </td>
          <td>
            <div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
            <del class="h6" v-if="item.price"
              >原價 {{ item.origin_price }} 元</del
            >
            <div class="h5" v-if="item.price">現在只要 {{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
              <button
                type="button"
                class="btn btn-outline-secondary"
                @click="getModalProduct(item.id)"
                :disabled="
                  loadingStatus.loadingItemId === item.id || !item.is_enabled
                "
              >
                <i
                  class="fas fa-spinner fa-pulse"
                  v-if="loadingStatus.loadingItemId === item.id"
                ></i>
                查看更多
              </button>
              <button
                type="button"
                class="btn btn-outline-danger"
                @click="addToCart(item.id, item.title)"
                :disabled="
                  loadingStatus.loadingItemId === item.id || !item.is_enabled">
                <i
                  class="fas fa-spinner fa-pulse"
                  v-if="loadingStatus.loadingItemId === item.id">
                  </i>
                加到購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <productModal
      ref="productModal"
      :product="product"
      @add-to-cart="addToCart"
    ></productModal>
  </div>
</template>

<script>
import productModal from '@/components/ProductModal.vue';

export default {
  data() {
    return {
      // 產品列表
      products: [],
      pagination: {},
      loadingStatus: {
        loadingItemId: '',
      },
      isLoading: true,
      // props 外層傳到內層的資料
      product: {},
    };
  },
  components: {
    productModal,
  },
  methods: {
    getProductData() {
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/products`;
      this.$http
        .get(url)
        .then((res) => {
          if (res.data.success) {
            this.products = res.data.products;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    addToCart(itemId, itemTitle, qty = 1) {
      this.loadingStatus.loadingItemId = itemId;
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/cart`;
      this.$http
        .post(url, {
          data: {
            product_id: itemId,
            qty,
          },
        })
        .then((res) => {
          if (res.data.success) {
            console.log(itemTitle + res.data.message);
            this.$refs.productModal.hideModal();
            this.loadingStatus.loadingItemId = '';
          } else {
            console.log(res.data.message);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getModalProduct(itemId) {
      this.loadingStatus.loadingItemId = itemId;
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/product/${itemId}`;
      this.$http.get(url)
        .then((res) => {
          if (res.data.success) {
            this.product = res.data.product;
            this.$refs.productModal.openModal();
            this.loadingStatus.loadingItemId = '';
          } else {
            console.log(res.data.message);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
  created() {
    this.getProductData();
  },
};
</script>
