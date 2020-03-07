<template>
  <div class="app" id="app">
    <div class="app__header">Каталог</div>

    <div 
      class="app__content"
      :class="shiftedClass"
    >
      <div 
        class="app-page__item"
        v-for="(item, index) in items"
        :key="item.id"
        @click="activateItem(index)"
      >{{ item.title }}</div>
    </div>
    
    <transition-group name="shift">
      <AppPage
        v-for="(item, index) in items"
        :key="item.id"
        :id="item.id"
        :title="item.title"
        v-show="index === activeItem"
        :active="index === activeItem"
        @close="deactivateItem"
      />
    </transition-group>
  </div>
</template>

<script>
import AppPage from './components/AppPage';
import getItems from './api/getItems/'

export default {
  name: 'app',
  components: {
    AppPage
  },
  data: () => ({
    items: [],
    activeItem: null
  }),
  computed: {
    activeItemId() {
      return this.items[this.activeItem].id
    },
    shiftedClass() {
      return {
        'content-shifted': typeof this.activeItem === 'number'
      }
    }
  },
  methods: {
    activateItem(i) {
      this.activeItem = i;
    },
    deactivateItem() {
      this.activeItem = null;
    }
  },
  created() {   
    this.items.push(...getItems(0));
  }
}
</script>

<style lang="scss">
html, body {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  font-size: 14px;
  color: #4f4f4f;
}

* {
  box-sizing: border-box;
}

.app {
  &__header {
    background-color: #eeeeee;
    font-weight: bold;
    padding: 20px 30px;
  }

  &__content {
    padding: 20px;
    transition: all .5s;
    background-color: #fff;
  }
}

.shift-enter-active, .shift-leave-active {
  transition: all .5s;
}

.shift-enter, .shift-leave-to {
  transform: translateX(100%) translateY(100px);
}

.content-shifted {
  transform: translateX(-100%) translateY(-100px); 
}
</style>
