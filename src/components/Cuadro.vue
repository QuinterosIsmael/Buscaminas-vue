<template>
    <div @mousedown.right="bandera"
         @contextmenu.prevent
         @mouseup.left="activar" class="cuadro" :class="info.inicial ? 'cuadro-inicial': 'cuadro-vacio' ">
        <span :class="clase">{{ valor }}</span>
    </div>
</template>

<script>
export default {
    props:['info'],
    computed:{
        valor(){
            //dependiendo el valor que tenga va a mostrar una bandera
            //el cuadro vacio o el valor que tenga el cuadro
            
            if (this.info.inicial){
                if(this.info.bandera){
                    return '🚩'
                }
                else{
                    return ''
                }
            }
            else{
                return this.info.valor
            }
        },
        clase(){
            //determina la clase  dependiendo  el valor del cuadro
            if (this.info.inicial){
                if (this.info.bandera){
                    return 'bandera'
                }
                else{
                    return ''
                }
            }
            else{
                return this.info.claseValor
            }
        }
    },
    methods:{
        bandera(){
            //se valida  que el cuadro este cubierto y que se cambie el valor de si tiene o no una bandera
            if(this.info.inicial){

                this.$emit('onCambiarMinasRestantes', this.info)
                //aca se recibe y se envia un -1 si el valor es true y un 1 si es false
            }
        },
        activar(){
            //aca se usa la ayuda del padre para poder despejar los cuadros que esten vacios
                this.$emit('onActivar', this.info)  
        }
    }
}
</script>

<style>


    .cuadro{
    display: grid;
    justify-content: center;
    align-items: center;
}

.cuadro-inicial{
    width: 20px;
    height: 20px;
    border: 3px;
    background-color: rgb(188, 159, 209);
    border-top-color: white;
    border-left-color: white;
    border-right-color: rgb(115, 89, 133);
    border-bottom-color: rgb(115, 89, 133);
    border-style: solid;
    border-width: 3px;
    cursor: pointer;
}

.cuadro-vacio{
   width: 25px;
   height: 25px;
   background-color: rgb(173, 129, 180);
   border-right-color: rgb(146, 109, 140);
   border-bottom-color: rgb(106, 76, 107);
   border-top-width: 0;
   border-left-width: 0;
   border-bottom-width: 1px;
   border-right-width: 1px;
   border-style: solid;
   cursor: default;
}


</style>