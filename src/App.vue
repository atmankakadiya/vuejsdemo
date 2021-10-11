<template>
  <div>
    <div v-if="loading" class="loader"></div>
    <div v-else>
      <div class="dropdown">
        <div>
          <span>Country:</span>
          <select
            v-model="selectedCountry"
            @change="onChangeCountry"
            class="country_dropdown"
          >
            <option value="">Select a Country</option>
            <option
              v-for="(country, index) in listCountry"
              :value="country.country_name"
              :key="index"
            >
              {{ country.country_name }}
            </option>
          </select>
        </div>
        <div>
          <span class="state">State:</span>
          <select
            :disabled="listState.length == 0"
            v-model="selectedState"
            @change="onChangeState"
            class="state_dropdown"
          >
            <option value="">Select a State</option>
            <option
              v-for="(state, index) in listState"
              :key="index"
              :value="state.state_name"
            >
              {{ state.state_name }}
            </option>
          </select>
        </div>
        <div>
          <span class="city">City:</span>
          <select
            :disabled="listCities.length == 0"
            v-model="selectedCity"
            class="city_dropdown"
            multiple
          >
            <option value="">Select a City</option>
            <option
              v-for="(city, index) in listCities"
              :key="index"
              :value="city.city_name"
            >
              {{ city.city_name }}
            </option>
          </select>
        </div>
      </div>
      <div class="list">
        <p>Selected Country - {{ this.selectedCountry }}</p>
        <p>Selected State - {{ this.selectedState }}</p>
        <p>Selected City - {{ this.selectedCity }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      listCountry: [],
      listState: [],
      listCities: [],
      selectedCountry: "",
      selectedState: "",
      selectedCity: [],
      authToken: "",
      loading: false,
    };
  },
  created() {
    this.generateAccessToken();
  },
  methods: {
    generateAccessToken() {
      axios
        .get("https://www.universal-tutorial.com/api/getaccesstoken", {
          headers: {
            Accept: "application/json",
            "api-token":
              "QbklUJd_2hgeEz7iOAzJ4aIyEq7Mwe34NQoPX_5DMtSf5hjWWvQWOwiobo8qofUI2ME",
            "user-email": "vikitavadadoriyasi@gmail.com",
          },
        })
        .then((res) => {
          this.authToken = res.data.auth_token;
          this.loadCountry();
        });
    },
    loadCountry() {
      this.loading = true;
      axios
        .get("https://www.universal-tutorial.com/api/countries", {
          headers: {
            Authorization: `Bearer ${this.authToken}`,
            Accept: "application/json",
          },
        })
        .then((res) => {
          this.listCountry = res.data;
        })
        .finally(() => (this.loading = false));
    },
    onChangeCountry() {
      this.loading = true;
      axios
        .get(
          `https://www.universal-tutorial.com/api/states/${this.selectedCountry}`,
          {
            headers: {
              Authorization: `Bearer ${this.authToken}`,
              Accept: "application/json",
            },
          }
        )
        .then((res) => {
          this.listState = res.data;
        })
        .finally(() => (this.loading = false));
    },
    onChangeState() {
      this.loading = true;
      axios
        .get(
          `https://www.universal-tutorial.com/api/cities/${this.selectedState}`,
          {
            headers: {
              Authorization: `Bearer ${this.authToken}`,
              Accept: "application/json",
            },
          }
        )
        .then((res) => {
          this.listCities = res.data;
        })
        .finally(() => (this.loading = false));
    },
  },
};
</script>
<style scoped>
.dropdown {
  display: flex;
  justify-content: center;
}
.country_dropdown {
  padding: 5px 4px;
  margin-left: 5px;
}
.state_dropdown {
  padding: 5px 4px;
  margin-left: 5px;
}
.city_dropdown {
  padding: 8px 6px;
  margin-left: 5px;
}
.state {
  margin: 0px 12px;
}
.city {
  margin: 0px 12px;
}
.list {
  margin: auto;
  width: 23%;
  padding: 10px;
}
.loader {
  position: absolute;
  left: 50%;
  top: 45%;
  border: 16px solid #f3f3f3;
  border-top: 16px solid #3498db;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  animation: spin 2s linear infinite;
}
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>