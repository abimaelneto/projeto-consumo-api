<script>
import City from "../components/City.vue";

export default {
  components: {
    City,
  },
  data() {
    return {
      cidades: [],
      filteredCidades: [],
      filtro: "",
    };
  },
  methods: {
    getIbgeData() {
      fetch("https://servicodados.ibge.gov.br/api/v1/localidades/municipios")
        .then((res) => res.json())
        .then((data) => {
          this.cidades = data.map((city) => ({
            nome: city.nome,
            estado: city.microrregiao.mesorregiao.UF.nome,
            UF: city.microrregiao.mesorregiao.UF.sigla,
          }));
        })
        .catch((err) => {
          console.log("error: ", err);
        });
    },
  },
  computed: {
    filteredCidades() {
      if (this.filtro.length <= 2) return [];

      return this.cidades.filter((c) => {
        return c.nome.toLowerCase().includes(this.filtro.toLowerCase());
      });
    },
  },
  mounted() {
    this.getIbgeData();
  },
};
</script>

<template>
  <div class="container">
    <div id="imagens">
      <img id="logo" src="../logos/attempt-icon.png" />
      <img id="text" src="../logos/attempt-text.png" />
    </div>

    <input
      type="text"
      v-model="filtro"
      placeholder="Busque por uma cidade do Brasil..."
    />

    <br />

    <div class="boxFilter" v-show="filteredCidades.length">
      <div v-for="city of filteredCidades" :key="city">
        <RouterLink :to="`/detalhe/${city.nome}`">
          <City :city="city" />
        </RouterLink>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  text-align: center;

  background: linear-gradient(360deg, #002d72 0%, #0055d9 100%);

  width: 100%;
  height: 100%;

  padding-top: 100px;
}

#imagens {
  display: flex;
  flex-direction: row;
  align-items: center;
  text-align: center;
  justify-content: center;

  width: 100%;
  height: 25%;

  padding-bottom: 100px;
}

img#logo,
img#text {
  width: auto;
  height: 100%;
}

input {
  height: 20px;
  width: 37%;
  min-width: 400px;
  border: none;
  padding: 20px;
  background-color: rgb(238, 238, 238);

  border-radius: 5px;
}

input:focus {
  box-shadow: 0 0 0 1px rgba(214, 214, 214, 0.562);
  outline: 0;
}

.boxFilter {
  height: 25vh;
  overflow: auto;
  cursor: pointer;
}

/* NAV BAR PERSONALIZADA */

.boxFilter::-webkit-scrollbar {
  width: 4px;
}

.boxFilter::-webkit-scrollbar-track {
  background: #88888800;
  border-radius: 6px;
}

.boxFilter::-webkit-scrollbar-thumb {
  background: #ffffff;
  border-radius: 6px;
}
</style>
