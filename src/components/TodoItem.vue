<template>
    <li class="task">
        <input type="checkbox" v-model="todo.done" @change="$emit('updateTodo')">
        <span v-if="!isEditing" @click="startEditing">{{ todo.title }}</span>
        <input v-else v-model="editTitle" @keydown.enter="saveEdit" @blur="saveEdit">
        <button class="btn delete" @click="$emit('deleteTodo', todo.id)">Delete</button>
    </li>
</template>

<script>
export default {
    props: {
        todo: Object
    },
    data() {
        return {
            isEditing: false,
            editTitle: this.todo.title
        };
    },
    methods: {
        startEditing() {
            this.isEditing = true;
            this.editTitle = this.todo.title;
        },
        saveEdit() {
            if (this.editTitle.trim()) {
                this.todo.title = this.editTitle.trim();
                this.isEditing = false;
                this.$emit('updateTodo');
            } else {
                this.$emit('showAlert');
            }
        }
    }
};
</script>

<style scoped>
.task {
    background-color: #1C1C1E;
    padding: 15px;
    margin: 10px 0;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    transition: all 300ms ease;
    border: 1px solid #2C2C2E;
}

.task:hover {
    background-color: #2C2C2E;
    transform: scale(1.01);
}

.task input[type="checkbox"] {
    margin-right: 10px;
}

.task span {
    flex-grow: 1;
    cursor: pointer;
}

.task input[type="text"] {
    flex-grow: 1;
    background-color: #2C2C2E;
    color: #FFFFFF;
    border: none;
    padding: 5px;
    border-radius: 4px;
}

.btn.delete {
    background-color: #FF3B30;
    margin-left: 0.5em;
}

.btn.delete:hover {
    background-color: #FF2D55;
}

@media (max-width: 640px) {
    .task {
        flex-direction: column;
        align-items: flex-start;
        gap: 10px;
    }
    .btn.delete {
        width: auto;
    }
}
</style>