<template>
  <div>
    <div class="row">
      <div class="filter">
        <form @submit.prevent="onFilterApply">
          <input
            placeholder="Filter by Name"
            class="form-control mr-2"
            type="text"
            id="name-filter"
            v-on:input="onFilterApply"
            v-model="filterName"
          />
          <input
            placeholder="Filter by Family"
            class="form-control mr-2"
            type="text"
            id="family-filter"
            v-on:input="onFilterApply"
            v-model="filterFamily"
          />
        </form>
      </div>
    </div>
    <div class="row">
      <div class="list-control">
        <div
          style="display: flex; flex-direction: row; vertical-align: middle; align-items: center;"
        >
          <span>Shows:</span>
          <select
            class="form-control"
            v-model="pageSize"
            @change="handlePageSizeChange($event)"
          >
            <option v-for="size in pageSizes" :key="size" :value="size">
              {{ size }}
            </option>
          </select>
        </div>
        <b-pagination
          v-model="page"
          :total-rows="getTotalItems"
          :per-page="pageSize"
          prev-text="<"
          next-text=">"
          @change="handlePageChange"
          variant="success"
        ></b-pagination>
      </div>
    </div>
    <div>
      <b-table-simple hover caption-top>
        <caption
          style="font-size: xx-large; text-align: center; font-family: Arial, Helvetica, sans-serif;"
        >
          Fruit
        </caption>
        <b-thead head-variant="dark" style="text-align: center;">
          <b-tr>
            <b-th>Name</b-th>
            <b-th>Family</b-th>
            <b-th>Protain</b-th>
            <b-th>Suger</b-th>
            <b-th>Carbohydrates</b-th>
            <b-th>Fats</b-th>
            <b-th>Calories</b-th>
            <b-th>Favourite</b-th>
          </b-tr>
        </b-thead>
        <b-tbody style="text-align: center;">
          <b-tr v-for="fruit in getFruit" :key="fruit.id">
            <b-td>{{ fruit.name }}</b-td>
            <b-td>{{ fruit.family }}</b-td>
            <b-td>{{ fruit.protein }}</b-td>
            <b-td>{{ fruit.sugar }}</b-td>
            <b-td>{{ fruit.carbohydrates }}</b-td>
            <b-td>{{ fruit.fat }}</b-td>
            <b-td>{{ fruit.calories }}</b-td>
            <b-button @click="addToFavorites(fruit.id)" variant="success"
              >Add</b-button
            >
          </b-tr>
        </b-tbody>
      </b-table-simple>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";

export default {
  name: "fruit-list",
  data() {
    return {
      filterName: "",
      filterFamily: "",
      pageSize: 10,
      pageSizes: [10, 20, 50],
      currentIndex: -1,
      page: 1,
    };
  },
  methods: {
    ...mapActions(["fetchFruit", "addToFavorites"]),

    getRequestParams(page, pageSize, filterName, filterFamily) {
      let params = {};

      if (page) {
        params["page"] = page;
      }

      if (pageSize) {
        params["size"] = pageSize;
      }

      if (filterName) {
        params["name"] = filterName;
      }

      if (filterFamily) {
        params["family"] = filterFamily;
      }

      return params;
    },

    retrieveFruit() {
      const params = this.getRequestParams(
        this.page,
        this.pageSize,
        this.filterName,
        this.filterFamily
      );
      this.fetchFruit(params);
    },

    handlePageChange(value) {
      this.page = value;
      this.retrieveFruit();
    },

    handlePageSizeChange(event) {
      this.pageSize = event.target.value;
      this.page = 1;
      this.retrieveFruit();
    },

    addToFavorites(id) {
      this.$store.dispatch("addToFavorites", id);
      alert("Successfully added");
    },

    onFilterApply() {
      this.retrieveFruit();
      // this.getTotalItems = this.retrieveFruit().size;
    },
  },

  computed: {
    ...mapGetters(["getFruit", "getTotalItems", "getFavorites"]),
  },
  created() {
    this.retrieveFruit();
  },
};
</script>
<style>
.filter form {
  display: flex;
  flex-direction: row;
  box-sizing: border-box;
  padding: 20px;
  width: 100%;
}

.list-control {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  box-sizing: border-box;
  padding: 20px;
  width: 100%;
}

.fruit-list {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  padding: 0;
}
.page-item.active .page-link {
  background-color: green;
  border-color: green;
}
.page-link:hover {
  color: green;
}
.page-link {
  color: green;
}
.fruit-item {
  width: 30%;
  list-style: none;
  margin: 5px;
  align-items: center;
  justify-content: center !important;
}
</style>
