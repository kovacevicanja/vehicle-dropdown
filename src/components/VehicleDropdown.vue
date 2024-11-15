<template>
  <div class="container">
    <div class="form-group">
      <label for="year">Year:</label>
      <div class="dropdown">
        <select v-model="selectedYear" @change="fetchMakes">
          <option value="" disabled>Select a year</option>
          <option v-for="year in years" :key="year" :value="year">{{ year }}</option>
        </select>
      </div>
    </div>

    <div class="form-group" v-if="selectedYear">
      <label for="make">Make:</label>
      <div class="dropdown">
        <select v-model="selectedMake" @change="fetchModels">
          <option value="" disabled>Select a make</option>
          <option v-for="make in makes" :key="make" :value="make">{{ make.name }}</option>
        </select>
      </div>
    </div>

    <div class="form-group" v-if="selectedMake">
      <label for="model">Model:</label>
      <div class="dropdown">
        <select v-model="selectedModel">
          <option value="" disabled>Select a model</option>
          <option v-for="model in models" :key="model" :value="model">{{ model.model }}</option>
        </select>
      </div>
    </div>

    <div v-if="selectedModel" class="vehicle-info">
      <h3>Selected Vehicle</h3>
      <p>Year: {{ selectedYear }}</p>
      <p>Make: {{ selectedMake.name }}</p>
      <p>Model: {{ selectedModel.model }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      years: [],
      makes: [],
      models: [],
      selectedYear: "",
      selectedMake: "",
      selectedModel: "",
    };
  },
  mounted() {
    document.title = "Vehicle Dropdown";
    this.fetchYears();
  },
  methods: {
    async fetchYears() {
      try {
        const response = await axios.get("https://new.api.nexusautotransport.com/api/vehicles/years", {
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
        });
        this.years = response.data.data;
      } catch (error) {
        console.error("Error fetching years:", error);
      }
    },

    async fetchMakes() {
      if (!this.selectedYear) return;
      try {
        const response = await axios.get(`https://new.api.nexusautotransport.com/api/vehicles/makes?year=${this.selectedYear}`, {
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
        });
        this.makes = response.data.data;
        this.selectedMake = "";
        this.models = [];
        this.selectedModel = "";
      } catch (error) {
        console.error("Error fetching makes:", error);
      }
    },

    async fetchModels() {
      if (!this.selectedYear || !this.selectedMake) return;
      try {
        const response = await axios.get(
          `https://new.api.nexusautotransport.com/api/vehicles/models?year=${this.selectedYear}&make=${this.selectedMake.name}`,
          {
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        );
        this.models = response.data.data;
        this.selectedModel = "";
      } catch (error) {
        console.error("Error fetching models:", error);
      }
    },
  },
};
</script>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&family=Poppins:wght@400;500;700&display=swap');

.container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 30px;
  background-color: #ffffffcc;
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
  width: 400px;
  color: #333;
  font-family: 'Poppins', sans-serif; 
}

.form-group {
  margin-bottom: 20px;
  width: 100%;
}

.dropdown select {
  width: 100%;
  padding: 12px;
  border-radius: 8px;
  border: 1px solid #f1c1c1;
  font-size: 16px;
  background-color: #fff;
  color: #555;
  transition: background-color 0.3s ease, border 0.3s ease;
  font-family: 'Roboto', sans-serif;
}

.dropdown select:focus {
  border-color: #ff7f50;
  background-color: #fff1e6; 
}

.dropdown select option {
  background-color: #fff;
  padding: 10px;
}

.dropdown select option:checked {
  background-color: #ff7f50; /* Highlight color when selected */
  color: #fff; /* White text when selected */
}


label {
  font-weight: 500;
  color: #333;
  margin-bottom: 8px;
  display: block;
  font-family: 'Roboto', sans-serif; 
  font-size: 16px;
}

.vehicle-info {
  margin-top: 20px;
  padding: 15px;
  background-color: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h3 {
  color: #333;
  margin-bottom: 12px;
  font-family: 'Poppins', sans-serif; 
  font-weight: 600;
  font-size: 20px;
}

select {
  background-image: url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/svgs/solid/caret-down.svg');
  background-position: right 10px center;
  background-repeat: no-repeat;
  padding-right: 30px;
}

@media (max-width: 600px) {
  .container {
    width: 100%;
    padding: 15px;
  }
}

</style>
