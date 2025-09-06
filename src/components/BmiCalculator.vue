<template>
  <div style="padding: 24px">
    <a-form layout="vertical">
      <a-row :gutter="16">
        <a-col :xs="24" :sm="12">
          <a-form-item
            label="Weight"
            :help="weightHelp"
            :validate-status="weightStatus"
          >
            <a-input-number
              v-model:value="weight"
              :min="0"
              :step="0.1"
              style="width: 100%"
              placeholder="Enter weight"
            >
              <template #addonAfter>
                <a-select v-model:value="weightUnit" style="width: 80px">
                  <a-select-option value="kg">kg</a-select-option>
                  <a-select-option value="lb">lb</a-select-option>
                </a-select>
              </template>
            </a-input-number>
          </a-form-item>
        </a-col>
        <a-col :xs="24" :sm="12">
          <a-form-item
            label="Height"
            :help="heightHelp"
            :validate-status="heightStatus"
          >
            <a-input-number
              v-model:value="height"
              :min="0"
              :step="0.1"
              style="width: 100%"
              placeholder="Enter height"
            >
              <template #addonAfter>
                <a-select v-model:value="heightUnit" style="width: 80px">
                  <a-select-option value="cm">cm</a-select-option>
                  <a-select-option value="m">m</a-select-option>
                  <a-select-option value="in">in</a-select-option>
                </a-select>
              </template>
            </a-input-number>
          </a-form-item>
        </a-col>
      </a-row>
      <a-form-item style="margin-top: 16px">
        <a-button type="primary" size="large" block @click="calculate">
          <template #icon>
            <CalculatorOutlined />
          </template>
          Calculate BMI
        </a-button>
      </a-form-item>
    </a-form>
    <a-divider style="margin: 24px 0" />
    <a-alert
      v-if="calculated"
      type="info"
      :show-icon="false"
      style="border-radius: 8px"
    >
      <template #message>
        <div
          style="
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
          "
        >
          <div style="font-size: 1.5rem; font-weight: 700; color: #1890ff">
            Your BMI: {{ bmiValue.toFixed(2) }}
          </div>
          <a-tag
            :color="bmiColor"
            style="
              margin-top: 8px;
              font-weight: 600;
              font-size: 0.9rem;
              padding: 4px 12px;
              border-radius: 16px;
            "
          >
            {{ bmiCategory }}
          </a-tag>
          <div style="margin-top: 16px; font-size: 1rem; color: #555">
            {{ advisor }}
          </div>
          <a-progress
            :percent="progressPercent"
            :status="progressStatus"
            :show-info="false"
            :stroke-color="progressStrokeColor"
            style="margin-top: 24px"
          />
        </div>
      </template>
    </a-alert>
    <div v-else style="text-align: center; color: #999; padding: 20px">
      Enter your weight and height, then click Calculate.
    </div>
    <a-space style="width: 100%; justify-content: center; margin-top: 24px">
      <a-button @click="reset">Reset</a-button>
      <a-button type="dashed" @click="fillSample"
        >Sample (70kg, 175cm)</a-button
      >
    </a-space>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";
import {
  CalculatorOutlined,
  ReloadOutlined,
  UserOutlined,
} from "@ant-design/icons-vue";

type UnitWeight = "kg" | "lb";
type UnitHeight = "cm" | "m" | "in";

export default defineComponent({
  name: "BmiCalculator",
  components: {
    CalculatorOutlined,
  },
  setup() {
    const weight = ref<number | null>(null);
    const weightUnit = ref<UnitWeight>("kg");
    const height = ref<number | null>(null);
    const heightUnit = ref<UnitHeight>("cm");

    const calculated = ref(false);
    const bmiValue = ref(0);

    // Validation help/status
    const weightHelp = computed(() =>
      weight.value === null || weight.value <= 0
        ? "Please enter a valid weight."
        : ""
    );
    const heightHelp = computed(() =>
      height.value === null || height.value <= 0
        ? "Please enter a valid height."
        : ""
    );
    const weightStatus = computed(() =>
      weight.value === null || weight.value <= 0 ? "error" : ""
    );
    const heightStatus = computed(() =>
      height.value === null || height.value <= 0 ? "error" : ""
    );

    function toKg(w: number, unit: UnitWeight) {
      if (unit === "kg") return w;
      // lb -> kg
      return w * 0.45359237;
    }

    function toMeters(h: number, unit: UnitHeight) {
      if (unit === "m") return h;
      if (unit === "cm") return h / 100;
      // in -> m
      return h * 0.0254;
    }

    function calculate() {
      if (
        !weight.value ||
        weight.value <= 0 ||
        !height.value ||
        height.value <= 0
      ) {
        calculated.value = false;
        return;
      }
      const kg = toKg(weight.value, weightUnit.value);
      const m = toMeters(height.value, heightUnit.value);
      const bmi = kg / (m * m);
      bmiValue.value = bmi;
      calculated.value = true;
    }

    function reset() {
      weight.value = null;
      height.value = null;
      calculated.value = false;
      bmiValue.value = 0;
    }

    function fillSample() {
      weight.value = 70;
      weightUnit.value = "kg";
      height.value = 175;
      heightUnit.value = "cm";
      calculate();
    }

    const bmiCategory = computed(() => {
      const b = bmiValue.value;
      if (b === 0) return "";
      if (b < 18.5) return "Underweight";
      if (b < 25) return "Normal weight";
      if (b < 30) return "Overweight";
      return "Obesity";
    });

    const bmiColor = computed(() => {
      const c = bmiCategory.value;
      if (c === "Underweight") return "blue";
      if (c === "Normal weight") return "green";
      if (c === "Overweight") return "orange";
      return "red";
    });

    const advisor = computed(() => {
      const b = bmiValue.value;
      if (!calculated.value) return "";
      if (b < 18.5)
        return "You are underweight. Consider a balanced calorie-rich diet, strength training, and consult a nutritionist if needed.";
      if (b < 25)
        return "Great — your weight is within a healthy range. Maintain a balanced diet, regular physical activity, and regular checkups.";
      if (b < 30)
        return "You are overweight. Consider reducing calorie intake, increasing physical activity, and consult a health professional for a plan.";
      return "You are in the obesity range. It is recommended to seek medical guidance for a safe weight-loss program and monitor comorbidities.";
    });

    const progressPercent = computed(() => {
      const b = bmiValue.value;
      if (!calculated.value) return 0;
      // map BMI 15 -> 40 to 0-100 for display
      const min = 15;
      const max = 40;
      const clamped = Math.min(Math.max(b, min), max);
      return ((clamped - min) / (max - min)) * 100;
    });

    const progressStatus = computed(() => {
      const c = bmiCategory.value;
      if (c === "Normal weight") return "success";
      if (c === "Underweight") return "normal";
      if (c === "Overweight") return "exception";
      return "exception";
    });

    const progressStrokeColor = computed(() => {
      const c = bmiCategory.value;
      if (c === "Normal weight") return "#52c41a";
      if (c === "Underweight") return "#1890ff";
      if (c === "Overweight") return "#faad14";
      return "#f5222d";
    });

    return {
      weight,
      weightUnit,
      height,
      heightUnit,
      calculate,
      reset,
      fillSample,
      calculated,
      bmiValue,
      bmiCategory,
      advisor,
      bmiColor,
      progressPercent,
      progressStatus,
      progressStrokeColor,
      weightHelp,
      heightHelp,
      weightStatus,
      heightStatus,
    };
  },
});
</script>

<style>
* {
  font-family: "Work Sans", sans-serif !important;
}
/* small responsive tweaks */
@media (max-width: 576px) {
  .ant-input-number {
    width: 100% !important;
  }
}
</style>
