<template>
  <div class="container-fluid">
    <div class="search-wrapper">
      <!-- the search bar form -->
      <form v-on:submit="getfilteredData">
        <div class="form-row">
          <div class="col-10">
            <input
              type="text"
              class="form-control"
              placeholder="Enter key word  ..."
              v-model="search"
              v-on:keyup="getfilteredData"
            />
          </div>
          <div class="col-2">
            <button type="submit" class="btn btn-primary">
              <i class="fa fa-search"></i>
            </button>
          </div>
        </div>
      </form>
      <!-- check boxes -->
      <div id="checkboxes">
        <div v-for="(stack,index) in stacks" :key="index" class="form-check form-check-inline">
          <input
            class="form-check-input"
            type="checkbox"
            v-model="stack.checked"
            v-on:change="getfilteredData"
          />
          <label class="form-check-label">{{ stack.value }}</label>
        </div>
      </div>
    </div>
    <!-- end of checkboxes -->
    <div class="card-columns">
      <!-- iterate data -->
      <template v-for="(item, index) in filteredData">
        <router-link :to="'/item/' + sanitize(item.name)" :key="index">
          <item-card :key="index" :item="item"></item-card>
        </router-link>
      </template>
    </div>
  </div>
</template>

<script>
import ItemCard from "./ItemCard";
import data from "../data/data";
import axios from "axios";

export default {
  name: "SearchPage",
  components: {
    "item-card": ItemCard,
  },
  computed: {
    selectedFilters: function () {
      let filters = [];
      let checkedFiters = this.stacks.filter((obj) => obj.checked);
      checkedFiters.forEach((element) => {
        filters.push(element.value);
      });
      return filters;
    },
  },
  data() {
    return {
      filteredData: [],
      search: "",
      stacks: [
        {
          checked: false,
          value: "language",
        },
        {
          checked: false,
          value: "framework",
        },
        {
          checked: false,
          value: "frontend",
        },
        {
          checked: false,
          value: "backend",
        },
        {
          checked: false,
          value: "mobile",
        },
        {
          checked: false,
          value: "web",
        },
        {
          checked: false,
          value: "hybrid",
        },
        {
          checked: false,
          value: "database",
        },
      ],
    };
  },
  methods: {
    sanitize(s) {
      return s.replace(/ /g, "-");
    },
    getfilteredData: function () {
      axios
        .get(
          "https://script.googleusercontent.com/macros/echo?user_content_key=briQcodA25YfLVx5vsKDTpxYx_3ZYrZiFNXE5GJjUVv_oeLmoQqbhVRE0C_KE9WWzHOHEv1uLvswR5uVfWIMpGu74wsoapqjm5_BxDlH2jW0nuo2oDemN9CCS2h10ox_1xSncGQajx_ryfhECjZEnH0vjQl1-jgfGZbjT0VYeYa3MNlIJgNW2Qa1Slj8RIywf2qrawQ6CEG4jSK1s25H0ihyzviGHYbc8Au2FqX70iIv_m_BXVyzGQ&lib=M91U84dyM7oxh4i0atN4adVVeyfhHcqFG"
        )
        .then(function (response) {
          // handle success
          console.log(response);
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
      this.filteredData = data;
      let filteredDataByfilters = [];
      let filteredDataBySearch = [];
      // first check if filters where selected
      if (this.selectedFilters.length > 0) {
        filteredDataByfilters = this.filteredData.filter((obj) =>
          this.selectedFilters.every((val) => obj.stack.indexOf(val) >= 0)
        );
        this.filteredData = filteredDataByfilters;
      }
      // then filter according to keyword, for now this only affects the name attribute of each data
      if (this.search !== "") {
        filteredDataBySearch = this.filteredData.filter(
          (obj) => obj.name.indexOf(this.search.toLowerCase()) >= 0
        );
        this.filteredData = filteredDataBySearch;
      }
    },
  },
  mounted() {
    this.getfilteredData();
  },
};
</script>
