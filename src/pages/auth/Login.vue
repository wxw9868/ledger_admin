<template>
  <VaForm ref="form" @submit.prevent="submit">
    <h1 class="font-semibold text-4xl mb-4">Log in</h1>
    <p class="text-base mb-4 leading-5">
      New to Vuestic?
      <RouterLink :to="{ name: 'signup' }" class="font-semibold text-primary">Sign up</RouterLink>
    </p>
    <VaInput v-model="formData.username" :rules="[validators.required]" class="mb-4" label="Username" />
    <VaInput
      v-model="formData.email"
      :rules="[validators.required, validators.email]"
      class="mb-4"
      label="Email"
      type="email"
    />
    <VaValue v-slot="isPasswordVisible" :default-value="false">
      <VaInput
        v-model="formData.password"
        :rules="[validators.required]"
        :type="isPasswordVisible.value ? 'text' : 'password'"
        class="mb-4"
        label="Password"
        @clickAppendInner.stop="isPasswordVisible.value = !isPasswordVisible.value"
      >
        <template #appendInner>
          <VaIcon
            :name="isPasswordVisible.value ? 'mso-visibility_off' : 'mso-visibility'"
            class="cursor-pointer"
            color="secondary"
          />
        </template>
      </VaInput>
    </VaValue>

    <div class="auth-layout__options flex flex-col sm:flex-row items-start sm:items-center justify-between">
      <VaCheckbox v-model="formData.keepLoggedIn" class="mb-2 sm:mb-0" label="Keep me signed in on this device" />
      <RouterLink :to="{ name: 'recover-password' }" class="mt-2 sm:mt-0 sm:ml-1 font-semibold text-primary">
        Forgot password?
      </RouterLink>
    </div>

    <div class="flex justify-center mt-4">
      <VaButton class="w-full" @click="submit"> Login</VaButton>
    </div>
  </VaForm>
</template>

<script lang="ts" setup>
import { reactive } from 'vue'
import { useRouter } from 'vue-router'
import { useForm, useToast } from 'vuestic-ui'
import { validators } from '../../services/utils'

const { validate } = useForm('form')
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const { push } = useRouter()
const { init } = useToast()

const formData = reactive({
  username: 'wxw9868',
  email: '',
  password: '123456',
  keepLoggedIn: false,
})

import axios from 'axios'
import { ref } from 'vue'

axios.defaults.baseURL = 'http://localhost:8080'
// axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/json'

const userData = ref(false)

const fetchUserData = async () => {
  try {
    const response = await axios.post('/user/login', formData)
    console.log(response)
    userData.value = true
  } catch (error) {
    console.error(error)
  }
}

fetchUserData()
const obj = reactive({ userData })
console.log('data: ', obj.userData)

function login() {
  axios
    .post('/user/login', formData)
    .then(function (response) {
      console.log(response)
    })
    .catch(function (error) {
      console.log(error)
    })
}

const submit = () => {
  login()
  liquidation()
  if (validate()) {
    init({ message: "You've successfully logged in", color: 'success' })
    push({ name: 'dashboard' })
  }
}

function liquidation() {
  // 向给定ID的用户发起请求
  axios
    .get('/stock/liquidation')
    .then(function (response) {
      // 处理成功情况
      console.log(response)
    })
    .catch(function (error) {
      // 处理错误情况
      console.log(error)
    })
    .finally(function () {
      // 总是会执行
    })
}
</script>
