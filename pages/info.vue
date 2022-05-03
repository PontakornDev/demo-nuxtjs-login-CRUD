<template>
  <div>
    <div class="flex justify-center pt-6">
      <span>
        <b>พนธกร ช่างปัด 611998030 14 ม.12 ต.หงาว อ.เทิง จ.เชียงราย 0620409695</b><br>
        <b class="flex justify-center">Login Success</b>
      </span>
    </div>
    <div class="flex justify-center pt-6">
      <v-btn text class="w-60 bg-blue-600 hover:bg-blue-900 text-white" @click="logOut">
        <v-icon
          dark
          class="mr-2"
        >
          mdi-logout-variant
        </v-icon>
        Logout
      </v-btn>
    </div>
    <div class="flex justify-center pt-6">
      <Button
        title="HOME"
        button-color="bg-lime-600 hover:bg-lime-900"
        path="/home"
      />
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, useContext, useRouter } from '@nuxtjs/composition-api'
import Button from '~/components/button/Button.vue'

export default defineComponent({
  components: {
    Button
  },
  middleware: 'auth',
  setup () {
    const { $auth }: any = useContext()
    const router = useRouter()

    const logOut = async () => {
      await $auth.logout()
      await router.push('/login')
    }
    return { logOut }
  }
})
</script>
