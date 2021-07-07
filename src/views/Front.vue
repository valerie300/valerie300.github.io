<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <router-link class="nav-link" to="/">首頁</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/products">前台產品列表</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/carts">前台購物車列表</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/dashboard">後台台購物車列表</router-link>
          </li>
        </ul>
      </div>
    </div>
  </nav>
  首頁
  <router-view></router-view>
</template>
