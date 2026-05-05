<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import http from './http';
  import type Produto from './Models/Produto';

  onMounted(listarProdutos)

  const produtos = ref([] as Produto[]);
  
  const produto = ref({
    nome: '',
    preco: 0
  } as Produto);

  async function listarProdutos() {
    const response = await http.get('/produtos');
    produtos.value = response.data;
  }

  async function adicionarProduto() {
    try {
      console.log('Enviando:', produto.value);

      const response = await http.post('/produtos', produto.value);

      if (response.status < 300) {
        produto.value = {
          nome: '',
          preco: 0
        } as Produto;

        listarProdutos();
      }
    } catch (error) {
      console.error('Erro no POST:', error);
    }
  }

  async function excluirProduto(id: number) {
    try {
      const response = await http.delete(`/produtos/${id}`);

      if (response.status < 300) {
        listarProdutos();
      }
    } catch (error) {
      console.error('Erro no DELETE:', error);
    }
  }

</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <div class="form-floating mb-3">
          <input type="text" class="form-control" id="floatingInput" placeholder="Nome do Produto" v-model="produto.nome">
          <label for="floatingInput">Nome do Produto</label>
        </div>
        <div class="form-floating">
          <input type="number" class="form-control" id="floatingPreco" placeholder="Preço" v-model="produto.preco">
          <label for="floatingPreco">Preço</label>
        </div>

        <button class="btn btn-primary" @click="adicionarProduto">Adicionar Produto</button>

        <table class="table">
          <thead>
            <tr>
              <th scope="col">ID</th>
              <th scope="col">Nome</th>
              <th scope="col">Preço</th>
              <th scope="col">Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="value in produtos" :key="value.id">
              <td>{{ value.id }}</td>
              <td>{{ value.nome }}</td>
              <td>{{ value.preco }}</td>
              <td>
                <div class="gap-2 d-flex">
                  <button class="btn btn-primary"><img src="./assets/editar.png" alt="" width="20"></button>
                  <button class="btn btn-danger" @click="excluirProduto(value.id)"><img src="./assets/lixeira-de-reciclagem.png" alt="" width="20"></button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
