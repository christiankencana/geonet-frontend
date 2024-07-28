<template>
  <div class="container py-5">
    <h1>Home</h1>
    <p>Products</p>
    <button class="btn btn-success mb-3" @click="openCreateModal">Add</button>

    <div class="row">
      <div class="col-md-12">
        <div class="card mb-12">
          <div class="card-body">
            <table class="table">
              <thead>
                <tr>
                  <th>Name</th>
                  <th>Description</th>
                  <th>Price</th>
                  <th>Stock</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="products.length == 0">
                  <td colspan="12" class="text-center">
                    <div class="alert alert-danger mb-0">
                      Data Belum Tersedia!
                    </div>
                  </td>
                </tr>
                <tr v-else v-for="(product, index) in products" :key="index">
                  <td>{{ product.name }}</td>
                  <td>{{ product.description }}</td>
                  <td>{{ product.price }}</td>
                  <td>{{ product.stock }}</td>
                  <td class="text-center">
                    <div class="d-grid gap-2 d-md-block">
                      <button class="btn btn-primary mb-2 mb-md-0 me-md-2" @click="openEditModal(product)">Edit</button>
                      <button class="btn btn-danger" @click="deleteProduct(product.id)">Delete</button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- Create/Edit Modal -->
    <div class="modal fade" id="productModal" tabindex="-1" aria-labelledby="productModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="productModalLabel">{{ isEdit ? 'Edit Product' : 'Add Product' }}</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="saveProduct">
              <div class="mb-3">
                <label for="name" class="form-label">Name</label>
                <input type="text" class="form-control" v-model="form.name" required>
                <div v-if="errors.name" class="text-danger">{{ errors.name }}</div>
              </div>
              <div class="mb-3">
                <label for="description" class="form-label">Description</label>
                <textarea class="form-control" v-model="form.description" required></textarea>
                <div v-if="errors.description" class="text-danger">{{ errors.description }}</div>
              </div>
              <div class="mb-3">
                <label for="price" class="form-label">Price</label>
                <input type="number" class="form-control" v-model="form.price" required>
                <div v-if="errors.price" class="text-danger">{{ errors.price }}</div>
              </div>
              <div class="mb-3">
                <label for="stock" class="form-label">Stock</label>
                <input type="number" class="form-control" v-model="form.stock" required>
                <div v-if="errors.stock" class="text-danger">{{ errors.stock }}</div>
              </div>
              <button type="submit" class="btn btn-success">{{ isEdit ? 'Update' : 'Create' }}</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import api from '../services/api';
import { Modal } from 'bootstrap';

const products = ref([]);
const form = ref({
  id: null,
  name: '',
  description: '',
  price: 0,
  stock: 0
});
const errors = ref({});
const isEdit = ref(false);
let productModal = null;

const validateForm = () => {
  errors.value = {};

  if (!form.value.name || form.value.name.length < 5) {
    errors.value.name = 'Name is required and must be at least 5 characters';
  }
  if (!form.value.description || form.value.description.length < 10) {
    errors.value.description = 'Description must be at least 10 characters';
  }
  if (!form.value.price || form.value.price < 100) {
    errors.value.price = 'Price must be at least 100';
  }
  if (!form.value.stock || form.value.stock < 1) {
    errors.value.stock = 'Stock must be at least 1';
  }

  return Object.keys(errors.value).length === 0;
};

const fetchDataProducts = async () => {
  try {
    const response = await api.get('/products');
    products.value = response.data.data;
  } catch (error) {
    console.error('Failed to fetch products:', error);
  }
};

const openCreateModal = () => {
  isEdit.value = false;
  form.value = {
    id: null,
    name: '',
    description: '',
    price: 0,
    stock: 0
  };
  errors.value = {};
  productModal = new Modal(document.getElementById('productModal'));
  productModal.show();
};

const openEditModal = (product) => {
  isEdit.value = true;
  form.value = { ...product };
  errors.value = {};
  productModal = new Modal(document.getElementById('productModal'));
  productModal.show();
};

const saveProduct = async () => {
  if (!validateForm()) {
    return;
  }
  try {
    if (isEdit.value) {
      await api.put(`/products/${form.value.id}`, form.value);
    } else {
      await api.post('/products', form.value);
    }
    fetchDataProducts();
    productModal.hide();
  } catch (error) {
    console.error('Failed to save product:', error);
  }
};

const deleteProduct = async (id) => {
  if (confirm('Are you sure you want to delete this product?')) {
    try {
      await api.delete(`/products/${id}`);
      fetchDataProducts();
    } catch (error) {
      console.error('Failed to delete product:', error);
    }
  }
};

onMounted(() => {
  fetchDataProducts();
});
</script>

<style>
/* Add your custom styles here */
</style>
