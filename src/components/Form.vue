<template>
  <el-card class="card">
    <template #header>
      <h2>Получи AI ассистента для быстрого деплоя</h2>
    </template>
    <el-form
      ref="formRef"
      :model="formData"
      :rules="rules"
      @submit.prevent="handleSubmit"
      class="form"
    >
      <el-form-item label="Ваше имя" label-position="top" prop="name">
        <el-input
          v-model="formData.name"
          placeholder="Введите ваше имя"
          size="large"
        />
      </el-form-item>
      <el-form-item label="Ваша должность" label-position="top" prop="position">
        <el-input
          v-model="formData.position"
          placeholder="Введите вашу должность"
          size="large"
        />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button
        :loading="loading"
        type="primary"
        size="large"
        @click="handleSubmit"
      >
        Отправить
      </el-button>
    </template>
  </el-card>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { ElButton, ElForm, ElFormItem, ElInput, ElNotification} from 'element-plus'
// @ts-ignore
export default defineComponent({
  name: 'Form',
  components: {
    ElForm, ElFormItem, ElInput, ElButton
  },
  props: {
    modelValue: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:model-value'],
  setup(_, { emit }) {
    const formRef = ref()
    const loading = ref(false)
    const formData = ref({
      name: '',
      position: ''
    })

    const rules = {
      name: [
        { required: true, message: 'Пожалуйста, введите ваше имя', trigger: 'blur' }
      ],
      position: [
        { required: true, message: 'Пожалуйста, введите вашу должность', trigger: 'blur' }
      ]
    }

    const handleSubmit = async () => {
      if (!formRef.value) return

      try {
        await formRef.value.validate()
        loading.value = true

        // Google Forms submission
        const formUrl = 'https://docs.google.com/forms/d/e/1FAIpQLSeSBpfwtDdDEWSHlpAoNT8tRJ7Tj0S6WUHGYIuXraQ3aEJDPw/formResponse'
        const formDataToSubmit = new FormData()

        // Add form fields (you'll need to get these field IDs from your Google Form)
        formDataToSubmit.append('entry.384679360', formData.value.name) // Replace with actual field ID
        formDataToSubmit.append('entry.200145190', formData.value.position) // Replace with actual field ID

        await fetch(formUrl, {
          method: 'POST',
          body: formDataToSubmit,
          mode: 'no-cors'
        })

        localStorage.setItem('formSubmitted', 'true')
        emit('update:model-value', true)

        ElNotification({
          title: 'Форма успешно отправлена!',
          type: 'success',
          duration: 3000
        })
        formData.value = {
          name: '',
          position: ''
        }
      } catch (error) {
        ElNotification({
          title: 'Произошла ошибка при отправке формы',
          type: 'error',
          duration: 3000
        })
      } finally {
        loading.value = false
      }
    }

    return {
      formRef,
      formData,
      rules,
      loading,
      handleSubmit
    }
  }
})
</script>

<style scoped>
.card {
  max-width: 600px;
}
</style>
