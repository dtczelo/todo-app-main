<template>
  <form class="form">
    <div class="check-container"></div>
    <input
      class="form__input"
      type="text"
      placeholder="Create a new todo..."
      v-model="newTodo"
      @keydown.enter.prevent="addTodo"
    />
    <div class="cross-container">
      <div class="icon-cross" @click.prevent="newTodo = ''">
        <img
          v-if="newTodo"
          src="../assets/images/icon-cross.svg"
          alt="Cross icon"
        />
      </div>
    </div>
  </form>
</template>

<script>
export default {
  data() {
    return {
      newTodo: "",
    };
  },
  methods: {
    addTodo() {
      this.$emit("add-todo", this.newTodo);
      this.newTodo = "";
    },
  },
};
</script>

<style lang="scss">
.form {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 1rem 0;
  background-color: var(--el-background);
  border-radius: 5px;
  box-shadow: 0px 20px 40px 0px hsla(235, 19%, 35%, 0.082);
  &__input {
    flex: 1;
    border: none;
    outline: none;
    color: var(--text);
    background: transparent;
    padding: 1rem 0;
    &::placeholder {
      color: var(--text-darker);
    }
  }
}
</style>