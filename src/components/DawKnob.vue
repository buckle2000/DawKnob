<template lang="pug">
  .inline-wrapper
    .knob
      svg(viewBox="-50 -50 100 100")
        circle(r="45")
        line(x1="0" y1="0" v-bind:x2="lineEnd.x" v-bind:y2="lineEnd.y")
        path(v-bind:d="arcPath")
      label(v-if="!hideLabel") {{Math.round(value)}}
</template>


<script>
import Hammer from "hammerjs";
function polarToCartesian(centerX, centerY, radius, angleInDegrees) {
  var angleInRadians = ((angleInDegrees - 90) * Math.PI) / 180.0;

  return {
    x: centerX + radius * Math.cos(angleInRadians),
    y: centerY + radius * Math.sin(angleInRadians)
  };
}

function describeArc(x, y, radius, startAngle, endAngle) {
  var start = polarToCartesian(x, y, radius, endAngle);
  var end = polarToCartesian(x, y, radius, startAngle);

  var largeArcFlag = endAngle - startAngle <= 180 ? "0" : "1";

  var d = [
    "M",
    start.x,
    start.y,
    "A",
    radius,
    radius,
    0,
    largeArcFlag,
    0,
    end.x,
    end.y
  ].join(" ");

  return d;
}

// TODO make custom element
export default {
  props: {
    min: {
      default: 0,
      type: Number
    },
    max: {
      default: 100,
      type: Number
    },
    initialValue: {
      default: NaN,
      type: Number
    },
    hideLabel: {
      default: false,
      type: Boolean
    }
  },
  data() {
    const value = isNaN(this.initialValue) ? this.min : this.initialValue
    return {
      value,
      angle:
        -180 + ((value - this.min) / (this.max - this.min)) * 360
    };
  },
  watch: {
    value(v) {
      this.angle = -180 + ((v - this.min) / (this.max - this.min)) * 360;
    }
  },
  mounted() {
    var hammertime = new Hammer(this.$el.querySelector("svg"), {
      recognizers: [[Hammer.Pan, { direction: Hammer.DIRECTION_VERTICAL }]]
    });
    let lastPosition;
    hammertime.on("panstart", () => {
      lastPosition = 0;
    });
    hammertime.on("pan", ev => {
      let dy = ev.deltaY - lastPosition;
      lastPosition = ev.deltaY;
      // console.log(ev.velocityY)
      this.angle -= dy * 3;
      if (this.angle > 180) this.angle = 180;
      if (this.angle < -180) this.angle = -180;
      this.value =
        this.min + ((this.angle - -180) / 360) * (this.max - this.min);
    });
  },
  computed: {
    lineEnd() {
      return polarToCartesian(0, 0, 47.5, this.angle);
    },
    arcPath() {
      if (this.angle < 0) return describeArc(0, 0, 45, this.angle, 0);
      else return describeArc(0, 0, 45, 0, this.angle);
    }
  }
};
</script>

<style lang="stylus" scoped>
.inline-wrapper {
  display: inline-block;
}

.knob {
  display: grid;
  place-items center
}

svg {
  // height: 3em;
  width: 1em;
  fill: none;
}

circle {
  stroke: #ccc;
  stroke-width: 4;
}

line {
  stroke-linecap: butt;
}

line, path {
  stroke: black;
  stroke-width: 5;
}
</style>
