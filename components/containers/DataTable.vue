<template>
  <v-data-table
    :headers="data.headers"
    :items="data.desserts"
    item-key="name"
    class="elevation-1"
    :search="data.search"
  >
    <template #top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>My CRUD</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        />
        <v-spacer />
        <v-col>
          <v-text-field
            v-model="data.search"
            label="Search"
            class="mx-4"
            append-icon="mdi-magnify"
            single-line
            hide-details
          />
        </v-col>
        <slot
          name="formData"
        />
        <v-dialog v-model="data.dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">
              Are you sure you want to delete this item?
            </v-card-title>
            <v-card-actions>
              <v-spacer />
              <v-btn color="blue darken-1" text @click="() => $emit('closeDelete')">
                Cancel
              </v-btn>
              <v-btn color="blue darken-1" text @click="() => $emit('delete')">
                OK
              </v-btn>
              <v-spacer />
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template #item.actions="{ item }">
      <v-btn
        dark
        small
        color="lime"
        :to="`/home/${item.id}`"
      >
        Edit
        <v-icon
          dark
          class="ma-2"
        >
          mdi-pencil
        </v-icon>
      </v-btn>
      <v-btn
        dark
        small
        color="red"
        @click="() => $emit('deleteItem',item)"
      >
        Delete
        <v-icon
          dark
          class="ma-2"
        >
          mdi-delete
        </v-icon>
      </v-btn>
    </template>
  </v-data-table>
</template>

<script lang="ts">
import { defineComponent, reactive, watch } from '@nuxtjs/composition-api'

export default defineComponent({
  props: {
    search: {
      type: String,
      default: ''
    },
    headers: {
      type: Array,
      default: () => []
    },
    desserts: {
      type: Array,
      default: () => []
    },
    dialog: {
      type: Boolean,
      default: false
    },
    dialogDelete: {
      type: Boolean,
      default: false
    }
  },
  setup (props) {
    const data = reactive({
      search: props.search,
      headers: props.headers,
      desserts: props.desserts,
      dialog: props.dialog,
      dialogDelete: props.dialogDelete
    })

    watch(() => props.dialog, (value) => {
      data.dialog = value
    })
    watch(() => props.desserts, (value) => {
      data.desserts = value
    })

    watch(() => props.headers, (value) => {
      data.headers = value
    })

    watch(() => props.search, (value) => {
      data.search = value
    })

    watch(() => props.dialogDelete, (value) => {
      data.dialogDelete = value
    })

    return { data }
  }
})
</script>
