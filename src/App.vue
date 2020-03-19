<template>
  <div id="app">
    <label>Type something here!</label>
    <br>
    <input v-model="text">
    <div class="profile-pic">
      <person-icon/>
    </div>
    <div>{{text}}</div>
    <hr>
    <div clas="examples">
      <h2>Examples</h2>
      <div v-for="(personColorized, index) in peopleColorized" :key="index" class="example-tile">
        <div class="profile-pic" :style="{ 'background-color': personColorized.bgColor, 'fill': personColorized.fillColor }">
          <person-icon/>
        </div>
        {{ personColorized.person }}
      </div>
    </div>
  </div>
</template>

<script>
import PersonIcon from "./components/PersonIcon.vue";

export default {
  name: "App",
  components: {
    PersonIcon
  },
  data() {
    return {
      text: "Test123",
      colors: [
        "#1f55a5",
        "#a50032",
        "#f5c400",
        "#518428",
        "#00a8e1",
        "#6a4c9e",
        "#ef7622",
        "#005f5f"
      ],
      people: [
        "steve.bob@gmail.com",
        "sally.sue@yahoo.com",
        "steve.jobs@apple.com",
        "bill.gates@microsoft.com"
      ]
    };
  },
  mounted() {
    this.setColors();
  },
  watch: {
    text() {
      this.setColors();
    }
  },
  computed: {
    peopleColorized() {
      return this.people.map(person => {
        const colors = this.getColors(person);
        return { person, bgColor: colors[0], fillColor: colors[1] };
      });
    }
  },
  methods: {
    setColors() {
      const colors = this.getColors(this.text);
      document.getElementsByClassName("profile-pic")[0].style.backgroundColor = colors[0];
      document.getElementsByClassName("profile-pic")[0].style.fill = colors[1];
    },
    getColors(str) {
      function djb2(str) {
        var hash = 5381;
        for (var i = 0; i < str.length; i++) {
          hash = (hash << 5) + hash + str.charCodeAt(i); /* hash * 33 + c */
        }
        return hash;
      }

      function getCorrectTextColor(hex) {
        const threshold = 130; /* about half of 256. Lower threshold equals more dark text on dark background  */

        const cleanHex = hex.substring(1, 7);

        const hRed = parseInt(cleanHex.substring(0, 2), 16);
        const hGreen = parseInt(cleanHex.substring(2, 4), 16);
        const hBlue = parseInt(cleanHex.substring(4, 6), 16);

        const cBrightness = (hRed * 299 + hGreen * 587 + hBlue * 114) / 1000;
        if (cBrightness > threshold) return "#000000";
        else return "#ffffff";
      }

      var hash = djb2(str);
      const index = Math.abs(hash % (this.colors.length - 1));
      const color = this.colors[index];
      const combo = [color, getCorrectTextColor(color)];
      return combo;
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  height: 100%;
  width: 100%;
}

.profile-pic {
  height: 50px;
  width: 50px;
  border-radius: 50px;
  margin: 20px auto 0 auto;
  align-items: center;
  display: flex;
  justify-content: center;
  flex-direction: column;
}


label {
  margin-bottom: 20px;
}
</style>
