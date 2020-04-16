<template>
  <div id="app">
    <h1>Can I touch this?</h1>
    <p class="sub-heading">
      A touching guide for the novel coronavirus (COVID-19) pandemic
    </p>

    <label class="field field__label">
      What do you want to touch?
      <select class="field__input field__select">
        <option name="metal">Metal</option>
      </select>
    </label>

    <label class="field field__label">
      How recently could someone have touched this?
      <input class="field field__input" type="datetime-local" />
    </label>
  </div>
</template>

<script>
const moment = require("moment");

export default {
  name: "App",
  data() {
    return {
      touchableObjects: {
        metal: {
          lifetime: moment.duration(5, "days")
        }
      }
    };
  }
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
h1 {
  margin-bottom: 0.25em;
}
.sub-heading {
  margin: 0 0 1em;
}
</style>
