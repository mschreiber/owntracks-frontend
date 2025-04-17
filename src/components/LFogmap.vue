<template>
  <div />
</template>

<script>
// See https://github.com/KoRiGaN/Vue2Leaflet/blob/e0cf0f29bc519f0a70f0f1eb6e579f947e7ea4ce/src/utils/utils.js
// to understand the `custom` attribute of each prop, how the `set<Prop>`
// methods are being used and why `mapObject` has to be named `mapObject`.

import { findRealParent, propsBinder } from "vue2-leaflet";
import L, { DomEvent } from "leaflet";
import "@/foglayer";

const props = {
  latLng: {
    type: Array,
    default: () => [],
    custom: false,
  },
  zoomAnimation: {
    type: Boolean,
    custom: true,
    default: true,
  },
  visible: {
    type: Boolean,
    custom: true,
    default: true,
  },
};

export default {
  props,
  mounted() {
    const options = {};
    if (this.zoomAnimation) {
      options.zoomAnimation = this.zoomAnimation;
    }
    this.mapObject = L.fogLayer(this.latLng, options);
    DomEvent.on(this.mapObject, this.$listeners);
    propsBinder(this, this.mapObject, props);
    this.parentContainer = findRealParent(this.$parent);
    this.parentContainer.addLayer(this, !this.visible);
    this.$watch(
      "latLng",
      (newVal) => {
        this.mapObject.setLatLngs(newVal);
      },
      { deep: true }
    );
  },
  beforeDestroy() {
    this.parentContainer.removeLayer(this);
  },
  methods: {
    setZoomAnimation(zoomAnimation) {
      this.mapObject.setOptions({ zoomAnimation });
    },
    setVisible(newVal, oldVal) {
      if (newVal === oldVal) return;
      if (newVal) {
        this.parentContainer.addLayer(this);
      } else {
        this.parentContainer.removeLayer(this);
      }
    },
    addLatLng(value) {
      this.mapObject.addLatLng(value);
    },
  },
};
</script>

<style scoped>
div {
  display: none;
}
</style>
