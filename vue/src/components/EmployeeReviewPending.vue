<template>
  <div>
    <section>
      <!--for demo wrap-->
      <h1>Pending Potholes</h1>
      <div class="tbl-header">
        <table cellpadding="0" cellspacing="0" border="0">
          <thead>
            <tr>
              <th>Nearest Address</th>
              <th>Zip Code</th>
              <th>Severity</th>
              <th>Discovery Date</th>
            </tr>
          </thead>
        </table>
      </div>
      <div class="tbl-content">
        <table cellpadding="0" cellspacing="0" border="0">
          <tbody
            v-for="pothole in filterByStatus"
            v-bind:key="pothole.potholeId"
          >
            <tr v-on:click="retrieveId(pothole.potholeId)" class="clickable">
              <td>
                {{ pothole.address.streetNumber }}
                {{ pothole.address.streetName }} {{ pothole.address.city }}, OH
              </td>
              <td>{{ pothole.address.zipCode }}</td>
              <td>{{ pothole.severity }} / 10</td>
              <td>{{ dateFormat(pothole.discoveryDate) }}</td>
            </tr>
            <tr id="description">
              <td colspan="4">{{ pothole.description }}</td>
            </tr>
            <tr>
              <td id="placeholder" colspan="4"></td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  props: ["potholes"],

  computed: {
    filterByStatus() {
      let filteredPotholes = this.potholes;
      // console.log(this.filteredPotholes);
      const results = filteredPotholes.filter((pothole) => {
        return pothole.repair.status == "Pending";
      });
      return results;
    },
  },
  methods: {
    retrieveId(potholeId) {
      this.$store.commit("SET_POTHOLE_ID", potholeId);
    },
    dateFormat(potholeDate) {
      let date = new Date(potholeDate);
      return date.toLocaleString();
    },
  },
};
</script>

<style scoped>
.clickable:hover + #description {
  background-color: rgb(235, 219, 191);
  opacity: 85%;
  cursor: pointer;
}
.clickable:hover {
  background-color: rgb(235, 219, 191);
  opacity: 85%;
  cursor: pointer;
}
#placeholder {
  background-color: rgba(139, 27, 27, 0.63);
  padding: 2px;
}
section {
  width: 40vw;
  /* background-image: url("../assets/how-potholes-form.jpg");
    background-repeat: no-repeat;
    background-size: cover; */
  height: 70vh;
  border: outset 1px grey;
  border-radius: 15px;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.24),
  0 2px 2px 0 rgba(0, 0, 0, 0.19);
  margin-top: 80px;
  margin-left: 24px;
}
div {
  /* display: flex;
    justify-content: center;
    align-items: center; */
}
h1 {
  font-size: 20px;
  color: black;
  text-transform: uppercase;
  font-weight: bolder;
  text-align: center;
  height: 5%;
  font-family: Arial, Helvetica, sans-serif;
}
table {
  width: 100%;
  table-layout: fixed;
}
.tbl-header {
  background-color: rgba(255, 255, 255, 0.3);
  border: rgba(255, 255, 255, 0.3);
}
.tbl-content {
  height: 70%;
  overflow-x: auto;
  margin-top: 0px;
  border: 1px solid rgba(255, 255, 255, 0.3);
}
th {
  padding: 20px 15px;
  text-align: left;
  font-weight: bolder;
  font-size: 12px;
  color: black;
  text-transform: uppercase;
  font-family: Arial, Helvetica, sans-serif;
}
td {
  padding: 15px;
  text-align: left;
  vertical-align: middle;
  font-weight: bold;
  font-size: 12px;
  color: black;
  border-bottom: solid 1px rgba(255, 255, 255, 0.1);
  font-family: Arial, Helvetica, sans-serif;
}
/* width */
::-webkit-scrollbar {
  width: 15px;
  height: 5px;
}

/* Track */
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 1px grey;
  border-radius: 10px;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: rgba(139, 27, 27, 0.63);
  border-radius: 10px;
  box-shadow: inset 0 0 1px rgb(0, 0, 0);
}
</style>