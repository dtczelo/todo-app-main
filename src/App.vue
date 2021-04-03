<template>
  <div id="app" :data-theme="darkLightMode">
    <picture class="bg-img-container">
      <source
        media="(min-width: 570px)"
        srcset="./assets/images/bg-desktop-light.jpg"
      />
      <img
        src="./assets/images/bg-mobile-light.jpg"
        alt="Mountains with purple to blue gradient"
      />
    </picture>

    <main class="main-wrapper">
      <header class="header">
        <h1 class="header__title">todo</h1>
        <button class="header__button" @click.prevent="darkModeSwitch">
          <img v-if="!darkMode" src=./assets/images/icon-moon.svg alt="Moon
          icon" /> <img v-else src=./assets/images/icon-sun.svg alt="Sun icon"
          />
        </button>
      </header>

      <Form @add-todo="addTodo"></Form>

      <section v-if="items.length > 0" class="todo-list">
        <div class="todo-list__container">
          <draggable v-model="items" ghost-class="ghost" @end="onEnd">
            <transition-group name="flip-list">
              <Todo
                class="todo-list__item"
                v-for="item in items"
                :key="item.id"
                :id="item.id"
                :message="item.message"
                :isChecked="item.isChecked"
                @delete-todo="deleteTodo"
                @check-todo="checkTodo"
              >
              </Todo>
            </transition-group>
          </draggable>
        </div>
        <div class="todo-list__bottom">
          <p>
            {{ items.filter((item) => item.isChecked === false).length }} items
            left
          </p>
          <Navigation v-if="!mobile" @sort-todo="sortTodo"></Navigation>
          <a class="todo-list__bottom__button" @click.prevent="deleteCompleted">
            Clear Completed
          </a>
        </div>
      </section>

      <Navigation v-if="mobile" @sort-todo="sortTodo"></Navigation>
      <footer v-if="items.length > 0" class="footer">
        <p>Drag and drop to reorder list</p>
      </footer>
    </main>
    <div
      :class="{ signatureActive: isSignatureActive }"
      class="signature"
      @click.prevent="toggleSignature"
    >
      <p class="signature__attribution">
        Challenge by
        <a href="https://www.frontendmentor.io?ref=challenge" target="_blank"
          >Frontend Mentor</a
        >. Coded by <a href="https://github.com/dtczelo">Zelô</a>.
      </p>
    </div>
  </div>
</template>

<script>
// Persistent drag and drop

import Todo from "./components/Todo";
import Form from "./components/Form";
import Navigation from "./components/Navigation";

export default {
  name: "App",
  components: {
    Todo,
    Form,
    Navigation,
  },
  data() {
    return {
      mobile: false,
      darkMode: false,
      items: [],
      oldIndex: "",
      newIndex: "",
      isSignatureActive: false,
    };
  },
  computed: {
    darkLightMode: function () {
      return this.darkMode === false ? "light" : "dark";
    },
  },
  methods: {
    isMobileScreen(e) {
      let m = e.target.innerWidth < 768 ? true : false;
      this.mobile = m;
    },
    darkModeSwitch() {
      this.darkMode = !this.darkMode;
    },
    uniqueId() {
      return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
        /[xy]/g,
        function (c) {
          var r = (Math.random() * 16) | 0,
            v = c == "x" ? r : (r & 0x3) | 0x8;
          return v.toString(16);
        }
      );
    },
    onEnd(e) {
      this.oldIndex = e.oldIndex;
      this.newIndex = e.newIndex;
    },
    addTodo(newTodo) {
      if (newTodo) {
        if (localStorage.getItem("localItems") === null)
          localStorage.setItem("localItems", "[]");
        let newArray = JSON.parse(localStorage.getItem("localItems"));
        newArray.push({
          id: this.uniqueId(),
          message: newTodo,
          isChecked: false,
        });
        localStorage.removeItem("localItems");
        localStorage.setItem("localItems", JSON.stringify(newArray));
        this.items = newArray;
      }
    },
    checkTodo(payload) {
      let newArray = this.items;
      newArray.map((item) => {
        if (item.id == payload.id) item.isChecked = !item.isChecked;
      });
      localStorage.removeItem("localItems");
      localStorage.setItem("localItems", JSON.stringify(newArray));
      this.items = newArray;
    },
    deleteTodo(payload) {
      let newArray = this.items.filter((item) => item.id !== payload.id);
      localStorage.removeItem("localItems");
      localStorage.setItem("localItems", JSON.stringify(newArray));
      this.items = newArray;
    },
    deleteCompleted() {
      let isSomeItemChecked = this.items.find(
        (item) => item.isChecked === true
      );
      if (isSomeItemChecked) {
        let newArray = this.items.filter((item) => item.isChecked === false);
        localStorage.removeItem("localItems");
        localStorage.setItem("localItems", JSON.stringify(newArray));
        this.items = newArray;
      }
    },
    sortTodo(payload) {
      this.items = payload.newSort;
    },
    toggleSignature() {
      this.isSignatureActive = !this.isSignatureActive;
    },
  },
  created() {
    window.addEventListener("resize", this.isMobileScreen);
  },
  beforeMount() {
    if (localStorage.getItem("localItems") === null)
      localStorage.setItem(
        "localItems",
        JSON.stringify([
          {
            id: "c2e83e6b-13ba-4db0-9103-de3d93100f1a",
            message: "Keep training",
            isChecked: false,
          },
          {
            id: "da360cd0-6387-4d9b-b8a6-5c58fc264f4a",
            message: "Compliment yourself each day",
            isChecked: false,
          },
          {
            id: "4f662989-928e-4dac-8d9e-256e0cb6ba93",
            message: "Meditate 1 hour",
            isChecked: false,
          },
          {
            id: "7c05c4bf-84ee-428c-980b-8697db3dd7d6",
            message: "Complete Todo App on Frontend Mentor",
            isChecked: true,
          },
        ])
      );
    this.items = JSON.parse(localStorage.getItem("localItems"));
  },
};
</script>

<style lang="scss">
// :root {
//   color-scheme: light dark;
// }

* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

html {
  font-family: "Josefin Sans", sans-serif;
  font-size: 18px;
}

#app[data-theme="light"] {
  // Light Theme
  --primary: hsl(220, 98%, 61%);
  --background: hsl(0, 0%, 98%);
  --el-background: #fff;
  --text: hsl(235, 24%, 19%);
  --hover: hsl(235, 19%, 35%);
  --text-darker: hsl(236, 9%, 61%);
  --line: hsl(236, 33%, 92%);
  --check-box: hsl(233, 11%, 84%);
  --signature-bg: #1f0902;
  --signature-text: #f25a29;
}

#app[data-theme="dark"] {
  // Dark Theme
  --primary: hsl(220, 98%, 61%);
  --background: hsl(235, 21%, 11%);
  --el-background: hsl(235, 24%, 19%);
  --text: hsl(234, 39%, 85%);
  --hover: hsl(236, 33%, 92%);
  --text-darker: hsl(234, 11%, 52%);
  --line: hsl(233, 14%, 35%);
  --check-box: hsl(237, 14%, 26%);
  --signature-bg: #f25a29;
  --signature-text: #1f0902;
}

#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  position: relative;
  width: 100%;
  min-height: 100vh;
  color: var(--text);
  background-color: var(--background);
}

a:hover {
  color: var(--hover);
}

.active,
a:hover.active {
  color: var(--primary);
}

.bg-img-container {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  & img {
    width: 100%;
    object-fit: cover;
  }
}

.main-wrapper {
  position: relative;
  width: min(90%, 550px);
  margin: 0 auto;
  padding: 2rem 0;
  z-index: 1;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-height: 10vh;
  color: #fff;
  &__title {
    font-size: clamp(1.5rem, -0.875rem + 9.375vw, 2.3rem);
    letter-spacing: 1rem;
    text-transform: uppercase;
  }
  &__button {
    outline: none;
    border: none;
    background: transparent;
  }
}

.todo-list {
  box-shadow: 0px 20px 40px 0px hsla(235, 19%, 35%, 0.157);
  border-radius: 5px;
  overflow: hidden;
  &__item {
    background-color: var(--el-background);
    padding: 0.5rem 0;
    min-height: 60px;
    border-bottom: 2px solid var(--line);
    cursor: move;
  }
  &__bottom {
    display: flex;
    justify-content: space-between;
    padding: 1rem 1rem;
    color: var(--text-darker);
    background-color: var(--el-background);
    & > * {
      font-size: 0.8rem;
    }
    &__button {
      cursor: pointer;
      &:hover {
        color: var(--hover);
      }
    }
  }
}

.check-container {
  width: 1.2rem;
  height: 1.2rem;
  margin: 0 1rem;
  border-radius: 50%;
  border: 1px solid var(--check-box);
  overflow: hidden;
  cursor: pointer;
}

.icon-check {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(150deg, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

.cross-container {
  width: 1.2rem;
  height: 1.2rem;
  margin: 0 1rem;
  cursor: pointer;
}

.footer {
  height: 20vh;
  display: grid;
  place-items: center;
  & p {
    color: var(--text-darker);
  }
}

// Drag & drop animation
.flip-list-move {
  transition: transform 0.5s;
}

.ghost {
  border-left: 6px solid #ac6af0;
}

.signature {
  position: fixed;
  bottom: 5%;
  right: 5%;
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: var(--signature-bg);
  color: var(--signature-text);
  font-weight: 700;
  border-radius: 50%;
  text-align: center;
  box-shadow: 0 2px 20px -8px #1f0902a8;
  opacity: 0.7;
  transition: background-color 0.4s ease, opacity 0.2s ease,
    box-shadow 0.4s ease, height 0.4s ease, border-radius 0.4s ease,
    width 0.4s ease;
  cursor: pointer;
  &:hover {
    box-shadow: 0 10px 40px -8px #1f0902a6;
    opacity: 1;
  }
  &::before {
    content: "Z";
    position: absolute;
    top: 50%;
    left: 50%;
    font-size: 1rem;
    transform: translate(-50%, -50%);
    opacity: 1;
    transition: opacity 0.5s ease-in;
  }
  &__attribution {
    line-height: 1.5;
    color: #1f0902;
    text-decoration: none;
    transform: scale(0);
    opacity: 0;
    transition: all 0.5s ease;
  }
}

.signatureActive {
  width: 240px;
  height: 70px;
  border-radius: 0;
  opacity: 1;
  background-color: #f25a29;
  & .signature__attribution {
    transform: scale(1);
    opacity: 1;
  }
  &.signature::before {
    opacity: 0;
  }
}
</style>
