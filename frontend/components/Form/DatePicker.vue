<template>
  <div v-if="!inline" class="flex w-full flex-col">
    <Label class="cursor-pointer"> {{ label }} </Label>
    <VueDatePicker v-model="selected" :enable-time-picker="false" clearable :dark="isDark" :format="formatDate" />
  </div>
  <div v-else class="sm:flex sm:items-start sm:gap-4">
    <Label class="flex w-full cursor-pointer px-1 py-2"> {{ label }} </Label>
    <VueDatePicker v-model="selected" :enable-time-picker="false" clearable :dark="isDark" :format="formatDate" />
  </div>
</template>

<script setup lang="ts">
  // @ts-ignore
  import VueDatePicker from "@vuepic/vue-datepicker";
  import "@vuepic/vue-datepicker/dist/main.css";
  import * as datelib from "~/lib/datelib/datelib";
  import { Label } from "@/components/ui/label";

  const emit = defineEmits(["update:modelValue", "update:text"]);

  const props = defineProps({
    modelValue: {
      type: Date as () => Date | string | null,
      required: false,
      default: null,
    },
    inline: {
      type: Boolean,
      default: false,
    },
    label: {
      type: String,
      default: "Date",
    },
  });

  const isDark = useIsDark();

  const formatDate = (date: Date | string | number) => fmtDate(date, "human", "date");

  const selected = computed<Date | null>({
    get() {
      // String
      if (typeof props.modelValue === "string") {
        // Empty string
        if (props.modelValue === "") {
          return null;
        }

        // Invalid Date string
        if (props.modelValue === "Invalid Date") {
          return null;
        }

        return datelib.parse(props.modelValue);
      }

      // Date
      if (props.modelValue instanceof Date) {
        if (props.modelValue.getFullYear() < 1000) {
          return null;
        }

        if (isNaN(props.modelValue.getTime())) {
          return null;
        }

        // Valid Date
        return props.modelValue;
      }

      return null;
    },
    set(value: Date | null) {
      console.debug("DatePicker: SET", value);
      if (value instanceof Date) {
        value = datelib.zeroTime(value);
        emit("update:modelValue", value);
      } else {
        value = value ? datelib.zeroTime(new Date(value)) : null;
        emit("update:modelValue", value);
      }
    },
  });
</script>

<style class="scoped">
  ::-webkit-calendar-picker-indicator {
    filter: invert(1);
  }
</style>
