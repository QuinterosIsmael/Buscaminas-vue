<template>
    <div class="tablero">
            <div class="niveles">
                <span>Nivel:</span>
                <span @click="seleccionarNivel(1)" class="nivel" :class="{'nivel-seleccionado':nivelActual.nivel==1}">1</span>
                <span @click="seleccionarNivel(2)" class="nivel" :class="{'nivel-seleccionado':nivelActual.nivel==2}">2</span>
                <span @click="seleccionarNivel(3)" class="nivel" :class="{'nivel-seleccionado':nivelActual.nivel==3}">3</span>
            </div>

            <div class="panel">
                <div class="marcador minas-restantes">
                    {{minasRestantesCifras}}
                </div>
                <div class="cara" @click="inicio">
                   <span>{{cara}}</span> 
                </div>

                <div class="segundos">
                    {{segundosCifras}}
                </div>
            </div>
            
            <div class="matriz ">           
                <cuadro @onCambiarMinasRestantes="cambiarMinasRestantes"
                @onActivar="activarCuadro" :info="item" v-for="(item,index) in cuadros" :key="index" 
                :style=" 'grid-row: ' + item.fila+ '; grid column:' + item.columna+';'" />
            </div>
        </div>
</template>

<script>

import Cuadro from "./Cuadro.vue"

export default {
    components: {
        Cuadro
    },
    data(){
        return{
            cara:'😀',
            cuadros:[],
            colores:['','uno','dos','tres','cuatro','cinco'],
            nivelUno:{
                nivel:1,
                filas:9,
                columnas:9,
                minas:10,
            },
            nivelDos:{
                nivel:2,
                filas:16,
                columnas:16,
                minas:40,
            },
            nivelTres:{
                nivel:3,
                filas:16,
                columnas:30,
                minas:99,
            },
            nivelActual: null,
            minas:[],
            minasRestantes:[],
            segundos:0,
            iniciar:false,
            timer:null,
            jugando:false,
            cuadrosRestantes:0,
        }
    },
    computed:{
        minasRestantesCifras(){
            let cifras= this.minasRestantes.toString()

            if(cifras.length==1){
                cifras= '00' + cifras
            }else if(cifras.length==2){
                cifras= '0' + cifras
            }
            return cifras
        },
        segundosCifras(){
            let cifras=this.segundos.toString()

            if (cifras.length==1){
                cifras= '00' + cifras
            }else if(cifras.length==2){
                cifras= '0' + cifras
            }
            return cifras 
        }
    },
    created(){
        this.nivelActual= this.nivelUno
        this.inicio()
    },

    methods: {

        detenerTime(){
            if(this.timer){
                clearInterval(this.timer)
            }
        },

        seleccionarNivel(nivel){

            //Se le selecciona el valor del nivel 
            if (this.nivelActual.nivel==nivel){
                return
            }
            if(nivel==1){
                this.nivelActual= this.nivelUno
            }
            else  if(nivel==2){
                this.nivelActual= this.nivelDos
            }
            else{
                this.nivelActual= this.nivelTres
            }

            this.inicio()
        },

        inicio(){

            this.cara='😀'
            this.detenerTime()
            this.segundos=0
            this.iniciar=false
            this.minasRestantes=this.nivelActual.minas

            //Cuando comienza la funcion se definen las columnas y las filas del nivel actual y se le otorga a 
            //cuadrosTotales las filas*columnas
            let filas=this.nivelActual.filas
            let columnas=this.nivelActual.columnas
            let cuadrosTotales= filas * columnas

            this.cuadrosRestantes= cuadrosTotales - this.nivelActual.minas

            this.cuadros= []
            let indices=[]

            for (let i  = 0; i < cuadrosTotales; i++ ){
                let cuadro={
                    inicial:true, //inicial indica si el cuadro esta o no cubierto
                    bandera:false,  //indica si  tiene una bandera o no
                    valor:'',
                    fila: Math.floor(i/columnas)+1,
                    columna:(i%columnas)+1,
                    vecinos: [],
                    claseValor: ''  //clase valor indica que tipo de clase va a tener el cuadro  dependiendo  su  numero
                }

                // console.log(i,cuadro.fila, cuadro.columna)

                this.cuadros.push(cuadro)
                // Al terminar el for el array indice va a cargar todas las posiciones
                indices.push(i)

            }
            for (let i  = 0;  i < this.nivelActual.minas;  i++){
                let posicion=Math.floor(Math.random()*(indices.length-1))
                let indice=  indices[posicion]

                this.cuadros[indice].valor= '💣'
                this.minas.push(this.cuadros[indice])
                indices.splice(posicion,1)

                // Hasta llegar al numero maximo de minas se van colocando en una posicion aleatoria del tablero
            }


            //Se empiezan a recorrer los cuadros y se marca el numero en cada uno 
            //dependiendo de la cantidad de minas que haya en sus cuadros vecinos

            for (let i = 0; i < cuadrosTotales; i++){
                let cuadro = this.cuadros[i];
                
                if(cuadro.columna == 1){
                    if(cuadro.fila== 1){
                        cuadro.vecinos.push(i + 1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas+1)      
                          
                    }
                    else if(cuadro.fila==filas){
                        cuadro.vecinos.push(i + 1)
                        cuadro.vecinos.push(i - columnas)
                        cuadro.vecinos.push(i - columnas + 1)
                        console.log(cuadro.vecinos)  
                    }
                    else{
                        cuadro.vecinos.push(i + 1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas+1)  
                        cuadro.vecinos.push(i - columnas)
                        cuadro.vecinos.push(i - columnas + 1) 
                    }
                }
                else if (cuadro.columna== columnas){
                    if(cuadro.fila==1){
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas-1)
                    }
                    else if(cuadro.fila==filas){
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i-columnas)
                        cuadro.vecinos.push(i-columnas-1)
                    }
                    else{
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas-1)
                        cuadro.vecinos.push(i-columnas)
                        cuadro.vecinos.push(i-columnas-1)
                    }
                } 
                else{
                    if(cuadro.fila==1){
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i+1)
                        cuadro.vecinos.push(i+columnas-1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas+1)
                    }
                    else if(cuadro.fila==filas){
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i+1)
                        cuadro.vecinos.push(i-columnas-1)
                        cuadro.vecinos.push(i-columnas)
                        cuadro.vecinos.push(i-columnas+1)
                    }
                    else{
                        cuadro.vecinos.push(i-1)
                        cuadro.vecinos.push(i+1)
                        cuadro.vecinos.push(i+columnas-1)
                        cuadro.vecinos.push(i+columnas)
                        cuadro.vecinos.push(i+columnas+1)
                        cuadro.vecinos.push(i-columnas-1)
                        cuadro.vecinos.push(i-columnas)
                        cuadro.vecinos.push(i-columnas+1)
                    }
                }
                if(cuadro.valor !='💣'){
                    //Dependiendo del numero de minas en cada cuadro vecino que tenga cada cuadro se le da el numero 
                    let minas = cuadro.vecinos.filter(v=>this.cuadros[v].valor=='💣').length

                    if(minas>0){
                        cuadro.valor=minas
                        cuadro.claseValor= this.colores[minas]
                    }
                }
            }
            this.jugando = true
        },
        activarCuadro(cuadro){
            //aca se  recibe el cuadro y se validar si esta cubierto y si tiene o no bandera
            if(this.jugando && cuadro.inicial && !cuadro.bandera){
                cuadro.inicial=false

                if(!this.iniciar){
                    this.timer=setInterval( () =>{
                        this.segundos++
                    },1000)
                    this.iniciar = true
                }

                if(cuadro.valor=='💣'){
                    this.explosion(cuadro)
                }

                 else{
                     this.cuadrosRestantes--

                    if(this.cuadrosRestantes <= 0){
                         this.ganar()
                         //Si la cantidad de cuadro restatntes es 0 se llamaba al metodo ganar de lo contrario se siguen despejando los cuadros
                    }
                        else if(cuadro.valor==''){
                         cuadro.vecinos.forEach(v => {
                        this.activarCuadro(this.cuadros[v])
                        })
                    }

                }
            
            }
        },
        cambiarMinasRestantes(cuadro){
            //Para restar las minas se crea un evento y este metodo recibiendo la cantidad como parametro
            // y se envia un $emit a Cuadro.vue y cuando vuelve la respuesta dependiendo de si hay una bandera  
            //se resta  se le restablece el valor al contador de minas

            if(this.jugando){
                cuadro.bandera= !cuadro.bandera
                this.minasRestantes += cuadro.bandera ? -1:1
            }


        },
        explosion(cuadro){

            this.jugando=false

        //     //Si se pierde primero se detiene el tiempo y se revelan las minas que todavia no esten marcadas 
            this.detenerTime()

            this.minas.forEach(mina=>{
                if(!mina.bandera){
                    mina.inicial=false
                }
            })

            let banderas = this.cuadros.filter(c => c.bandera)
            //Se recorren las banderas y si estan marcadas y no tienen mina entonces se marca que estaban mal marcadas
            banderas.forEach(c=>{
                if(c.valor!='💣'){
                    c.banderas=false
                    c.valor='❌'
                    c.inicial=false
                }
            })

            cuadro.valor='💥'
            this.cara='😭'
        },
        ganar(){
            this.jugando= false
            this.detenerTime()

            this.minas.forEach(mina =>{
                mina.bandera=true
            })
            this.minasRestantes=0
            this.cara= '😎'
        }
    }
}
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@100&display=swap');


.uno{
    color: black;
        font-size: 18px;
    font-weight: bold;
}

.dos{
    color:rgb(2, 2, 92);
        font-size: 18px;
    font-weight: bold;
}

.tres{
    color:rgb(126, 2, 2);
        font-size: 18px;
    font-weight: bold;
}

.cuatro{
    color: rgb(3, 54, 10);
        font-size: 18px;
    font-weight: bold;
}

.cinco{
        color: rgb(51, 3, 73);
        font-size: 18px;
    font-weight: bold;
}

html{
       font-family: 'Roboto Mono', monospace; 
}

.tablero{
    display: grid;
    justify-content: center;
    background-color: rgb(199, 176, 216);
    padding: 15px;
    user-select: none;
}

.niveles{
    display: grid;
    grid-auto-flow: column;
    grid-gap: 10px;
    font-size: 24px;
    font-weight: bold;
    color: rgb(14, 10, 10);
    padding:5px;
    justify-content: start;
    align-items: center;
}

.nivel{
    width: 32px;
    height: 32px;
    color: rgb(5, 5, 5);
    text-align: center;
    vertical-align: center;
    cursor: pointer;
}

.nivel-seleccionado{
    color: rgb(1, 7, 12) !important;
    background-color: rgb(70, 100, 119);
    border-style: solid;
    border-width: 2px;
    border-radius: 50%;
    cursor: default;
}

.panel{
    display: grid;
    grid-auto-flow: column;
    font-size: 40px;
    margin-top: 10px;
    padding: 10px;
    border-top-color: rgb(160, 128, 184);
    border-left-color:rgb(160, 128, 184);
    border-right-color: rgb(224, 164, 221);
    border-bottom-color: rgb(224, 164, 221);
    border-style: solid;
    border-width: 2px;
}

.marcador{
    background-color: rgb(34, 13, 26);
    color: red;
    height: 40px;
    padding: 20px;
    border-top-color: rgb(160, 128, 184);
    border-left-color:rgb(160, 128, 184);
    border-right-color: rgb(224, 164, 221);
    border-bottom-color: rgb(224, 164, 221);
    border-style: solid;
    border-width: 1px;

}

.minas-restantes{
    
    justify-self: start;
}

.cara{
    display: grid;
    justify-content: center;
    align-items: center;
    justify-self: center;
    width: 50px;
    height: 50px;
    font-size: 40px;
    border-top-color: rgb(224, 164, 221);
    border-left-color:rgb(224, 164, 221);
    border-right-color: rgb(160, 128, 184);
    border-bottom-color: rgb(160, 128, 184);
    border-style: solid;
    border-width: 2px;
    cursor: pointer;
}

.segundos{
    background-color: rgb(34, 13, 26);
    padding: 20px;
    color: red;
    height: 40px;
    justify-self: end;
}

.matriz{
    display: grid;
    background-color:rgb(217, 195, 233) ;
    padding:2px;
    margin-top: 10px;
    border-top-color: rgb(160, 128, 184);
    border-left-color:rgb(160, 128, 184);
    border-right-color: rgb(224, 164, 221);
    border-bottom-color: rgb(224, 164, 221);
    border-style: solid;
    border-width: 5px;
}
</style>