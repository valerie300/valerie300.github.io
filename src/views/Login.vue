<template>
    <div class="container mt-5">
      <Form v-slot="{ errors }" @submit.prevent="signIn" class="row justify-content-center">
        <div class="col-md-6">
        <h1 class="h3 mb-3 font-weight-normal">請先登入</h1>
        <div class="mb-2">
        <label for="email" class="sr-only">Email address</label>
        <Field
          id="email"
          name="信箱"
          type="email"
          class="form-control"
          :class="{ 'is-invalid': errors['信箱'] }"
          placeholder="請輸入 Email"
          rules="email|required"
          v-model="user.email"
          required
          autofocus
        ></Field>
        <ErrorMessage name="信箱" class="invalid-feedback"></ErrorMessage>
        </div>
        <div class="mb-2">
        <label for="password" class="sr-only">Password</label>
        <Field
          id="password"
          name="密碼"
          type="password"
          class="form-control"
          placeholder="password"
          v-model="user.password"
          required
        ></Field>
        <ErrorMessage name="密碼" class="invalid-feedback"></ErrorMessage>
        </div>
        <div class="text-end mt-4">
            <button class="btn btn-lg btn-info btn-block" @click="signIn" type="submit">
              登入
            </button>
        </div>
        </div>
      </Form>
    </div>
  <!-- <button type="button" class="btn btn-success" @click="goAdmin">
    進入後台管理
  </button> -->
</template>

<script>
export default {
  data() {
    return {
      user: {},
    };
  },
  methods: {
    signIn() {
      const url = `${process.env.VUE_APP_URL}admin/signin`;
      this.$http.post(url, this.user).then((res) => {
        if (res.data.success) {
          this.$router.push('/dashboard/admincarts');
        } else {
          console.log(res.data.message);
        }
      });
    },
  },
};
</script>
