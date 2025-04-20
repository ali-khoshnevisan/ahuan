<template>
  <div class="table-container">
    <q-table :rows="rows" :columns="columns">
      <template v-slot:top>
        <q-btn color="primary" :disable="loading" label="Add Product" @click="addRow" />
        <q-space />
        <q-input borderless dense debounce="300" color="primary" v-model="filter">
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn
            flat
            dense
            color="primary"
            icon="edit"
            @click="editProduct(props.row)"
          />
          <q-btn
            flat
            dense
            color="negative"
            icon="delete"
            @click="deleteProduct(props.row)"
          />
        </q-td>
      </template>
    </q-table>
    <q-dialog v-model="editDialog">
      <q-card>
        <q-card-section>
          <div class="row q-col-gutter-md">
            <q-input filled v-model="title" />
            <q-input v-model="description" />
            <q-input v-model="price" />
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'IndexPage',
  data () {
    return{
      editDialog: false,
      title: '',
      description: '',
      price: 0,
      columns: [
        {name: 'Title', required: true, label: 'Title' , align: 'center', field: 'Title'},
        {name: 'Description', required: true, label: 'Description' , align: 'center', field: 'Description'},
        {name: 'Price', required: true, label: 'Price' , align: 'center', field: 'Price', sortable: true},
        {name: 'Category', required: true, label: 'Category' , align: 'center', field: 'Category'},
        {name: 'Image', required: true, label: 'Image' , align: 'center', field: 'Image'},
        {name: 'C_OR_R', required: true, label: 'C_OR_R' , align: 'center', field: 'C_OR_R'},
        {name: 'actions', required: true, label: 'Actions' , align: 'center', field: 'actions'},
      ],
      rows: []
    }
  },
  mounted() {
    this.getData()
  },
  methods: {
    getData() {
      this.$api.get('foods', {
        params: {
          type: 'T',
          cat: 'test'
        }
      })
        .then(res => {
          this.rows = res.data;
        })
        .catch(err => {
          console.log(err);
        })
    },
    editProduct (product) {
      this.editDialog = true;
      this.title = product.Title;
      this.description = product.Description;
      this.price = product.Price;
    }
  }
});
</script>

<style lang="scss" scoped>
.table-container {
  padding: 40px;
}
</style>
