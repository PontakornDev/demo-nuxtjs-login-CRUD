<template>
  <div class="container" data-app>
    <DataTable
      :dialog-delete="data.dialogDelete"
      :headers="headers"
      :desserts="desserts"
      :search="data.search"
      :dialog="data.dialog"
      @delete="deleteItemConfirm"
      @deleteItem="deleteItem"
      @closeDelete="closeDelete"
    >
      <div slot="formData">
        <v-dialog
          v-model="data.dialog"
          max-width="500px"
        >
          <template #activator="{ on, attrs }">
            <v-btn
              color="green"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              <v-icon dark>
                mdi-folder-plus
              </v-icon>
              Add Item
            </v-btn>
          </template>

          <v-card>
            <v-form @submit.prevent="submit">
              <v-card-title>
                <span class="text-h5">Add Information</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field
                        v-model="data.mirot30"
                        label="MIROT30*"
                        type="text"
                        hint="TEXT"
                        required
                      />
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        v-model="data.crru30"
                        label="CRRU30*"
                        type="number"
                        hint="NUMBER"
                        required
                      />
                    </v-col>
                  </v-row>
                </v-container>
                <small>*indicates required field</small>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn
                  color="accent"
                  text
                  @click="data.dialog = false"
                >
                  Close
                </v-btn>
                <v-btn
                  color="success"
                  text
                  type="submit"
                >
                  Save
                </v-btn>
              </v-card-actions>
            </v-form>
          </v-card>
        </v-dialog>
      </div>
    </DataTable>
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
        title="INFO"
        button-color="bg-lime-600 hover:bg-lime-900"
        path="/info"
      />
    </div>
    <SnackBar :title="data.titleSnackbar" :value="data.valueSnackbar" :image="data.imageSnackbar" />
  </div>
</template>
<script lang="ts">
import { defineComponent, useContext, useRouter, reactive, computed, ref, onMounted } from '@nuxtjs/composition-api'
import DataTable from '~/components/containers/DataTable.vue'
import Button from '~/components/button/Button.vue'

export default defineComponent({
  components: {
    DataTable,
    Button
  },
  middleware: 'auth',
  setup () {
    const { $auth, $axios }: any = useContext()
    const router = useRouter()

    const desserts = ref<any>([])
    const data = reactive({
      search: '',
      code: '',
      mirot30: '',
      crru30: '',
      dialog: false,
      titleSnackbar: '',
      valueSnackbar: false,
      imageSnackbar: '',
      dialogDelete: false,
      idItem: 0
    })

    onMounted(() => {
      getData()
    })

    const headers = computed(() => {
      return [
        { text: 'mirot30', value: 'mirot30' },
        { text: 'crru30', value: 'crru30' },
        { text: 'Action', value: 'actions' }
      ]
    })

    const resetStatus = () => {
      if (data.valueSnackbar) {
        setTimeout(() => {
          data.valueSnackbar = false
        }, 1000)
      }
    }

    const getData = async () => {
      await $axios
        .$get('/api/getdata')
        .then((res: any) => {
          desserts.value = res
        })
        .catch((err: any) => {
          console.log(err)
        })
    }

    const closeDelete = () => {
      data.dialogDelete = false
    }

    const deleteItem = (item: any) => {
      data.idItem = item.id
      data.dialogDelete = true
    }

    const deleteItemConfirm = async () => {
      await $axios
        .$delete(`/api/delete/${data.idItem}`)
        .then(() => {
          data.titleSnackbar = 'ลบข้อมูลเรียบร้อย'
          data.imageSnackbar = '/check.png'
          data.valueSnackbar = true
          data.dialogDelete = false
          setTimeout(() => {
            resetStatus()
            getData()
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

    const submit = async (e: any) => {
      const body = {
        mirot30: e.target[0].value,
        crru30: e.target[1].value
      }

      data.dialog = false
      await $axios
        .$post('/api/add', body)
        .then(() => {
          data.mirot30 = ''
          data.crru30 = ''
          data.titleSnackbar = 'บันทึกข้อมูลเรียบร้อย'
          data.imageSnackbar = '/check.png'
          data.valueSnackbar = true
          setTimeout(() => {
            resetStatus()
            getData()
          }, 1000)
        })
        .catch((error: any) => {
          console.log(error)
          data.titleSnackbar = 'เกิดข้อผิดพลาด'
          data.imageSnackbar = '/multiply.png'
          data.valueSnackbar = true
          setTimeout(() => {
            resetStatus()
          }, 2000)
        })
    }

    const logOut = async () => {
      await $auth.logout()
      await router.push('/login')
    }
    return { logOut, data, headers, desserts, submit, closeDelete, deleteItemConfirm, deleteItem }
  }
})
</script>
