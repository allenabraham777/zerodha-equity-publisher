<template>
  <section class="container">
    <nav class="navbar px-4 navbar-dark bg-primary fixed-top">
      <span class="navbar-brand">
        Bhavcopy
      </span>
      <a
        href="https://github.com/allenabraham777/zerodha-equity-publisher"
        class="btn btn-outline-light ml-auto"
        ><i class="fa fa-github" aria-hidden="true"></i
      ></a>
    </nav>
    <div class="mt-5 pt-5">
      <h4 v-if="lastUpdated" class="text-center text-decoration-underline">
        Bhavcopy for the date {{ lastUpdated }}
      </h4>
      <div
        class="row w-100 mt-5 p-2 border border-outlined"
        style="--bs-gutter-x: 0"
      >
        <form class="col-md-4 order-md-2" @submit="search">
          <div class="input-group">
            <input
              class="form-control"
              style="margin-right: 0.3rem"
              placeholder="Search by company name"
              @change="changeName"
            />
            <div class="input-group-append">
              <button class="input-group-text btn btn-success">
                <i class="fa fa-search" aria-hidden="true"></i>
              </button>
            </div>
          </div>
        </form>
        <div v-if="equities && equities.length" class="col-md-8 order-md-1 pt-2 text-center text-md-start pt-md-0">
          <a
            :href="'/api/equities/export/' + name"
            class="btn btn-outline-dark"
            >Export as CSV <i class="fa fa-download"></i
          ></a>
        </div>
      </div>
      <div v-if="loading" class="text-center py-3">
        <div
          class="spinner-grow text-primary"
          role="status"
        >
          <span class="sr-only">Loading...</span>
        </div>
      </div>
      <Body v-bind:equities="equities" />
    </div>
    <div class="text-center text-secondary">
      <p>
        *Data updates on every business day at 18:00 PM IST (UTC +05:30)
      </p>
    </div>
  </section>
</template>

<script>
import Body from "./components/Body.vue";
import { fetchAllEquities, fetchEquitiesByName } from "./utils/api";

export default {
  name: "App",
  components: {
    Body,
  },
  data() {
    return {
      loading: true,
      equities: [],
      lastUpdated: "",
      name: "",
    };
  },
  methods: {
    changeName({ target: { value } }) {
      this.name = value;
      if (!value) this.loadEquities();
    },
    async search(e) {
      e.preventDefault();
      this.loading = true;
      this.equities = await fetchEquitiesByName(this.name);
      this.loading = false;
    },
    async loadEquities() {
      this.loading = true;
      const { equities, lastUpdated } = await fetchAllEquities();
      this.equities = equities;
      this.lastUpdated = lastUpdated;
      this.loading = false;
    },
  },
  mounted() {
    this.loadEquities();
  },
};
</script>

<style></style>
