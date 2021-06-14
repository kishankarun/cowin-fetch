<template>
  <nav  class="breadcrumb">
    <ul>
      <li><router-link class="navbar-item" to="/">Home</router-link></li>
      <li><router-link class="navbar-item" to="/about">About</router-link></li>
    </ul>
  </nav>
  <div class="is-size-3 has-text-weight-bold">CoWin Fetch</div>
  <div class="is-size-7">
    App to fetch information from CoWin site regarding vaccination availability.
    (Read details in the <router-link to="/about">About</router-link> page)
  </div>
  <p class="is-size-7 is-italic has-text-danger">* Works in most browsesrs.</p>
  <router-view/>
</template>

