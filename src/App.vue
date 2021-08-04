<template>
  <div id="app">
    <calendar
      initial-date="2020-08-05"
      @date-selected="dateSelected"
      :days="days"
    />
    <calendar style="margin-left: 1rem" :months="months" />
  </div>
</template>

<script>
import Calendar from "./components/Calendar.vue";

export default {
  name: "App",
  components: { Calendar },
  data() {
    return {
      months: [
        "yanv",
        "fevr",
        "mart",
        "apre",
        "mayy",
        "yun",
        "yul",
        "avg",
        "sent",
        "octy",
        "noy",
        "dek",
      ],
      days: ["vos", "pon", "vto", "sred", "che", "pya", "subb"],
    };
  },
  methods: {
    dateSelected(date) {
      console.log(date);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
