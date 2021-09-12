<template>
  <div
    class="
      block
      w-full
      justify-between
      border-4 border-black
      bg-blue-600
      text-white
      p-5
      mb-10
    "
  >
    <div class="mb-2 md:flex md:justify-between">
      <span
        class="
          w-full
          md:w-9/12
          text-xl
          font-bold
          underline
          my-auto
          block
          truncate
          md:inline
        "
      >
        <a :href="href" target="_blank" rel="noopener noreferrer">
          <slot></slot>
        </a>
      </span>
      <button
        class="
          w-full
          md:w-2/12
          p-2
          border-4 border-white
          bg-blue-600
          active:bg-blue-800
          block
          md:inline
          mt-3
          md:mt-0
        "
        @click.prevent="copyText()"
      >
        Copy
      </button>
    </div>
    <span class="block mt-2">{{ copyMessage }}</span>
  </div>
</template>

<script>
export default {
  data() {
    return {
      copyMessage: '',
    };
  },
  props: {
    href: String,
  },
  methods: {
    copyText() {
      navigator.clipboard.writeText(this.href);
      this.copyMessage = 'Copied to clipboard';
      setTimeout(() => {
        this.copyMessage = '';
      }, 3000);
    },
  },
};
</script>

<style>
</style>
