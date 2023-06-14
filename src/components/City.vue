<script>
export default {
  props: {
    city: Object,
  },
};
</script>

<template>
  <main class="content">
    <div class="cidade">{{ city?.nome }}</div>
    <div class="estado">{{ city?.estado }} / {{ city?.UF }}</div>
  </main>
</template>

<style scoped>
.content {
  display: flex;
  justify-content: space-between;
  align-items: center;

  width: 25vw;
  min-width: 350px;
  height: 55px;
  border-radius: 5px;
  margin: 10px;

  padding-left: 15px;
  padding-right: 15px;

  background-color: rgb(238, 238, 238) !important;
  box-shadow: 1px 1px 3px lightgray;
}

.estado {
  font-size: 1ch;
}
</style>
