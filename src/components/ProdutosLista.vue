<template>
  <section class="produtos-container">
    <transition mode="out-in">
      <div key="produtos" v-if="produtos && produtos.length > 0" class="produtos">
        <div class="produto" v-for="produto in produtos" :key="produto.id">
          <router-link :to="{name: 'produto', params:{id: produto.id}}">
            <img v-if="produto.fotos" :src="produto.fotos[0].src" :alt="produto.fotos[0].titulo"/>
            <p class="preco">{{produto.preco}}</p>
            <h2 class="titulo">{{produto.nome}}</h2>
            <p>{{produto.descricao}}</p>
          </router-link>
        </div>
        <ProdutosPaginar :produtosTotal="produtosTotal" :produtosPorPagina="produtosPorPagina"/>
      </div>
      <div v-else-if="produtos && produtos.length === 0" key="sem-resultados">
        <p class="sem-resultados">Busca sem resultados, busque outro termo</p>
      </div>
     
        <PaginaCarregando v-else key="carregando"/>
    
    </transition>
  </section>
</template>

<script>
import ProdutosPaginar from '@/components/ProdutosPaginar.vue'
import api from '@/services.js'
import {serialize} from '@/helpers.js'

export default {
  name: "produtolista",
  components:{
    ProdutosPaginar
  },
  data() {
    return {
      produtos: [],
      produtosTotal: 0,
      produtosPorPagina: 9,
    }
  },
  computed:{
    url(){
      const query = serialize(this.$route.query)
      return `/produto?_limit=${this.produtosPorPagina}` + query
    }
  },
  methods:{
    async getProdutos(){
      this.produtos=null
      const {data, headers} = await api.get(`${this.url}`)
      this.produtosTotal = Number(headers['x-total-count'])
      this.produtos = data
    }
  },
  watch:{
    url(){
      this.getProdutos()
    }
  },
  created(){
    this.getProdutos()
  }
}
</script>

<style scoped>

.produtos-container{
  max-width: 1000px;
  margin: 0 auto;
}

.produtos {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 30px;
  margin: 30px;
}
@media screen and (max-width:500px) {
    .produtos {
      grid-template-columns: 1fr;
      gap: 10px;
    }
}
.produto{
  box-shadow: 0 4px 8px rgba(30, 60, 90, 0.1);
  padding: 10px;
  background: #fff;
  border-radius: 4px;
  transition: all 0.2s;
}

.produto:hover{
  box-shadow: 0 4px 8px rgba(30, 60, 90, 0.2);
  transform: scale(1.1);
  position: relative;
  z-index: 1;
}

.produto img{
  border-radius: 4px;
  margin-bottom: 20px;
}

.titulo{
  margin-bottom: 10px;
}

.preco{
  color: #e80;
  font-weight: bold;
}

.sem-resultados{
  text-align: center;
}

</style>