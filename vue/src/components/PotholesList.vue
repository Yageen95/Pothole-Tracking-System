
<template>
  <div>
    <section>
      <!--for demo wrap-->
      <h1>Reported Potholes</h1>
      <div class="tbl-header">
        <table cellpadding="0" cellspacing="0" border="0">
          <thead
            class="clickable"
            @click="setZoom(11), setLat(39.983334), setLng(-82.98333)"
          >
            <tr>
              <th>Image</th>
              <th>Nearest Address</th>
              <th>Zip Code</th>
              <th>Direction of Travel</th>
              <th>Pothole Severity</th>
              <th>Discovery Date</th>
            </tr>
          </thead>
        </table>
      </div>
      <div class="tbl-content">
        <table cellpadding="0" cellspacing="0" border="0">
          <tbody v-for="pothole in filterByStatus" v-bind:key="pothole.id">
             
            <tr type="radio" id="radio"
              @click="sendCenterLocations(pothole.potholeId)"
              class="clickable"
            >
            <!-- <input id="radio" type="radio"> -->
            <td id= "img-container">
              <img id ="pothole-img" v-bind:src= "pothole.secureUrl"  alt="picture of a hole of the pot variety">
            </td>
              <td>
                {{ pothole.address.streetNumber }}
                {{ pothole.address.streetName }} {{ pothole.address.city }}, OH
              </td>
              <td>{{ pothole.address.zipCode }}</td>
              <td>{{ pothole.direction }}</td>
              <td>{{ pothole.severity }} / 10</td>
              <td>{{ dateFormat(pothole.discoveryDate) }}</td>
            </tr>
            
            <tr id="description">
              <td colspan="6">{{ pothole.description }}</td>
            </tr>
            <tr>
              <td id="placeholder" colspan="6"></td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </div>
</template>

<script>
import MapService from "../services/MapService.js";
export default {
  props: ["potholes"],
  data() {
    return {
      holder: "",
    };
  },
  computed: {
    filterByStatus() {
      let filteredPotholes = this.potholes;
      // console.log(this.filteredPotholes);
      const results = filteredPotholes.filter((pothole) => {
        return pothole.repair.status == "Pending";
      });
      return results;
    },
    currentCoordinate() {
      return this.$store.state.center.lat;
    },
  },
  methods: {
    addressToString(pothole) {
      let streetName = pothole.address.streetName;

      if (streetName.includes(" ")) {
        streetName = streetName.replace(" ", "+");
      }
      let url =
        pothole.address.streetNumber +
        "+" +
        streetName +
        ",+" +
        pothole.address.city +
        ",+" +
        pothole.address.state;
      return url;

      //1275+Kinnear+Rd,+Columbus,+OH
    },
    dateFormat(potholeDate) {
      let date = new Date(potholeDate);
      return date.toLocaleString();
    },
    retrieveId(potholeId) {
      this.$store.commit("SET_POTHOLE_ID", potholeId);
    },
    sendCenterLocations(id) {
      let chosenPothole = this.potholes.filter((pothole) => {
        return pothole.potholeId == id;
      });
      console.log(chosenPothole[0].potholeId);
      let url = this.addressToString(chosenPothole[0]);
      MapService.getMapInformation(url).then((response) => {
        let lat1 = response.data.results[0].geometry.location.lat;
        let lng1 = response.data.results[0].geometry.location.lng;
        this.$store.commit("SET_LAT", lat1);
        this.$store.commit("SET_LNG", lng1);
        this.$store.commit("SET_ZOOM", 15);
        this.holder = response.data;
      });
    },
    setLat(lat) {
      this.$store.commit("SET_LAT", lat);
    },
    setLng(lng) {
      this.$store.commit("SET_LNG", lng);
    },
    setZoom(zoom) {
      this.$store.commit("SET_ZOOM", zoom);
    },
  },
};
</script>

<style scoped>

#radio{
  /* display:none; */
}

#radio:checked > td{
  background-color: black;
}

#img-container{
  height: 5rem;
  width: 18%;
}

#pothole-img{
  height: 5rem;
  width: 84%;
}

.clickable:hover + #description {
  background-color: rgb(236, 227, 209);
  opacity: 85%;
  cursor: pointer;
}

.clickable:hover {
  background-color: rgb(236, 227, 209);
  opacity: 85%;
  cursor: pointer;
}

#placeholder {
  background-color: rgba(139, 27, 27, 0.63);
  padding: 2px;
}
section {
  height: 100%;
  /* background-color: rgba(255, 255, 255, 0.486); */
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
  /* background-color: rgba(255, 255, 255, 0.3); */
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
  /* box-shadow: inset 0 0 2px grey; */
  border-radius: 10px;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: rgba(139, 27, 27, 0.63);
  border-radius: 10px;
  /* box-shadow: inset 0 0 2px rgb(0, 0, 0); */
}
</style>