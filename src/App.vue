<template>
  <nav  class="breadcrumb">
    <ul>
      <li><router-link class="navbar-item" to="/">Home</router-link></li>
      <li><router-link class="navbar-item" to="/about">About</router-link></li>
    </ul>
  </nav>
  <h1 class="is-size-1">CoWin Fetch</h1>
  <h5> App to fetch information from CoWin site regarding vaccination availability</h5>
  <router-view/>
</template>

