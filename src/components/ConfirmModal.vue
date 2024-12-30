<template>
    <div v-if="visible" class="modal-overlay">
        <div class="modal-content">
            <h3>{{ title }}</h3>
            <p>{{ message }}</p>
            <div class="modal-actions">
                <button class="btn btn-danger" @click="confirm">Confirmar</button>
                <button class="btn btn-secondary" @click="cancel">Cancelar</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "ConfirmModal",
    props: {
        title: {
            type: String,
            default: "Confirmar acción",
        },
        message: {
            type: String,
            default: "¿Estás seguro de que deseas continuar?",
        },
        visible: {
            type: Boolean,
            required: true,
        },
    },
    emits: ["confirm", "cancel"],
    methods: {
        confirm() {
            this.$emit("confirm");
        },
        cancel() {
            this.$emit("cancel");
        },
    },
};
</script>

<style scoped>
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal-content {
    background: white;
    padding: 20px;
    border-radius: 10px;
    text-align: center;
}

.modal-actions button {
    margin: 5px;
}
</style>
