<template>
    <div class="navbar">
        <div class="logo">
            <img src="@/assets/logo.png" alt="Mi Logo" />
        </div>
        <div class="menu">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Menu',
    computed: {
    },
    methods: {
    }
}
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
    height: 40px; /* Ajusta la altura del logo */
    width: auto;
}

/* Menú */
.navbar .menu {
    display: flex;
    padding-right: 20px;
    transition: all 0.3s ease-in-out;
}

.navbar .menu-item {
    margin-left: 10px;
}

/* Botón de menú hamburguesa */
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
    flex-direction: column; /* Apila los elementos verticalmente */
    justify-content: center;
    }

    .logo {
    text-align: center;
    margin-bottom: 10px; /* Espacio entre el logo y el botón */
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