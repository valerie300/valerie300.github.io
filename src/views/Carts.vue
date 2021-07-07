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
  前台購物車列表
  <div class="container">
    <div class="text-end">
      <button
        class="btn btn-outline-danger"
        type="button"
        @click="deleteAllCarts"
        :class="{ disabled: cart.carts.length === 0 }"
      >
        清空購物車
      </button>
    </div>
    <table class="table align-middle">
      <thead>
        <tr>
          <th></th>
          <th>品名</th>
          <th style="width: 150px">數量/單位</th>
          <th>金額</th>
        </tr>
      </thead>
      <tbody>
        <template v-if="cart.carts">
          <tr v-for="item in cart.carts" :key="item.id">
            <td>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="removeCartItem(item.id, item.product.title)"
                :disabled="loadingStatus.loadingItemId === item.id"
              >
                <i
                  class="fas fa-spinner fa-pulse"
                  v-if="loadingStatus.loadingItemId === item.id"
                ></i>
                x
              </button>
            </td>
            <td>
              {{ item.product.title }}
            </td>
            <td>
              <div class="input-group input-group-sm">
                <div class="input-group mb-3">
                  <input
                    v-model.number="item.qty"
                    @change="updateCart(item)"
                    :disabled="loadingStatus.loadingItemId === item.id"
                    min="1"
                    type="number"
                    class="form-control"
                  />
                  <span class="input-group-text" id="basic-addon2">{{
                    item.product.unit
                  }}</span>
                </div>
              </div>
            </td>
            <td class="text-end">
              {{ item.final_total }}
            </td>
          </tr>
        </template>
        <template>
          <tr>
            <td>
              <div>購物車內無品項</div>
            </td>
          </tr>
        </template>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3" class="text-end">總計</td>
          <td class="text-end">{{ cart.total }}</td>
        </tr>
        <tr v-if="cart.final_total !== cart.total">
          <td colspan="3" class="text-end text-success">折扣價</td>
          <td class="text-end text-success">{{ cart.final_total }}</td>
        </tr>
      </tfoot>
    </table>
  </div>
  <!-- 表單驗證 -->
  <div class="my-5 row justify-content-center">
    <Form v-slot="{ errors }" @submit="createOrder" class="col-md-6" ref="form">
      <div class="mb-3">
        <label for="name" class="form-lable">姓名</label>
        <Field
          id="name"
          name="姓名"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': errors['姓名'] }"
          placeholder="請輸入 姓名"
          rules="required"
          v-model="form.user.name"
        ></Field>
        <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
      </div>
      <div class="mb-3">
        <label for="name" class="form-lable">Email</label>
        <Field
          id="email"
          name="email"
          type="email"
          class="form-control"
          :class="{ 'is-invalid': errors['email'] }"
          placeholder="請輸入 Email"
          rules="email|required"
          v-model="form.user.email"
        ></Field>
        <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
      </div>
      <div class="mb-3">
        <label for="name" class="form-lable">電話</label>
        <Field
          id="tel"
          name="電話"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': errors['電話'] }"
          placeholder="請輸入 電話"
          :rules="isPhone"
          v-model="form.user.tel"
        ></Field>
        <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
      </div>
      <div class="mb-3">
        <label for="name" class="form-lable">地址</label>
        <Field
          id="address"
          name="地址"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': errors['地址'] }"
          placeholder="請輸入 地址"
          rules="required"
          v-model="form.user.address"
        ></Field>
        <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
      </div>
      <div class="mb-3">
        <label for="message" class="form-label">留言</label>
        <textarea
          name="message"
          id="message"
          class="form-control"
          cols="30"
          rows="10"
          v-model="form.message"
        ></textarea>
      </div>
      <div class="text-end">
        <button
          type="submit"
          class="btn btn-outline-primary"
          :class="{ disabled: cart.carts.length === 0 }"
        >
          送出訂單
        </button>
      </div>
    </Form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loadingStatus: {
        loadingItemId: '',
      },
      cart: {
        carts: {},
      },
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: '',
        },
        message: '',
      },
      isLoading: true,
    };
  },
  methods: {
    getCartList() {
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/cart`;
      this.$http
        .get(url)
        .then((res) => {
          if (res.data.success) {
            this.cart = res.data.data;
            this.isLoading = false;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    deleteAllCarts() {
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/cart`;
      this.$http.delete(url)
        .then((res) => {
          if (res.data.success) {
            console.log(`${res.data.message}所有購物車品項`);
            this.getCartList();
          } else {
            console.log(res.data.message);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    removeCartItem(itemId, itemTitle) {
      this.loadingStatus.loadingItemId = itemId;
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/cart/${itemId}`;
      this.$http
        .delete(url)
        .then((res) => {
          if (res.data.success) {
            console.log(itemTitle + res.data.message);
            this.getCartList();
            this.loadingStatus.loadingItemId = '';
          } else {
            console.log(res.data.message);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    isPhone(value) {
      const phoneNumber = /^(09)[0-9]{8}$/;
      return phoneNumber.test(value) ? true : '請輸入正確電話號碼';
    },
    createOrder() {
      const url = `${process.env.VUE_APP_URL}api/${process.env.VUE_APP_PATH}/order`;
      this.$http
        .post(url, { data: this.form })
        .then((res) => {
          if (res.data.success) {
            console.log(res.data.message);
            this.getCartList();
            // 清空表單，送出表單後不會再被觸發驗證
            this.$refs.form.resetForm();
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
    this.getCartList();
  },
};
</script>
