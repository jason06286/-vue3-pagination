<script>
import { defineComponent, ref } from 'vue';
import VPagination from '@/components/VPagination.vue';

export default defineComponent({
  name: 'App',
  components: {
    VPagination,
  },

  setup() {
    const page = ref(1);
    function updateHandler() {
      console.log(page.value);
    }

    return { page, updateHandler };
  },
});
</script>

<template>
  <div class="App">
    <v-pagination
      v-model="page"
      :range-size="1"
      :pages="20"
      @update:modelValue="updateHandler"
    />
  </div>
</template>

<style lang="scss">
</style>
