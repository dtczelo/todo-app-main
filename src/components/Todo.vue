<template>
  <div class="todo" @mouseover="hovered = true" @mouseleave="hovered = false">
    <div class="check-container" @click.prevent="checkTodo">
      <div class="icon-check" v-if="isChecked">
        <img src="../assets/images/icon-check.svg" alt="Check icon" />
      </div>
    </div>
    <p class="todo__text" :class="{ check: isChecked }">{{ message }}</p>
    <div class="cross-container">
      <div @click.prevent="deleteTodo()">
        <img
          v-if="hovered"
          src="../assets/images/icon-cross.svg"
          alt="Cross icon"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    message: String,
    isChecked: Boolean,
  },
  data() {
    return {
      hovered: false,
    };
  },
  methods: {
    mouseOver() {
      this.hovered = !this.hovered;
    },
    checkTodo() {
      this.$emit("check-todo", { id: this.id });
    },
    deleteTodo() {
      this.$emit("delete-todo", { id: this.id });
    },
  },
};
</script>

<style lang="scss">
.todo {
  display: flex;
  align-items: center;
  justify-content: space-between;
  &__text {
    flex: 1;
    justify-self: start;
  }
}

.check {
  color: var(--text-darker);
  text-decoration: line-through;
}
</style>