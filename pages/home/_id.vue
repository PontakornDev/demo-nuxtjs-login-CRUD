<template>
  <div>
    <SnackBar :title="data.titleSnackbar" :value="data.valueSnackbar" :image="data.imageSnackbar" />

    <v-row justify="center" class="mt-8">
      <v-col
        cols="12"
        sm="10"
        md="8"
        lg="6"
      >
        <v-card>
          <v-card-title class="text-h5">
            My EDIT
          </v-card-title>
          <v-card-text>
            <v-text-field
              v-model="data.mirot30"
              type="text"
              :rules="[() => !!data.mirot30 || 'This field is required']"
              label="Mirot30"
              placeholder="Please fill in the box."
              required
              @keyup="data.formHasErrors = true"
            />
            <v-text-field
              v-model="data.crru30"
              type="number"
              :rules="[
                () => !!data.crru30 || 'This field is required',
              ]"
              label="Crru30"
              placeholder="Please fill in the box."
              required
              @keyup="data.formHasErrors = true"
            />
          </v-card-text>
          <v-divider class="mt-12" />
          <v-card-actions>
            <Button
              title="Cancel"
              button-color="bg-slate-300 hover:bg-slate-600"
              path="/home"
            />
            <v-spacer />
            <v-slide-x-reverse-transition>
              <v-tooltip
                v-if="data.formHasErrors"
                left
              >
                <template #activator="{ on, attrs }">
                  <v-btn
                    icon
                    class="my-0 mr-2"
                    v-bind="attrs"
                    @click="resetForm"
                    v-on="on"
                  >
                    <v-icon>mdi-refresh</v-icon>
                  </v-btn>
                </template>
                <span>Refresh form</span>
              </v-tooltip>
            </v-slide-x-reverse-transition>
            <v-btn
              color="secondary"
              class="bg-green-300 hover:bg-green-500"
              text
              @click="submit"
            >
              Submit
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script lang='ts'>
import { defineComponent, reactive, useRouter, onMounted, useContext } from '@nuxtjs/composition-api'
import Button from '~/components/button/Button.vue'
import SnackBar from '~/components/SnackBar.vue'

export default defineComponent({
  components: {
    Button,
    SnackBar
  },
  middleware: 'auth',
  setup () {
    const { $axios }: any = useContext()
    const router = useRouter()

    const data = reactive({
      formHasErrors: false,
      mirot30: '',
      crru30: '',
      params: '',
      titleSnackbar: '',
      imageSnackbar: '',
      valueSnackbar: false
    })
    onMounted(() => {
      data.params = router.history.current.params.id
      getData()
    })

    const getData = () => {
      $axios
        .$get(`/api/getedit/${data.params}`)
        .then((res: any) => {
          data.mirot30 = res.mirot30
          data.crru30 = res.crru30
        })
        .catch((err: any) => {
          console.log(err)
        })
    }

    const resetForm = () => {
      data.formHasErrors = false
      getData()
    }

    const resetStatus = () => {
      if (data.valueSnackbar) {
        setTimeout(() => {
          data.valueSnackbar = false
        }, 1000)
      }
    }

    const submit = async () => {
      const body = {
        mirot30: data.mirot30,
        crru30: data.crru30
      }

      await $axios
        .$put(`/api/update/${data.params}`, body)
        .then(() => {
          data.titleSnackbar = 'แก้ไขข้อมูลเรียบร้อย'
          data.imageSnackbar = '/check.png'
          data.valueSnackbar = true
          setTimeout(() => {
            router.push('/home')
          }, 1000)
        })
        .catch((err: any) => {
          console.log(err)
          data.titleSnackbar = 'เกิดข้อผิดพลาด'
          data.imageSnackbar = '/multiply.png'
          data.valueSnackbar = true
          setTimeout(() => {
            resetStatus()
          }, 2000)
        })
    }
    return { data, getData, resetForm, submit }
  }
})
</script>
