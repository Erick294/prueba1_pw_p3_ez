<template>
  <div class="imagenes">
    <img v-bind:src="imagenes[0]" alt="No se encontró el archivo">
    <img v-bind:src="imagenes[1]" alt="No se encontró el archivo">
    <img v-bind:src="imagenes[2]" alt="No se encontró el archivo">
  </div>

  <div class="nombres">
    <p>{{nombres[0]}}</p>
    <p>{{nombres[0]}}</p>
    <p>{{nombres[0]}}</p>
  </div>

  <div v-if="validarPerdida" class="mensaje_fallo">
    <h2>Haz utilizado tus 5 intentos</h2>
    <h2>El juego ha terminado, intentalo nuevamente</h2>
    <button v-on:click="reiniciar()">Nuevo Juego</button>
  </div>

  <div v-if="win" class="mensaje_logro">
    <h2>Puntaje: {{numPuntaje}}</h2>
    <img src="../img/congratulations.gif" alt="">
    <h2>Felicitaciones has ganado un premio de $10.000,00</h2>
    <button v-on:click="reiniciar()">Nuevo Juego</button>
  </div>

  <button v-if="habilitado" v-on:click="obtenerPokemonsArray()">Jugar</button>
</template>

<script>
export default {
    data(){
        return{
            numPuntaje: 0,
            numIntento: 5,
            pokemonNum: [12,45,125,47,9,13],
            pokemonArr: [],
            nombres: ["xxxxxxxx", "xxxxxxxx", "xxxxxxxx"],
            imagenes: [
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
            ],
            habilitado: true,
            win:false
        }
    },
    computed:{
        obtenerAleatorios(){
            return Math.floor((Math.random()*2)+1);
        },
        obtenerNumeroId(){
            console.log(this.pokemonNum[this.obtenerAleatorios]);
            return this.pokemonNum[this.obtenerAleatorios];       
        },
        validarPerdida(){
            if(this.puntaje==0){
                return true;
            }else{
                return false
            }
        }
    },
    methods:{
        async obtenerPokemonsArray() {
            this.pokemonArr = await this.obtenerPokemons();
            this.mostrarNombres();
            this.mostrarImagenes();
            this.aumentarPuntaje();
            this.disminuirIntentos();
        },
        async obtenerApi(id){
            const data = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then((r) => r.json());
            return data
        },
        async obtenerPokemons(){
            const data1 = await this.obtenerApi(this.obtenerNumeroId)
            const data2 = await this.obtenerApi(this.obtenerNumeroId)
            const data3 = await this.obtenerApi(this.obtenerNumeroId)
 
            this.pokemonArr[0] = { nombre: data1.name, id: data1.id, imagen: data1.front_default};
            this.pokemonArr[1] = { nombre: data2.name, id: data2.id, imagen: data2.front_default};
            this.pokemonArr[2] = { nombre: data3.name, id: data3.id, imagen: data3.front_default};
        },
        mostrarNombres() {
            this.nombres[0] = this.pokemonArr[0].nombre;
            this.nombres[1] = this.pokemonArr[1].nombre;
            this.nombres[2] = this.pokemonArr[2].nombre;
  
        },
        mostrarImagenes() {
            this.imagenes[0] = this.pokemonArr[0].imagen;
            this.imagenes[1] = this.pokemonArr[1].imagen;
            this.imagenes[2] = this.pokemonArr[2].imagen;
        },
        disminuirIntentos() {
            if (this.numIntento > 0) {
                console.log(this.numPuntaje)
                return this.numIntento--;
            } else if (this.numPuntaje < 10 && this.numIntento == 0) {
                this.habilitado = false;
            }else if (this.numPuntaje >= 10 && this.numIntento >= 0) {
                this.win=true;
                this.habilitado = false;
            }
        },
        aumentarPuntaje() {
            if (this.pokemonArr[0].id == this.pokemonArr[1].id &&
                this.pokemonArr[0].id == this.pokemonArr[2].id ) {
                return (this.numPuntaje += 5);
            } else if (
                this.pokemonArr[0].id == this.pokemonArr[1].id ||
                this.pokemonArr[0].id == this.pokemonArr[2].id ||
                this.pokemonArr[1].id == this.pokemonArr[2].id
            ) {
                return (this.numPuntaje += 2);
            } else {
                return (this.numPuntaje += 0);
            }
        },
        reiniciar() {
            (this.nombres = ["xxxxxxxx", "xxxxxxxx", "xxxxxxxx"]),
                (this.imagenes = [
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
                "https://static.vecteezy.com/system/resources/previews/001/209/957/non_2x/square-png.png",
                ]);
            this.numPuntaje = 0;
            this.numIntento = 5;
            this.habilitado = true;
            this.win = false;
            },
        },
    }
</script>

<style>
.imagenes{
    display: inline;
    text-align: center;
}

img{
    width: 255px;
    height: 255px;
    text-align: center;
    margin: 10px;
}

.nombres{
    display: block;
    text-align: center;
}

p{
    width: 512px;
    display: inline;
    margin: 105px;
}

button{
    border: solid;
    margin: 20px;
    padding: 10px 50px;
    font-size: 20px;
}

.mensaje_fallo h2{
    color: red;
    font-size: 20px;
    text-align: center;
}

.mensaje_logro h2{
    color:skyblue;
    font-size: 20px;
    text-align: center;
}
</style>