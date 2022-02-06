<template>
  <div class="container">
    <h1>Auth</h1>
    <form @submit.prevent="submitHandler" class="mb-3">
      <h3 v-if="error">{{ error }}</h3>

      <div class="mb-3">
        <label for="email" class="form-label">Email address</label>
        <input
          type="email"
          class="form-control"
          id="email"
          v-model="form.email.value"
          :class="{invalid: !form.email.valid  && form.email.touched}"
          @blur="form.email.blur"
        >
        <small class="text-danger" v-if="form.email.touched && form.email.errors.required">Email field is
          required</small>
      </div>
      <div class="mb-3">
        <label for="password" class="form-label">Password</label>
        <input
          type="password"
          class="form-control"
          id="password"
          v-model="form.password.value"
          :class="{invalid: !form.password.valid && form.password.touched}"
          @blur="form.password.blur"
        >
        <small class="text-danger" v-if="form.password.touched && form.password.errors.required">Password field is
          required</small>
        <small class="text-danger" v-else-if="form.password.touched && form.password.errors.minLength">
          Password length can't be less then <span class="text-primary">8</span>. Now it is
          <span class="text-primary">{{ form.password.value.length }}</span>.
        </small>
      </div>
      <button type="submit" class="btn btn-primary" :disabled="!form.valid">Submit</button>
    </form>
    <Suspense v-if="submitted">
      <UserList/>
      <template #fallback>
        <div class="text-center">
          <div class="spinner-border" style="width: 3rem; height: 3rem;" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
        </div>
      </template>
    </Suspense>
  </div>
</template>

<script>
import { useForm } from '@/use/form'
import UserList from '@/components/UserList'
import { ref, onErrorCaptured } from 'vue'

const required = val => !!val
const minLength = num => val => val.length >= num

export default {
  components: { UserList },
  setup () {
    const submitted = ref(false)
    const error = ref()
    const form = useForm({
      email: {
        value: '',
        validators: { required }
      },
      password: {
        value: '',
        validators: {
          required,
          minLength: minLength(8)
        }
      }
    })

    function submitHandler () {
      console.log('Email', form.email.value)
      console.log('Password', form.password.value)
      submitted.value = true
    }

    onErrorCaptured(e => {
      error.value = e.message
    })

    return {
      form,
      submitHandler,
      submitted,
      error
    }
  }
}
</script>

<style>
.invalid {
  box-shadow: 0 2px 4px brown, 0 2px 8px brown;
}
</style>
