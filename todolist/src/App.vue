<template>
    <nav class="nav-bar">
      <ul>
        <li><router-link to="/">HOME</router-link></li>
        <li><router-link to="/todos">TODOS</router-link></li>
        <li></li>
      </ul>
    </nav>
     <router-view />
</template>

<script>
    export default {

    }
</script>

<style>
   .nav-bar{border: 1px solid #ddd; width: 100%;}
   .nav-bar ul{max-width: 1024px; width: 100%; list-style: none; display: flex; margin: 0 auto;}
   .nav-bar ul li{}
   .nav-bar ul li a{text-decoration: none; padding: 10px; margin: 10px 0; display: block; font-weight: bold; color: #3c501a;}
</style>