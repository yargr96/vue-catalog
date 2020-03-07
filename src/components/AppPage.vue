<template>
  <div class="app-page">
    <div 
      class="app-page__wrapper"
      :class="shiftedClass"
    >
      <div class="app-page-header app-page__header">
        <div 
          @click="close"
          class="app-page-header__back"
        ></div>
        <div class="app-page-header__title">{{ title }}</div>
      </div>

      <div class="app-page__content">
        <div 
          class="app-page__item"
          v-for="(item, index) in items"
          :key="item.id"
          @click="handleItemClick(index)"
        >{{ item.title }}</div>
      </div>      
    </div>

    <transition-group name="shift">
      <app-page 
        v-for="(item, index) in items"
        :key="item.id"
        v-show="index === activeItem"
        @close="handlePageClose"
        :id="item.id"
        :title="item.title"
        :active="index === activeItem"
      />
    </transition-group>
  </div>
</template>

<script>
import getItems from '../api/getItems/'

export default {
  name: "AppPage",
  props: {
    id: String,
    title: String,
    active: Boolean
  },
  data: () => ({
    items: [],
    initialized: false,
    activeItem: null
  }),
  computed: {
    shiftedClass() {
      return {
        'content-shifted': typeof this.activeItem === 'number'
      }
    }
  },
  methods: {
    getItems() {
      const item = getItems(this.id);
      if (!item) return;

      const children = item.children;
      if (!children) return;

      this.items.push(
        ...children
      )
    },
    close() {
      this.$emit("close");
    },
    handleItemClick(index) {
      this.activeItem = index;
    },
    handlePageClose() {
      this.activeItem = null;
    }
  },
  watch: {
    active(value) {      
      if (this.initialized) return;
      if (value) {
        this.getItems();
        this.initialized = true;
      }
    }
  },
};
</script>

<style lang="scss">
.app-page-header {
  display: flex;
  align-items: center;
  height: 70px;
  background-color: #eeeeee;
  font-weight: bold;

  &__back {
    height: 100%;
    width: 40px;
    margin-right: 10px;
    background-image: url(https://css.fotosklad.ru/local/templates/fotosklad_mobile/img/catalog/arrow.svg);
    background-position: center;
    background-repeat: no-repeat;
    cursor: pointer;
  }
}

.app-page {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  &__wrapper {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: #fff;
    transition: all .5s;
  }

  &__content {
    padding: 20px;
  }

  &__item {
    background-color: #eee;
    padding: 10px;
    cursor: pointer;
    border-radius: 4px;

    &:not(:last-child) {
      margin-bottom: 10px;
    }
  }
}
</style>