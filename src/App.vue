<script lang="ts">
import { defineComponent, ref } from 'vue';
import Form from '@/components/Form.vue'
import Gift from '@/components/Gift.vue'


// @ts-ignore
export default defineComponent({
  name: 'App',
  components: {
    Form,
    Gift,
  },
  setup() {
    const isFormSubmitted = ref(localStorage.getItem('formSubmitted') === 'true');

    return {
      isFormSubmitted
    }
  }
})
</script>

<template>
  <main class="container">
    <Form v-if="!isFormSubmitted" v-model="isFormSubmitted" />
    <Gift v-else />
  </main>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #0083ff;
  position: relative;
}
</style>
