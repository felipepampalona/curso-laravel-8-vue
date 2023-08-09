<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">


                <!-- início do card de busca -->
                <card-component titulo="Busca de marcas">
                    <template v-slot:conteudo>
                        <div class="form-row">
                            <div class="col mb-3">
                                <input-container-component titulo="ID" id="inputId" id-help="idHelp" texto-ajuda="Opcional. Informe o ID da marca">
                                    <input type="number" class="form-control" id="inputId" v-model="busca.id" aria-describedby="idHelp" placeholder="ID">
                                </input-container-component>
                            </div>
                            <div class="col mb-3">
                                <input-container-component titulo="Nome da marca" id="inputNome" id-help="nomeHelp" texto-ajuda="Opcional. Informe o nome da marca">
                                    <input type="text" class="form-control" id="inputNome" v-model="busca.nome" aria-describedby="nomeHelp" placeholder="Nome da marca">
                                </input-container-component>
                            </div>
                        </div>
                    </template>

                    <template v-slot:rodape>
                        <button type="submit" class="btn btn-primary btn-sm float-right" @click="pesquisar()">Pesquisar</button>
                    </template>
                </card-component>
                <!-- fim do card de busca -->


                <!-- início do card de listagem de marcas -->
                <card-component titulo="Relação de marcas">
                    <template v-slot:conteudo>
                        <table-component 
                        :visualizar="{visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaVisualizar'}"
                        :atualizar="{visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaAtualizar'}"
                        :remover="{visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaRemover'}"
                        :titulos="{
                            id: {titulo: '#', tipo: 'texto'},
                            imagem: {titulo: 'Imagem', tipo: 'imagem'},
                            nome: {titulo: 'Nome', tipo: 'texto'},
                            created_at: {titulo: 'Criação', tipo: 'data'}
                        }"
                        :dados="marcas.data"></table-component>
                    </template>

                    <template v-slot:rodape>
                        <div class="row">
                            <div class="col-10">
                                <paginate-component>
                                    <li v-for="i, key in marcas.links" :key="key" :class="i.active ? 'page-item active' : 'page-item'">
                                        <a class="page-link" v-html="i.label" @click="paginacao(i)"></a>
                                    </li>
                                </paginate-component>
                            </div>
                            <div class="col-2">
                                <button type="button" class="btn btn-success btn-sm float-right" data-toggle="modal" data-target="#modalMarca">Adicionar</button>
                            </div>
                            </div>
                    </template>
                </card-component>
                <!-- fim do card de listagem de marcas -->
            </div>
        </div>


        <!-- inicio modal adicionar marca -->
        <modal-component id="modalMarca" titulo="Adicionar marca">

            <template v-slot:alertas>
                <alert-component tipo="success" :detalhes="transacaoDetalhes" titulo="Cadastro realizado com sucesso" v-if="transacaoStatus == 'adicionado'"></alert-component>
                <alert-component tipo="danger" :detalhes="transacaoDetalhes" titulo="Erro ao tentar cadastrar a marca" v-if="transacaoStatus == 'erro'"></alert-component>
            </template>

            <template v-slot:conteudo>
                <div class="form-group">
                    <input-container-component titulo="Nome da marca" id="novoNome" id-help="novoNomeHelp" texto-ajuda="Informe o nome da marca">
                        <input type="text" class="form-control" id="novoNome" aria-describedby="novoNomeHelp" placeholder="Nome da marca" v-model="nomeMarca">
                    </input-container-component>
                </div>

                <div class="form-group">
                    <input-container-component titulo="Imagem" id="novoImagem" id-help="novoImagemHelp" texto-ajuda="Selecione uma imagem no formato PNG">
                        <input type="file" class="form-control-file" id="novoImagem" aria-describedby="novoImagemHelp" placeholder="Selecione uma imagem" @change="carregarImagem($event)">
                    </input-container-component>
                </div>
            </template>

            <template v-slot:rodape>
                <button type="button" class="btn btn-danger" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-success" @click="salvar()">Salvar</button>
            </template>

        </modal-component>
         <!-- final modal adicionar marca -->

         <!-- inicio modal visualizar marca -->
        <modal-component id="modalMarcaVisualizar" titulo="Visualizar marca">

            <template v-slot:alertas></template>

            <template v-slot:conteudo>
                <div class="text-center">
                    <img :src="'/storage/'+$store.state.item.imagem" v-if="$store.state.item.imagem" class="img-thumbnail" width="72" height="72">
                </div>
                <div class="form-group">
                    <input-container-component titulo="ID">
                        <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                    </input-container-component>
                    <input-container-component titulo="Nome">
                        <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                    </input-container-component>
                    
                </div>
            </template>

            <template v-slot:rodape>
                <button type="button" class="btn btn-danger" data-dismiss="modal">Fechar</button>
            </template>

        </modal-component>
        <!-- final modal visualizar marca -->

         <!-- inicio modal remover marca -->
         <modal-component id="modalMarcaRemover" titulo="Remover marca">

            <template v-slot:alertas>
                <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                <alert-component tipo="danger" titulo="Erro ao relizar transação" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'erro'"></alert-component>
            </template>

            <template v-slot:conteudo v-if="$store.state.transacao.status != 'sucesso'">
                <div class="text-center">
                    <img :src="'/storage/'+$store.state.item.imagem" v-if="$store.state.item.imagem" class="img-thumbnail" width="72" height="72">
                </div>
                <div class="form-group">
                    <input-container-component titulo="ID">
                        <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                    </input-container-component>
                    <input-container-component titulo="Nome">
                        <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                    </input-container-component>
                    
                </div>
            </template>

            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-danger"  @click="remover()" v-if="$store.state.transacao.status != 'sucesso'">Remover</button>
            </template>

            </modal-component>
            <!-- final modal remover marca -->     
            
            
                    <!-- inicio modal atualizar marca -->
                    <modal-component id="modalMarcaAtualizar" titulo="Atualizar marca">

                        <template v-slot:alertas>
                            <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                            <alert-component tipo="danger" titulo="Erro ao relizar transação" :detalhes="$store.state.transacao" v-if="$store.state.transacao.status == 'erro'"></alert-component>
                        </template>

                    <template v-slot:conteudo>
                        <div class="form-group">
                            <input-container-component titulo="Nome da marca" id="novoNome" id-help="novoNomeHelp" texto-ajuda="Informe o nome da marca" >
                                <input type="text" class="form-control" v-model="$store.state.item.nome" placeholder="Nome da marca" >
                            </input-container-component>
                        </div>

                        <div class="form-group">
                            <input-container-component titulo="Imagem" id="novoImagem" id-help="novoImagemHelp" texto-ajuda="Selecione uma imagem no formato PNG">
                                <input type="file" class="form-control-file" id="atualizarImagemNova" placeholder="Selecione uma imagem" @change="carregarImagem($event)">
                            </input-container-component>
                        </div>
                    </template>

                    <template v-slot:rodape>
                        <button type="button" class="btn btn-danger" data-dismiss="modal">Fechar</button>
                        <button type="button" class="btn btn-warning" @click="atualizar()">Atualizar</button>
                    </template>

            </modal-component>
            <!-- final modal adicionar marca -->
    </div>
</template>

<script>
    export default {
        data() {
            return {
                urlBase: 'http://localhost:8000/api/v1/marca',
                urlPaginacao: '',
                urlFiltro: '',
                nomeMarca: '',
                arquivoImagem: [],
                transacaoStatus: '',
                transacaoDetalhes: {},
                marcas: { data: [] },
                busca: { id: '', nome: ''}
            }
        },
        methods: {
            atualizar(){
                let url = this.urlBase + '/' + this.$store.state.item.id
                //console.log(url)
                let formData = new FormData()
                formData.append('_method', 'patch')
                formData.append('nome', this.$store.state.item.nome)
                if(this.arquivoImagem[0]){
                formData.append('imagem', this.arquivoImagem[0])
                }
                
                let config = {
                    headers: {
                        'Content-Type': 'multipart/form-data'
                    }
                }

                axios.post(url, formData, config)
                    .then(response => {
                        atualizarImagemNova.value = ''
                        this.$store.state.transacao.status = 'sucesso'
                        this.$store.state.transacao.mensagem = 'Marca atualizada com sucesso'
                        this.carregarLista()
                    })
                    .catch(errors => {
                        this.$store.state.transacao.status = 'erro'
                        this.$store.state.transacao.mensagem = errors.response.data.message
                        this.$store.state.transacao.dados = errors.response.data.errors
                        
                        //errors.response.data.message
                    })
                
            },
            remover(){
                let confimacao = confirm('Tem certeza que deseja apagar?')
                if(!confimacao){
                     return false
                }
                let url = this.urlBase + '/' + this.$store.state.item.id
                //console.log(url)
                let formData = new FormData()
                formData.append('_method', 'delete')

                

                //let url = this.urlBase + '/493434'
                axios.post(url, formData)
                    .then(response => {
                        this.$store.state.transacao.status = 'sucesso'
                        this.$store.state.transacao.mensagem = response.data.msg
                        this.carregarLista()
                    })
                    .catch(errors => {
                        this.$store.state.transacao.status = 'erro'
                        this.$store.state.transacao.mensagem = errors.response.data.erro
                        
                        //errors.response.data.message
                    })
                
            },
            pesquisar(){
                let filtro = ''

                for(let chave in this.busca){
                    if(this.busca[chave]){
                    if(filtro != ''){
                        filtro +=';'
                    }
                    filtro += chave + ':like:' + this.busca[chave]
                }
            }
                //console.log(filtro)
                if(filtro != '') {
                    this.urlPaginacao = 'page=1'
                    this.urlFiltro = '&filtro=' + filtro
                }else {
                    this.urlFiltro = '';
                }
                this.carregarLista()
            },
            paginacao(i) {
                if(i.url){
                //this.urlBase = i.url
                this.urlPaginacao = i.url.split('?')[1]
                //console.log(this.urlPaginacao)
                this.carregarLista()
                }
            },
            carregarImagem(e) {
                this.arquivoImagem = e.target.files
            },
            salvar() {
                console.log(this.nomeMarca, this.arquivoImagem[0])

                let formData = new FormData();
                formData.append('nome', this.nomeMarca)
                formData.append('imagem', this.arquivoImagem[0])

                let config = {
                    headers: {
                        'Content-Type': 'multipart/form-data'
                    }
                }

                axios.post(this.urlBase, formData, config)
                    .then(response => {
                        this.transacaoStatus = 'adicionado'
                        this.transacaoDetalhes = {
                            mensagem: 'ID do registro: ' + response.data.id
                        }
                        console.log(response)
                    })
                    .catch(errors => {
                        this.transacaoStatus = 'erro'
                        this.transacaoDetalhes = {
                            mensagem: errors.response.data.message,
                            dados: errors.response.data.errors
                        }
                        
                        //errors.response.data.message
                    })
            },
            
            carregarLista() {

                let url = this.urlBase + '?' + this.urlPaginacao + this.urlFiltro

                axios.get(url)
                .then(response => {
                    this.marcas = response.data
                })
                .catch(error => {
                    console.log(error);
                })
            }
        }, 
        mounted() {
            this.carregarLista()
        }
    }
</script>
