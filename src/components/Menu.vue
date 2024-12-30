<template>
    <div class="navbar">
        <div class="logo">
            <img src="@/assets/logo.png" alt="Mi Logo" />
        </div>
        <!-- Botón de menú hamburguesa -->
        <span class="hamburger-container">
            <button class="hamburger" @click="toggleMenu" aria-label="Toggle Menu">
                <span></span>
                <span></span>
                <span></span>
            </button>
        </span>
        <!-- Menú desplegable -->
        <div class="menu" :class="{ active: isMenuOpen || isDesktop }">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Menu',
    data() {
        return {
            isMenuOpen: false, // Menú cerrado por defecto
            isDesktop: window.innerWidth > 768, // Detecta si es escritorio o móvil
        };
    },
    methods: {
        toggleMenu() {
            this.isMenuOpen = !this.isMenuOpen; // Alterna el estado del menú
        },
        handleResize() {
            this.isDesktop = window.innerWidth > 768; // Actualiza el estado en base al tamaño de la ventana
            if (this.isDesktop) {
                this.isMenuOpen = false; // Asegura que el menú no quede abierto innecesariamente
            }
        },
    },
    mounted() {
        window.addEventListener('resize', this.handleResize); // Escucha cambios en el tamaño de la ventana
    },
    beforeUnmount() {
        window.removeEventListener('resize', this.handleResize); // Limpia el listener al desmontar el componente
    },
};
</script>

<style>
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: #333;
    padding: 10px 20px;
    color: white;
    position: fixed;
    width: 100%;
    top: 0;
    left: 0;
    z-index: 1000;
    margin-bottom: 20px;
}

.navbar a {
    color: white;
    text-decoration: none;
    padding: 14px 20px;
    display: block;
}

.navbar a:hover {
    background-color: #575757;
    border-radius: 4px;
}

.navbar .logo {
    font-size: 1.5em;
    font-weight: bold;
}

.navbar .logo img {
    height: 40px;
    width: auto;
}

/* Estilos del menú */
.navbar .menu {
    display: none; /* Oculto por defecto */
    flex-direction: row; /* Horizontal por defecto */
    padding-right: 20px;
    transition: all 0.3s ease-in-out;
}

.navbar .menu-item {
    margin-left: 10px;
}

/* Menú activo en móviles o visible en escritorio */
.navbar .menu.active {
    display: flex;
}

/* Estilos para el contenedor del botón hamburguesa */
.hamburger-container {
  position: absolute;
  top: 10px; /* Ajusta la distancia desde el top */
  right: 20px; /* Ajusta la distancia desde la derecha */
}
/* Botón hamburguesa */
.hamburger {
    display: none;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    background: none;
    border: none;
}

.hamburger span {
    width: 25px;
    height: 3px;
    background-color: white;
    margin: 3px 0;
    transition: all 0.3s;
}

/* Responsive para pantallas pequeñas */
@media (max-width: 768px) {
    .navbar {
        /*flex-direction: column;*/
        justify-content: center;
    }

    .menu {
        display: none;
        flex-direction: column;
        position: absolute;
        top: 60px;
        right: 0;
        background-color: #333;
        width: 100%;
        text-align: center;
        padding: 10px 0;
        justify-content: center;
    }

    .menu.active {
    display: flex;
    }

    .hamburger {
    display: flex;
    }

    .menu-item {
    margin: 10px 0;
    }
}
</style>
