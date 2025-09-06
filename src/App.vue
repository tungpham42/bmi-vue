<template>
  <a-layout
    style="
      min-height: 100vh;
      background: linear-gradient(135deg, #f0f2f5 0%, #d9e6f2 100%);
      display: flex;
      align-items: center;
      justify-content: center;
    "
  >
    <a-row style="width: 100%" justify="center">
      <a-col :xs="24" :sm="20" :md="16" :lg="12">
        <a-card
          :title="title"
          bordered
          style="
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            background: #ffffff;
          "
        >
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
@import url("https://fonts.googleapis.com/css2?family=Work+Sans:wght@400;500;600;700&display=swap");
body {
  margin: 0;
  font-family: "Work Sans", sans-serif !important;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
