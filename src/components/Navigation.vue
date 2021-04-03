<template>
  <nav class="items-selection">
    <a
      :class="{ active: displayAll }"
      class="items-selection__link items-selection__link--1"
      @click="sortTodo('all')"
      >All</a
    >
    <a
      :class="{ active: displayActive }"
      class="items-selection__link items-selection__link--2"
      @click="sortTodo('active')"
      >Active</a
    >
    <a
      :class="{ active: displayCompleted }"
      class="items-selection__link items-selection__link--3"
      @click="sortTodo('completed')"
      >Completed</a
    >
  </nav>
</template>

<script>
export default {
  data() {
    return {
      displayAll: true,
      displayActive: false,
      displayCompleted: false,
    };
  },
  methods: {
    sortTodo(sort) {
      let newArray = JSON.parse(localStorage.getItem("localItems"));
      switch (sort) {
        case "all":
          this.$emit("sort-todo", { newSort: newArray });
          this.displayAll = true;
          this.displayActive = false;
          this.displayCompleted = false;
          break;
        case "active":
          this.$emit("sort-todo", {
            newSort: newArray.filter((item) => item.isChecked === false),
          });
          this.displayAll = false;
          this.displayActive = true;
          this.displayCompleted = false;
          break;
        case "completed":
          this.$emit("sort-todo", {
            newSort: newArray.filter((item) => item.isChecked === true),
          });
          this.displayAll = false;
          this.displayActive = false;
          this.displayCompleted = true;
          break;
      }
    },
  },
};
</script>

<style lang="scss">
.items-selection {
  display: flex;
  justify-content: space-around;
  margin-top: 1rem;
  padding: 1rem 0;
  color: var(--text-darker);
  background-color: var(--el-background);
  border-radius: 5px;
  box-shadow: 0px 20px 40px 0px hsla(235, 19%, 35%, 0.082);
  &__link {
    cursor: pointer;
    &--1 {
      margin-left: auto;
    }
    &--2 {
      margin: 0 1rem;
    }
    &--3 {
      margin-right: auto;
    }
  }
}

@media (min-width: 768px) {
  .items-selection {
    margin-top: 0;
    padding: 0 0;
    box-shadow: none;
  }
}
</style>