<template>
  <div class="block mb-2">
    <label :for="inputId" class="block text-xl font-bold mb-2">{{ inputLabel }}</label>
    <input
      type="text"
      :id="inputId"
      v-model="value"
      @input="passEvent"
      :placeholder="inputPlaceholder"
      class="block p-2 border-4 border-black w-full"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      value: '',
    };
  },
  props: {
    inputId: String,
    inputLabel: String,
    inputPlaceholder: String,
  },
  methods: {
    passEvent() {
      return this.$emit('valueChange', this.value);
    },
    clearValue() {
      this.value = '';
    },
  },
};
</script>

<style>
</style>
