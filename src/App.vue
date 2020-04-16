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
        <option disabled></option>
        <option
          v-for="object in touchableObjects"
          :key="object.name"
          :value="object.name"
        >
          {{ object.name }}
        </option>
      </select>
    </label>

    <label class="field field__label">
      How recently could someone else have touched this?
      <input
        class="field__input"
        type="datetime-local"
        v-model="touchedTime"
        required
      />
    </label>

    <div v-if="showResult">
      <p class="result" :class="{ 'result--yes': canTheyTouchThis, 'result--no': !canTheyTouchThis }">
        {{ canTheyTouchThisText }}
      </p>
      <p class="result__description">The novel coronavirus can survive on {{ selectedObject.name.toLowerCase() }} for up to {{ selectedObjectReadableLifetime }}.</p>
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
      touchableObjects: [
        {
          name: "Metal",
          lifetime: moment.duration(5, "days")
        },
        {
          name: "Wood",
          lifetime: moment.duration(4, "days")
        },
        {
          name: "Plastic",
          lifetime: moment.duration(3, "days")
        },
        {
          name: "Stainless Steel",
          lifetime: moment.duration(3, "days")
        },
        {
          name: "Cardboard",
          lifetime: moment.duration(1, "days")
        }
      ]
    };
  },
  computed: {
    showResult() {
      return this.rawSelectedObject !== "" && this.touchedTime !== null;
    },
    selectedObject() {
      const rawSelectedObject = this.rawSelectedObject;
      return this.touchableObjects.find(
        object => object.name === rawSelectedObject
      );
    },
    selectedObjectReadableLifetime() {
      return this.selectedObject.lifetime.humanize();
    },
    canTheyTouchThis() {
      if (this.selectedObject === undefined || !this.touchedTime) {
        return null;
      }

      const selectedObjectLifetime = this.selectedObject.lifetime;
      const touchedTime = moment(this.touchedTime);
      const durationDiff = moment.duration(moment().diff(touchedTime));
      return durationDiff.asHours() > selectedObjectLifetime.asHours();
    },
    canTheyTouchThisText() {
      if (this.canTheyTouchThis) {
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

.result {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 0.25em;

  &--yes {
    color: green;
  }

  &--no {
    color: red;
  }

  &__description {
    font-size: 0.875rem;
    margin: 0;
  }
}
</style>
