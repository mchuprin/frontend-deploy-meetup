<template>
  <el-card>
    <template #header>

    </template>
    <el-form
      ref="formRef"
      :model="formData"
      :rules="rules"
      @submit.prevent="handleSubmit"
      class="form"
    >
      <el-form-item label="Ваше имя" prop="name">
        <el-input
          v-model="formData.name"
          placeholder="Введите ваше имя"
        />
      </el-form-item>
      <el-form-item label="Ваша должность" prop="position">
        <el-input
          v-model="formData.position"
          placeholder="Введите вашу должность"
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" :loading="loading">
          Отправить
        </el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { ElButton, ElForm, ElFormItem, ElInput, ElMessage } from 'element-plus'

export default defineComponent({
  name: 'Form',
  components: {
    ElForm, ElFormItem, ElInput, ElButton
  },
  setup() {
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
        formDataToSubmit.append('entry.1234567890', formData.value.name) // Replace with actual field ID
        formDataToSubmit.append('entry.0987654321', formData.value.position) // Replace with actual field ID

        await fetch(formUrl, {
          method: 'POST',
          body: formDataToSubmit,
          mode: 'no-cors'
        })

        ElMessage.success('Форма успешно отправлена!')
        formData.value = {
          name: '',
          position: ''
        }
      } catch (error) {
        ElMessage.error('Произошла ошибка при отправке формы')
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
/* .form-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  background: var(--color-background-soft);
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  margin-bottom: 2rem;
  color: var(--color-heading);
}

.form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

@media (max-width: 768px) {
  .form-container {
    padding: 1.5rem;
  }
} */
</style>
