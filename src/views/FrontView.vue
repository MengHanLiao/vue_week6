<template>
  <div>
    <nav class="navbar navbar-expand-md navbar-light bg-light">
      <div class="container-fluid flex flex-md-column">
        <router-link class="navbar-brand" to="/">前台</router-link>
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
            <router-link class="nav-link" to="/products">產品列表</router-link>
            <router-link class="nav-link" to="/cart">購物清單</router-link>
            <router-link class="nav-link" to="/admin/products">前往後臺</router-link>
          </div>
        </div>
      </div>
    </nav>
    <router-view />
  </div>
</template>
