<script>
import climaBox from "../components/climaBox.vue";
import loadingModel from "../components/loadingModal.vue";
import Carousel from "../components/Carousel.vue";

export default {
  components: {
    climaBox,
    loadingModel,
    Carousel,
  },
  data() {
    return {
      search: this.$route.params.name,
      loading: true,
      openImgModal: false,
      imagesList: [],
      clima: [],
      error: "",
      imgError: "",
      tempAtual: "",
    };
  },
  methods: {
    getImages(url) {
      if (this.imagesList != "") return;
      this.loading = true;

      // Obtem foto do Google usando a API de pesquisa de imagens
      const googleSearchApiKey = "AIzaSyBLq5cUwYO31kXPjCxhaELx_tNXv_TI-ec"; // Chave de API do Google JSON  com pesquisas de até 10 000 requisições por dia se for somente web é ilimitado
      const customSearchEngineId = "163bf32740a0e4962"; // ID de pesquisa personalizado
      const googleSearchUrl = `https://www.googleapis.com/customsearch/v1?key=${googleSearchApiKey}&cx=${customSearchEngineId}&q=cidade%20de%20${url}&searchType=image`;

      fetch(googleSearchUrl)
        .then((res) => res.json())
        .then((data) => {
          this.imagesList = data.items.slice(1, 10).map((i) => i.link);
        })
        .catch((err) => {
          this.imgError = "Algo deu errado, tente novamente mais tarde.... :(";
          console.log("error: ", err);
        })
        .finally(() => {
          this.loading = false;
        });
    },
    buildUnitString(string, unit) {
      return `${string}${unit}`;
    },
    getClima() {
      let url = `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${this.search}?unitGroup=metric&key=ZNGGPXU6288M2AFT47884B8LQ&contentType=json`;
      fetch(url)
        .then((e) => e.json())
        .then((data) => {
          this.clima = data.days.slice(1, 10).map((day, index) => ({
            index,
            data: day.datetime.split("-").reverse().join("-"),
            tempmin: this.buildUnitString(day.tempmin, "°"),
            tempmax: this.buildUnitString(day.tempmax, "°"),
            tempmedia: this.buildUnitString(day.temp, "°"),
            nascerdosol: day.sunrise,
            pordosol: day.sunset,
            humidade: `${day.humidity}${"%"}`,
            condição: day.conditions,
            descrição: day.description,
            precipitação: this.buildUnitString(day.precip, "mm"),
            icon: day.icon.replaceAll("-", ""),
          }));

          this.tempAtual = data.currentConditions.temp + "°";
        })
        .catch((err) => {
          this.climaError =
            "Algo deu errado, tente novamente mais tarde.... :(";
          console.log("erro: ", err);
        })
        .finally(() => {
          this.loading = false;
        });
    },

    showImg() {
      this.getImages();
      this.openImgModal = !this.openImgModal;
    },
  },
  mounted() {
    this.getClima();
  },
};
</script>

<template>
  <main class="content">
    <h2>{{ search }} - {{ tempAtual }}</h2>

    <p class="error" v-if="error">{{ error }}</p>

    <Carousel :items="clima" />
    <p class="fotos" @click="showImg">Veja algumas fotos do local!</p>
    <div class="caroselImg" v-show="openImgModal">
      <p class="cloneBtn" @click="openImgModal = false">X</p>
      <p class="error" v-show="imgError">{{ imgError }}</p>
      <div v-for="img of imagesList" :key="img">
        <img :src="img" alt="" />
      </div>
    </div>

    <loadingModel v-if="loading"></loadingModel>
  </main>
</template>

<style scoped>
.content {
  display: flex;
  height: 80vh;
  flex-direction: column;
  align-items: center;
}

h2 {
  font-weight: 700;
  font-size: 2rem;
  color: rgb(51, 51, 51);

  padding-top: 20px;
  padding-bottom: 20px;
}

/* IMAGENS */
.caroselImg {
  position: fixed;
  overflow: auto;

  top: 0;
  left: 0;

  z-index: 150;
  width: 100%;
  height: 100%;

  display: flex;
  flex-wrap: wrap;
  justify-content: center;

  background-color: rgba(0, 0, 0, 0.8);
}

.caroselImg img {
  height: 350px;
  border-radius: 5px;
  width: auto;
  margin: 10px;
}

.fotos {
  background-color: rgba(0, 0, 0, 0.8);
  padding: 20px;
  border-radius: 10px;
  color: white;
  font-weight: 700;

  cursor: pointer;
}

.cloneBtn {
  color: white;
  background-color: rgb(255, 255, 255, 0.5);

  display: flex;
  justify-content: center;
  align-items: center;

  position: absolute;
  margin: 20px;
  top: 0;
  right: 0;

  width: 50px;
  height: 50px;
  border-radius: 50px;
  font-weight: 700;

  transition: 0.4s;
}

.cloneBtn:hover {
  opacity: 0.7;
}

.error {
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;

  background-color: lightcoral;
  color: white;

  font-weight: 700;
  border-radius: 10px;
  width: 350px;
  height: 100px;
}
</style>
