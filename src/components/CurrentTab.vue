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
        <v-tab
          v-for="item in $data.result"
          :key="item[0]"
          @click="setCurrentCurrency(item[0])"
        >
          {{ item[0] }}
        </v-tab>
      </v-tabs>
    </header>
    <div class="input-container">
      <v-text-field v-model="count" class="text-input"></v-text-field>
      <span class="input-label">{{ $data.base }}</span>
    </div>

    <v-tabs-items v-model="tab">
      <v-tab-item v-for="item in $data.result" :key="item[0]">
        <v-card v-for="item in paginatedData" :key="item[0]">
          <v-card-text> {{ $data.count }} {{ $data.base }} = </v-card-text>
          <v-card-text>
            {{ (item[1] * $data.count).toFixed(2) }} {{ item[0] }}
          </v-card-text>
        </v-card>
      </v-tab-item>
      <div class="button-container">
        <button
          class="btn btn-pagination"
          @click="prevPage"
          :disabled="pageNumber === 0"
        >
          назад
        </button>
        <button
          class="btn btn-pagination"
          @click="nextPage"
          :disabled="pageNumber >= pageCount - 1"
        >
          далее
        </button>
      </div>
    </v-tabs-items>
  </v-container>
</template>

<script>
export default {
  name: "CurrentTab",
  data() {
    return {
      pageNumber: 0,
      sizePage: 4,
      count: 5,
      tab: 30,
      errored: false,
      loading: true,
      base: "EUR",
      result: []
    };
  },
  mounted() {
    const proxyUrl = `https://cors-anywhere.herokuapp.com/`;
    const targetUrl = `http://api.openrates.io/latest/?base=${this.$data.base}`;
    fetch(proxyUrl + targetUrl)
      .then(response => response.json())
      .then(data => {
        console.log(data);
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
  },
  computed: {
    getCurrency: function(current) {
      return this.count * current;
    },
    pageCount: function() {
      let l = this.result.length;
      let s = this.sizePage;
      return Math.ceil(l / s);
    },
    paginatedData: function() {
      const start = this.pageNumber * this.sizePage;
      const end = start + this.sizePage;
      return this.result.slice(start, end);
    }
  },
  methods: {
    setCurrentCurrency: function(currency) {
      console.log(currency);
      const proxyUrl = `https://cors-anywhere.herokuapp.com/`;
      const targetUrl = `http://api.openrates.io/latest/?base=${currency}`;
      fetch(proxyUrl + targetUrl)
        .then(response => response.json())
        .then(data => {
          console.log(data);
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
    },
    nextPage: function() {
      this.pageNumber++;
    },
    prevPage: function() {
      this.pageNumber--;
    }
  }
};
</script>

<style>
.v-main__wrap {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.v-window-item {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 18px;
}

.container.container {
  max-width: 720px;
  padding-top: 0;
}

.input-container {
  display: flex;
  width: 30%;
}
.text-input {
}
.input-label {
  align-self: center;
}
.btn {
  width: 112px;
  height: 34px;
  filter: drop-shadow(0px 4px 4px rgba(0, 0, 0, 0.25));
}
.btn:disabled,
.btn[disabled] {
  background-color: #efefef;
}
</style>
