<template>
  <div class="home">
    <header class="hero-section">
      <h1 class="display-4 mt-3"><strong>Aplicación frontend de un blog de cursos online realizado el Vue.js 3</strong></h1>
      <h1 class="display-4 mt-3"><strong>Integrante:</strong></h1>

    </header>
    <section class="content-section container">
      <div class="row justify-content-center align-items-center">
        <!-- Integrante 1 -->
        <div class="col-md-4">
          <div class="card integrante-card shadow-sm">
            <div class="card-body text-center">
              <img src="/img/avatars/avatar.jpg" alt="Avatar" class="rounded-circle">
              <h5 class="card-title">Limbert Olmos Mercado</h5>
              <p class="card-text">Desarrollador FullStack</p>
            </div>
          </div>
        </div>
      </div>
    </section>

  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  }
}
</script>

<style>
  .home{
    text-align: center
  }
  .rounded-circle {
  width: 100px;
  height: 100px;
  object-fit: cover;
}
</style>