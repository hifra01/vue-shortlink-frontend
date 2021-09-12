<template>
  <div class="w-11/12 md:w-2/3 lg:w-7/12 mx-auto">
    <div class="my-10">
      <a :href="appUrl"
        ><h1 class="text-center font-bold text-2xl underline">
          {{ appTitle }}
        </h1></a
      >
    </div>
    <div class="border-4 border-black bg-yellow-400 p-5 mb-10">
      <form action="#" @submit.prevent="createAlias()">
        <div class="mb-5">
          <text-input
            ref="urlTextInput"
            inputId="urlInput"
            inputLabel="Original URL"
            inputPlaceholder="https://..."
            :inputData="url"
            @valueChange="setUrl($event)"
          />
          <error-text>{{ urlError }}</error-text>
        </div>
        <div class="mb-5">
          <text-input
            ref="aliasTextInput"
            inputId="aliasInput"
            inputLabel="New alias"
            inputPlaceholder="Leave empty to generate random alias"
            :inputData="alias"
            @valueChange="setAlias($event)"
          />
          <error-text>{{ aliasError }}</error-text>
        </div>
        <input
          type="submit"
          value="Submit"
          class="
            w-full
            p-2
            border-4 border-black
            bg-green-600
            text-white
            font-bold
            text-xl
            active:bg-green-800
            cursor-pointer
          "
        />
      </form>
    </div>
    <loading-box v-if="isLoading" />
    <error-box v-if="submitError">{{ submitError }}</error-box>
    <success-box v-if="!submitError && newUrl" :href="newUrl">{{
      newUrl
    }}</success-box>
  </div>
</template>

<script>
import Joi from 'joi';
import TextInput from './components/TextInput.vue';
import ErrorBox from './components/ErrorBox.vue';
import SuccessBox from './components/SuccessBox.vue';
import ErrorText from './components/ErrorText.vue';
import LoadingBox from './components/LoadingBox.vue';

export default {
  name: 'App',
  components: {
    TextInput,
    ErrorBox,
    SuccessBox,
    ErrorText,
    LoadingBox,
  },
  data() {
    return {
      appTitle: process.env.VUE_APP_TITLE,
      appUrl: process.env.VUE_APP_BASE_URL,
      url: '',
      alias: '',
      urlError: '',
      aliasError: '',
      submitError: '',
      newUrl: '',
      isLoading: false,
    };
  },
  methods: {
    validateUrl() {
      const validationResult = Joi.string().trim().uri().validate(this.url, { convert: false });
      if (!this.url) {
        return 'URL cannot be empty';
      }
      return validationResult.error ? 'Please insert a valid URL' : '';
    },
    validateAlias() {
      const validationResult = Joi.string().trim().allow('').regex(/^[\w-]+$/)
        .validate(this.alias, { convert: false });
      return validationResult.error ? 'Alias can only contain letters, numbers, dash (-), and underscore (_)' : '';
    },
    setUrl(newUrl) {
      this.urlError = '';
      this.url = newUrl;
      this.urlError = this.validateUrl();
    },
    setAlias(newAlias) {
      this.aliasError = '';
      this.alias = newAlias;
      this.aliasError = this.validateAlias();
    },
    clearUrl() {
      this.url = '';
      this.$refs.urlTextInput.clearValue();
    },
    clearAlias() {
      this.alias = '';
      this.$refs.aliasTextInput.clearValue();
    },
    async createAlias() {
      this.newUrl = '';
      this.submitError = '';
      this.urlError = this.validateUrl();
      this.aliasError = this.validateAlias();

      if (this.urlError || this.aliasError) return;

      try {
        this.isLoading = true;
        const response = await fetch(`${process.env.VUE_APP_BACKEND_URL}/api/add`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            url: this.url,
            alias: this.alias,
          }),
        });
        const data = await response.json();
        if (data.status === 'success') {
          this.clearUrl();
          this.clearAlias();
          this.isLoading = false;
          this.newUrl = `${process.env.VUE_APP_BACKEND_URL}/${data.alias}`;
        } else {
          this.isLoading = false;
          this.submitError = data.message;
        }
      } catch (error) {
        this.isLoading = false;
        this.submitError = 'Service is currently unavailable. Please try again later.';
      }
    },
  },
};
</script>

<style>
</style>
