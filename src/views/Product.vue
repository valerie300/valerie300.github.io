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
  單一頁面
  {{product.title}}
</template>

<script>
export default {
  data() {
    return {
      product: {},
    };
  },
  created() {
    // const id = this.$route.params.id 要改物件形式
    const { id } = this.$route.params;
    const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/product/${id}`;
    this.$http.get(url).then((res) => {
      if (res.data.success) {
        this.product = res.data.product;
      } else {
        console.log(res.data.message);
      }
    });
  },
};
</script>
