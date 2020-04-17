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
      How recently could someone else have
      <span class="orphan">touched this?</span>
      <input class="field__input" type="date" v-model="touchedDate" required />
    </label>

    <label
      class="field field__label"
      v-show="showTimeField"
      @focusin="timeFieldUsed = true"
    >
      What time could it have <span class="orphan">been touched?</span>
      <input class="field__input" type="time" v-model="touchedTime" required />
    </label>

    <div v-if="showResult">
      <p
        class="result"
        :class="{
          'result--yes': canTheyTouchThis === true,
          'result--maybe': canTheyTouchThis === undefined,
          'result--no': canTheyTouchThis === false
        }"
      >
        {{ canTheyTouchThisText }}
      </p>
      <p v-if="canTheyTouchThis === undefined">
        To get a definitive answer, you can either enter the time the object may have been exposed to the virus or you can play it safe and wait <span class="orphan">a day or two.</span>
      </p>
      <p class="result__description" v-if="canTheyTouchThis">
        You can safely touch this.
      </p>
      <p class="result__description" v-if="!canTheyTouchThis">
        You shouldn't touch this.
      </p>
      <p class="result__description">
        The novel coronavirus can survive on
        {{ selectedObject.name.toLowerCase() }} for up to
        {{ selectedObjectReadableLifetime }}.
        <span v-html="selectedObjectReadableSources"></span>
      </p>
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
      touchedDate: "",
      touchedTime: "",
      timeFieldUsed: false,
      touchableObjects: [
        {
          name: "Metal",
          lifetime: moment.duration(5, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Wood",
          lifetime: moment.duration(4, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Plastic",
          lifetime: moment.duration(3, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Stainless Steel",
          lifetime: moment.duration(3, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Cardboard",
          lifetime: moment.duration(1, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Copper",
          lifetime: moment.duration(4, "hours"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Aluminum",
          lifetime: moment.duration(8, "hours"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Glass",
          lifetime: moment.duration(5, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Ceramics",
          lifetime: moment.duration(5, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Paper",
          lifetime: moment.duration(5, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        },
        {
          name: "Food",
          lifetime: moment.duration(0, "days"),
          sources: [
            "https://www.webmd.com/lung/how-long-covid-19-lives-on-surfaces"
          ]
        }
      ]
    };
  },
  computed: {
    showTimeField() {
      return (
        this.timeFieldUsed ||
        this.canTheyTouchThis === undefined ||
        this.touchedTime !== ""
      );
    },
    showResult() {
      return this.rawSelectedObject !== "" && this.touchedDate !== "";
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
    selectedObjectReadableSources() {
      if (!this.selectedObject.sources) {
        return "";
      }

      const conditionalS = this.selectedObject.sources.length > 1 ? "s" : "";

      return (
        `[Source${conditionalS}: ` +
        this.selectedObject.sources.reduce(function(result, item, index) {
          const sourceNum = index + 1;
          const conditionalComma = index > 0 ? "," : "";
          return `${result}${conditionalComma} <a href="${item}">${sourceNum}</a>`;
        }, "") +
        "]"
      );
    },
    canTheyTouchThis() {
      if (this.selectedObject === undefined || !this.touchedDate) {
        return null;
      }

      const selectedObjectLifetime = this.selectedObject.lifetime;

      let touchedTime;
      if (this.touchedTime !== "") {
        touchedTime = moment(this.touchedDate + " " + this.touchedTime);
      } else {
        touchedTime = moment(this.touchedDate);
      }

      const durationDiff = moment.duration(moment().diff(touchedTime));

      console.log(durationDiff.asDays());
      console.log(selectedObjectLifetime.asDays());
      if (
        this.touchedTime === "" &&
        Math.abs(durationDiff.asDays() - selectedObjectLifetime.asDays()) < 1
      ) {
        return undefined;
      }
      return durationDiff.asHours() > selectedObjectLifetime.asHours();
    },
    canTheyTouchThisText() {
      if (this.canTheyTouchThis === true) {
        return "Yes";
      } else if (this.canTheyTouchThis === undefined) {
        return "Maybe";
      } else {
        return "No";
      }
    }
  }
};
</script>

<style lang="scss">
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 20vh 1rem 20vh;
}

h1 {
  font-size: 2.75rem;
  margin-bottom: 0.25em;
}
.sub-heading {
  font-size: 1rem;
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

  &--maybe {
    color: orange;
  }

  &--no {
    color: red;
  }

  &__description {
    font-size: 1rem;
    margin: 0 0 0.5em;
  }
}

.orphan {
  white-space: nowrap;
}
</style>
