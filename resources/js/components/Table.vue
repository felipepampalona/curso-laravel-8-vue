<template>
  <div>
    <table class="table table-striped table-hover table-bordered">
        <thead class="thead-dark">
          <tr>
            <th scope="col" v-for="t, key in titulos" :key="key">{{t.titulo}}</th>
            <th v-if="atualizar.visivel || visualizar.visivel || remover.visivel"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="obj, chave in dadosFiltrados" :key="chave">
            <td v-for="valor, chaveValor in obj" :key="chaveValor">
                <span v-if="titulos[chaveValor].tipo == 'texto'">{{valor}}</span>
                <span v-if="titulos[chaveValor].tipo == 'data'">
                  {{ valor | formataDataTempoGlobal }}
                </span>
                <span v-if="titulos[chaveValor].tipo == 'imagem'">
                  <img :src="'/storage/'+valor" width="30" height="30" >
                 </span>
            </td>
            <td v-if="atualizar.visivel || visualizar.visivel || remover.visivel">
              <button type="button" v-if="visualizar.visivel" class="btn btn-outline-primary btn-sm" 
                :data-toggle="visualizar.dataToggle" :data-target="visualizar.dataTarget" @click="setStore(obj)">
                <i class="fa fa-eye"></i>
              </button>
              <button type="button"  v-if="atualizar.visivel" class="btn btn-outline-warning btn-sm"
              :data-toggle="atualizar.dataToggle" :data-target="atualizar.dataTarget" @click="setStore(obj)">
                <i class="fa fa-pencil"></i>
              </button>
              <button type="button"  v-if="remover.visivel" class="btn btn-outline-danger btn-sm"
              :data-toggle="remover.dataToggle" :data-target="remover.dataTarget" @click="setStore(obj)">
                <i class="fa fa-trash"></i>
              </button>
            </td>
          </tr>
      
        </tbody>
    </table>
  </div>
</template>

<script>
    export default {
      props: ['dados', 'titulos', 'visualizar', 'atualizar', 'remover'],
      methods: {
        setStore(obj){
          this.$store.state.transacao.status = ''
          this.$store.state.transacao.mensagem = ''
          this.$store.state.transacao.dados = ''
          this.$store.state.item = obj
        }
      },
      computed: {
            dadosFiltrados() {
                
                let campos = Object.keys(this.titulos)
                let dadosFiltrados = []

                this.dados.map((item, chave) => {

                    let itemFiltrado = {}
                    campos.forEach(campo => {
                        
                        itemFiltrado[campo] = item[campo] //utilizar a sintaxe de array para atribuir valores a objetos
                    })
                    dadosFiltrados.push(itemFiltrado)
                })

                return dadosFiltrados //retorne um array de objetos 
            }
        }
    }
</script>
