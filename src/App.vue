<template>
  <div id="app">
    <div>
      <fieldset>
        <legend>Volume Control</legend>
        Pan
        <DawKnob :hideLabel="true" :initialValue="50"/>
        Master
        <DawKnob :hideLabel="true" :initialValue="70"/>
      </fieldset>
      <p>
        What's your overall experience with our product?
        <!-- <input type="checkbox" name="some" id=""> -->
      </p>
      <p>
        Bad
        <DawKnob :initialValue="50" :hideLabel="true"/>
        Good
      </p>
    </div>
  </div>
</template>

<script>
import DawKnob from "./components/DawKnob.vue";

export default {
  name: "app",
  components: {
    DawKnob
  }
};
</script>

<style lang="stylus">
#app {
  font-family: Georgia;
  height: 90vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

fieldset {
  display inline-block
}

fieldset > * {
  vertical-align: bottom;
}

.knob {
  font-size: 24pt;
}
</style>
