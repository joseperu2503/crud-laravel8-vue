<template>
    <div class="container">
        <div class="row">
            <h1 class="text-center">Gestionar Articulos</h1>
            <hr>
            <!-- Button trigger modal -->
            <div class="col-lg-12 mb-4">
                <button @click="modificar=false; abrirModal();" type="button" class="btn btn-primary">
                    Nuevo
                </button>
            </div>
            

            <!-- Modal -->
            <div class="modal fade"  :class="{mostrar:modal}" >
                <div class="modal-dialog modal-dialog-centered">
                    <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel"> {{tituloModal}} </h5>
                        <button @click="cerrarModal();" type="button" class="btn-close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="mb-3">
                            <label for="nombre" class="form-label">Nombre</label>
                            <input type="text" class="form-control" id="nombre" v-model="articulo.nombre">
                        </div>
                        <div class="mb-3">
                            <label for="descripcion" class="form-label">Descripcion</label>
                            <textarea type="text" class="form-control" id="descripcion" v-model="articulo.descripcion" rows="4"></textarea>
                        </div>
                        <div class="mb-3">
                            <label for="stock" class="form-label">stock</label>
                            <input type="number" class="form-control" id="stock" v-model="articulo.stock">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button @click="cerrarModal();" type="button" class="btn btn-secondary">Cancelar</button>
                        <button @click="guardar();" type="button" class="btn btn-primary">Guardar</button>
                    </div>
                    </div>
                </div>
            </div>
            <div class="col-12">
                <div class="table-responsive">
                    <table class="table">
                        <thead>
                            <tr>
                                <th>ID</th>
                                <th>Nombre</th>
                                <th>Stock</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="articulo in articulos" :key="articulo.id">
                                <td>{{articulo.id}}</td>
                                <td>{{articulo.nombre}}</td>
                                <td>{{articulo.stock}}</td>
                                <td>
                                    <button class="btn btn-warning" @click="modificar=true; abrirModal(articulo);">Editar</button>
                                    <a type="button" @click="eliminar(articulo.id)" class="btn btn-danger">Eliminar</a>
                                </td>

                            </tr>
                        </tbody>

                    </table>

                </div>

            </div>

        </div>
    </div>
</template>
<script>
export default {
    name: "articulos",
    data(){
        return{
            articulo:{
                nombre:'',
                descripcion:'',
                stock:''
            },
            id:0,
            modificar:true,
            modal:0,
            tituloModal:'',
            articulos:[]
        }
    },
    mounted(){
        this.listar()
    },
    methods:{
        async listar(){
            await axios.get('articulos')
            .then(response=>{
                this.articulos = response.data
            })
            .catch(error =>{
                console.log(error)
                this.articulo = []
            })
        },
        async eliminar(id){
            await axios.delete('articulos/'+id)  
            .then(response=>{
                this.listar()
            })
            .catch(error =>{
                console.log(error)
            })  
        },
        async guardar(id){
            if(this.modificar){
                await axios.put('articulos/'+this.id,this.articulo)  
                .then(response=>{
                    this.cerrarModal();
                    this.listar()
                })
                .catch(error =>{
                    console.log(error)
                }) 
            }else{
                await axios.post('articulos',this.articulo)  
                .then(response=>{
                    this.cerrarModal();
                    this.listar()
                })
                .catch(error =>{
                    console.log(error)
                })               
            }
             
        },
        abrirModal(data={}){
            this.modal=1;
            if(this.modificar){
                this.id=data.id;
                this.tituloModal='Modificar Artículo';
                this.articulo.nombre=data.nombre;
                this.articulo.descripcion=data.descripcion;
                this.articulo.stock=data.stock;
            }else{
                this.id=0;
                this.tituloModal='Crear Artículo';
                this.articulo.nombre='';
                this.articulo.descripcion='';
                this.articulo.stock=1;
            }
        },
        cerrarModal(){
            this.modal=0;
        },
    }
}
</script>
<style>
.mostrar{
    display: list-item;
    opacity: 1 !important;
    background: rgba(0, 0, 0, 0.281);
}
</style>