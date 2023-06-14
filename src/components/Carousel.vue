<script>
import ClimaBox from "@/components/climaBox.vue";
export default {
  components: {
    ClimaBox,
  },
  props: {
    items: Array,
  },
  data() {
    return {
      index: 0,
    };
  },
  methods: {
    updateIndex(action) {
      if (this.index == 0 && action == "prev") return;
      if (this.index == 6 && action == "next") return;
      this.index = action == "next" ? this.index + 1 : this.index - 1;
    },
  },
  computed: {
    activeItems() {
      return this.items.slice(this.index, this.index + 4);
    },
  },
};
</script>

<template>
  {{ index }}
  <div class="climaBox">
    <div @click="updateIndex('prev')" class="arrow">
      <div class="centerArrow">&lt</div>
    </div>
    <div v-for="x of activeItems" :key="x">
      <ClimaBox :clima="x" />
    </div>
    <div @click="updateIndex('next')" class="arrow">
      <div class="centerArrow">></div>
    </div>
  </div>
</template>

<style scoped>
/* CLIMA */
.climaBox {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  margin: auto;

  width: 90vw;

  cursor: pointer;
}

.arrow {
  display: flex;
  align-items: center;
  justify-content: center;

  margin: 10px;
  width: 50px;
}

.centerArrow {
  box-shadow: 8px 8px 24px #c5c5c5, -8px -8px 24px #fbfbfb;
  background: rgb(255, 255, 255, 0.2);

  border-radius: 10px;
  padding: 10px;

  font-weight: 700;
  font-size: 30px;
  transition: 0.2s;

  user-select: none;
}

.centerArrow:hover {
  background-color: rgb(245, 245, 245);
}
</style>
