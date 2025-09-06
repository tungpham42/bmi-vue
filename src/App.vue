<template>
  <a-layout style="min-height: 100vh; padding: 24px; background: #f0f2f5">
    <a-row justify="center">
      <a-col :xs="24" :sm="20" :md="14" :lg="10">
        <a-card :title="title" bordered>
          <BmiCalculator />
        </a-card>
      </a-col>
    </a-row>
  </a-layout>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import BmiCalculator from "./components/BmiCalculator.vue";

export default defineComponent({
  name: "App",
  components: { BmiCalculator },
  setup() {
    const title = ref("BMI Calculator & Advisor");
    return { title };
  },
});
</script>

<style>
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji",
    "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
}
</style>
