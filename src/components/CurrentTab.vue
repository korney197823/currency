<template>
  <v-container class="container">
    <header class="header">
      <section v-if="errored">
        <p>Errored loading data</p>
      </section>
      <section v-if="loading">
        <p>Loading ...</p>
      </section>
      <h3>Курс {{ $data.base }} на сегодня</h3>
      <v-tabs v-model="tab" show-arrows>
        <v-tab v-for="item in $data.result" :key="item[0]">
          {{ item[0] }}
        </v-tab>
      </v-tabs>
    </header>
    <v-tabs-items v-model="tab">
      <v-tab-item v-for="item in $data.result" :key="item[0]">
        <v-card v-for="item in $data.result" :key="item[0]">
          <v-card-text>{{ item[1] }}</v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
  </v-container>
</template>

<script>
export default {
  name: "CurrentTab",
  data() {
    return {
      count: null,
      tab: 30,
      errored: false,
      loading: true,
      base: "",
      result: []
    };
  },
  mounted() {
    const proxyUrl = `https://cors-anywhere.herokuapp.com/`;
    const targetUrl = `http://api.openrates.io/latest`;
    fetch(proxyUrl + targetUrl)
      .then(response => response.json())
      .then(data => {
        console.log(data)
        this.result = Object.entries(data.rates);
        this.base = data.base;
        if (data.base === "EUR") {
          this.result.push(["EUR", 1]);
          this.tab = this.result.length - 1;
        }
      })
      .catch(error => {
        console.log(error);
        this.errored = true;
      })
      .finally(() => (this.loading = false));
  }
};
</script>

<style scoped>
.container {
  padding-top: 0;
}
@media (min-width: 720px) {
  .container {
    max-width: 720px;
  }
}
</style>
