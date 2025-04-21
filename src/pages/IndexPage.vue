<template>
  <div class="table-container">
    <q-table :rows="rows"
             :columns="columns"
             :loading="loading"
             row-key="name">
      <template v-slot:top>
        <q-btn color="positive" :disable="loading" label="Add Product" icon="add" @click="addProduct" />
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
            @click="editProduct(props.row.Id)"
          />
          <q-btn
            flat
            dense
            color="negative"
            icon="delete"
            @click="deleteProduct(props.row.Id)"
          />
        </q-td>
      </template>
      <template v-slot:body-cell-image="props">
        <q-td :props="props">
          <q-img :src="props.row.Image" :alt="props.row.Image" />
        </q-td>
      </template>
    </q-table>
    <q-dialog v-model="dialog" class="dialog">
      <q-card class="dialog-card">
        <q-card-section>
          <q-btn flat icon="close" @click="dialog=false" />
          <span>
            {{editMode ? 'ویرایش محصول' : 'اضافه کردن محصول'}}
          </span>
        </q-card-section>
        <q-card-section>
          <div class="row q-col-gutter-md">
            <div class="col-12">
              <q-input label="title" outlined v-model="title" :loading="loading" />
            </div>
            <div class="col-12">
              <q-input label="description" outlined v-model="description" :loading="loading" />
            </div>
            <div class="col-12">
              <q-input label="price" outlined v-model="price" :loading="loading" />
            </div>
            <div class="col-12">
              <q-input label="image" outlined v-model="image" :loading="loading" />
            </div>
          </div>
        </q-card-section>
        <q-card-section>
          <q-btn :label="editMode ? 'update' : 'add'" :icon-right="editMode ? 'edit' : 'add'" :disable="loading" color="primary" @click="submitForm" />
        </q-card-section>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import {Notify} from "quasar";

export default defineComponent({
  name: 'IndexPage',
  data () {
    return{
      dialog: false,
      editMode: false,
      title: '',
      description: '',
      price: 0,
      image: '',
      productId: 0,
      loading: false,
      columns: [
        {name: 'Title', required: true, label: 'Title' , align: 'center', field: 'Title'},
        {name: 'Description', required: true, label: 'Description' , align: 'center', field: 'Description'},
        {name: 'Price', required: true, label: 'Price' , align: 'center', field: 'Price', sortable: true},
        {name: 'Category', required: true, label: 'Category' , align: 'center', field: 'Category'},
        {name: 'image', required: true, label: 'Image' , align: 'center', field: 'Image'},
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
    submitForm() {
      const data = {
        Title: this.title,
        Description: this.description,
        Price: this.price,
        Image: this.image,
        C_OR_R: 'T',
        Category: 'test'
      }
      if (this.editMode) {
        this.$api.put(`foods?id=${this.productId}`, data)
          .then(() => {
            Notify.create({
              message: 'محصول با موفقیت ویرایش شد',
              duration: 1000,
              position: 'top',
              type: 'positive'
            })
            this.getData()
            this.dialog = false
          })
          .catch(() => {
            this.dialog = false
          })
      } else {
        this.$api.post('foods', data)
          .then(() => {
            Notify.create({
              message: 'محصول با موفقیت اضافه شد',
              duration: 1000,
              position: 'top',
              type: 'positive'
            })
            this.getData()
            this.dialog = false
          })
          .catch(() => {
            this.dialog = false
          })
      }
    },
    getData() {
      this.loading = true
      this.$api.get('foods', {
        params: {
          type: 'T',
          cat: 'test'
        }
      })
        .then(res => {
          this.loading = false
          this.rows = res.data;
        })
        .catch(err => {
          this.loading = false
          console.error(err);
        })
    },
    getProductWithId (id) {
      this.$api.get('foods?id=' + id)
        .then(res => {
          this.loading = false
          this.title = res.data.Title
          this.description = res.data.Description
          this.price = res.data.Price
          this.image = res.data.Image
        })
        .catch(() => {
          this.loading = false
        })
    },
    addProduct () {
      this.editMode = false
      this.dialog = true
      this.title = ''
      this.description = ''
      this.price = ''
      this.image = ''
    },
    editProduct (productId) {
      this.editMode = true
      this.loading = true
      this.dialog = true
      this.productId = productId
      this.getProductWithId(productId)
    },
    deleteProduct (productId) {
      this.loading = true
      this.$api.delete('foods', {
        params: {
          id: productId
        }
      })
        .then(() => {
          this.loading = false
          this.getData()
          Notify.create({
            message: 'محصول با موفقیت حذف شد',
            duration: 1000,
            position: 'top',
            type: 'positive'
          })
        })
        .catch(() => {
          this.loading = false
          Notify.create({
            message: 'مشکلی به وجود آمده است',
            duration: 1000,
            position: 'top',
            type: 'negative'
          })
        })
    }
  }
});
</script>

<style lang="scss" scoped>
.table-container {
  padding: 40px;
}
:deep(.dialog-card) {
  border-radius: 64px;
}
</style>
