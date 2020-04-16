<template>
  <div id="app">
    <h1>Can I touch this?</h1>
    <p class="sub-heading">
      A touching guide for the novel coronavirus (COVID-19) pandemic
    </p>

    <label class="field field__label">
      What do you want to touch?
      <select
        class="field__input field__select"
        v-model="rawSelectedObject"
        required
      >
        <option></option>
        <option>Metal</option>
      </select>
    </label>

    <label class="field field__label">
      How recently could someone have touched this?
      <input
        class="field__input"
        type="datetime-local"
        v-model="touchedTime"
        required
      />
    </label>

    <div v-if="showResult">
      <p class="result">{{ canTheyTouchThis }}</p>
    </div>
  </div>
</template>

<script>
const moment = require("moment");

export default {
  name: "App",
  data() {
    return {
      rawSelectedObject: "",
      touchedTime: null,
      touchableObjects: {
        metal: {
          lifetime: moment.duration(5, "days")
        }
      }
    };
  },
  computed: {
    showResult() {
      return this.selectedObject && this.touchedTime;
    },
    selectedObject() {
      return this.rawSelectedObject.toLowerCase();
    },
    canTheyTouchThis() {
      if (
        !(this.selectedObject in this.touchableObjects) ||
        !this.touchedTime
      ) {
        return "";
      }

      const selectedObjectLifetime = this.touchableObjects[this.selectedObject]
        .lifetime;
      const touchedTime = moment(this.touchedTime);
      const durationDiff = moment.duration(moment().diff(touchedTime));
      if (durationDiff.asHours() > selectedObjectLifetime.asHours()) {
        return "Yes";
      } else {
        return "No";
      }
    }
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
  font-size: 0.875rem;
  margin: 0 0 2em;
}

.field {
  display: block;
  text-align: center;
  margin: 1.25em;

  &__input {
    display: block;
    margin: 0.5em auto;
    text-align: center;
  }
}
</style>
