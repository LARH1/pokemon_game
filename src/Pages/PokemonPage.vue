<template>
<div>
    <div v-if="pokemon.id">
        <poke-image :pokeId="pokemon.id" :isVisible="showPoke" />
        <poke-options :options="poke_data" @poke-selection="CheckSelected" ref="poke_options" />

        <div v-if="showAnswer" class="fade-in">
            <h3>{{message}}</h3>
            <button class="btn btn-primary" @click="Restart">
                <i class="fa-solid fa-rotate-left fa-lg"></i>
            </button>
            <br>
            <br>
        </div>
    </div>
    <div v-else class="text-center">
        <div class="spinner-grow text-info" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>

    </div>
</div>
</template>

<script>
import getOptions from "@/helpers/options";
import PokeImage from "@/components/PokeImage.vue";
import PokeOptions from "@/components/PokeOptions.vue";
export default
{
    components:
    {
        "poke-image": PokeImage,
        "poke-options": PokeOptions,
    },
    data()
    {
        return {
            id: 1,
            poke_data: [],
            pokemon:
            {},
            showPoke: false,
            showAnswer: false,
            message: "",
        }
    },
    methods:
    {
        /**
         * Obtener la lista de pokemones
         */
        async GetData()
        {
            // Obtener las opciones
            this.poke_data = await getOptions();
            // Obtener el ganador
            const id = Math.floor(Math.random() * 4);
            this.pokemon = this.poke_data[id];
        },

        /**
         * Comprobar el pokemon seleccionado
         */
        CheckSelected(pokeId)
        {
            this.showAnswer = true;
            this.showPoke = true;
            let correct = pokeId == this.pokemon.id;
            if (correct)
                this.message = `¡Correcto, es ${this.pokemon.name}!`;
            else
                this.message = `Oops!, era ${this.pokemon.name}`;
            this.$refs.poke_options.CheckWinner(correct);
        },

        /**
         * Reiniciar juego
         */
        Restart()
        {
            this.poke_data = [];
            this.pokemon = {};
            this.message = "";
            this.showPoke = false;
            this.showAnswer = false;
            this.GetData();
            this.$refs.poke_options.Reset();
        }
    },
    mounted()
    {
        this.GetData();
    }
}
</script>
