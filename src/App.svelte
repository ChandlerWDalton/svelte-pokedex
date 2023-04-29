<script>
	import { onMount } from "svelte";
	let pokemon = [];
	let filteredPokemon = [];
	let selPokemon;
	let types = [];
    let favorites = []
	let favorited = false;

	onMount(async () => {
		pokemon = await load();
		filteredPokemon = pokemon;
		types = [
			'bug',
      		'dragon',
			'electric',
			'fighting',
			'fire',
			'flying',
			'ghost',
			'grass',
			'ground',
			'ice',
			'normal',
			'poison',
			'psychic',
			'rock',
			'water',
			'dark',
			'steel',
			'fairy'
		];
		favorites = []

	});

	async function load() {
        try{
            const retrievedpokemon = await getPokemon();
            return retrievedpokemon;
        } catch (err){
            console.error('Error retreiving pokemon', err);
        }
    }

    async function getPokemon(){
        let johtoPokemon = []
        for (let i = 152; i <= 251; i++){
            try{
                const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${i}/`);
                const pokemonData = await response.json();
                johtoPokemon.push(pokemonData);
            } catch(err){
                console.error('Error getting pokemon', err)
            }
        }
        return johtoPokemon

    }

    function showPokemon(pokemon){
        selPokemon = pokemon
		favorited = false;

		if(favorites.includes(pokemon.name)){
			favorited = true;
		}
    }

    function filterPokemon(type){

      filteredPokemon = pokemon.filter(x => {
        if(x.types.filter(e => e.type.name === type).length > 0)
        {
          return true
        }
      });
    }

    function viewFavorites(){
      filteredPokemon = pokemon.filter(pokemon => {
        if(favorites.includes(pokemon.name))
        {
          return true
        }
      });
    }

	function reset(){
		filteredPokemon = pokemon
	}


	function toggleFavorite(){
        favorited = !favorited;
        if(favorited){
            favorites.push(selPokemon.name)
			console.log(favorites);
        } else {
            favorites = favorites.filter(favorite => favorite !== selPokemon.name)
        }
    }

</script>

<main>
	<h1>Johto Dex</h1>
	<div class="body">
		{#if selPokemon}
		<div class="selPokemon">
		  <div class="selPokemon-images">
			<img class="sel-Image" src={selPokemon.sprites.front_default} alt="front sprite" />
            {#if !favorited}
            <a>
            <svg on:click={toggleFavorite} xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="m-4 inline-block w-8 h-8 stroke-current"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path></svg>
            </a>
            {:else}
            <a>
            <svg on:click={toggleFavorite} xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="m-4 inline-block w-8 h-8 stroke-current fill-current"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path></svg>
            </a>
            {/if}
		  </div>
		  <h3>{selPokemon.name}</h3>
		  <p># {selPokemon.id}</p>
		  <p>Height: {selPokemon.height} M</p>
		  <p>Weight: {selPokemon.weight} KG</p>
		</div>
		{/if}
		<div class="allPokemon">
		  <div class="filter-nav">
			{#each types as type}
			<button on:click={filterPokemon(type)}>{type}</button>
			{/each}
			<button on:click={viewFavorites}>FAVORITED</button>
			<button on:click={reset}>RESET</button>
		  </div>
		  <ul>
			{#each filteredPokemon as pokemon}
			<li key={pokemon.name}>
			  <div on:click={showPokemon(pokemon)}> 
				<img class="rounded-box btn bg-secondary h-24" src={pokemon.sprites.front_default} alt="front sprite" />
				<a href={pokemon.url}>{ pokemon.name }</a>
			  </div>
			</li>
			{/each}
		  </ul>
		</div>
	  </div>
</main>

<style>
	main {
		text-align: center;
	}

	h1 {
		color: black;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.body {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  flex-wrap: wrap;
}

h3 {
  margin: 40px 0 0;
}

.selPokemon {
  padding: 1em;
  margin: 1em;
  border: 1px solid black;
  border-radius: 10px;
}

.selPokemon div {
  border-bottom: 1px solid black;
}

.allPokemon{
  width: 100%;
}

.filter-nav {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  background-color: #dddddd;
  padding: 1em 0;
  justify-content: space-around;
}

.sel-Image {
  width: 200px;
  height: auto;
}

.selPokemon-images {
  display: flex;
  flex-direction: row;
  align-self: center;
}

svg {
  width: 2em;
  stroke: black;
}

button {
  margin: 0 5px;
  padding: 1em;
  border: 1px solid #42b983;
  border-radius: 10px;
  background-color: #42b983;
  color: white;
  cursor: pointer;
}

ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
}
li {
  margin: 0 10px;
}
li div {
  display: flex;
  flex-direction: column;
  padding: 1em;
  border: 1px solid #42b983;
  margin: .5em;
  border-radius: 10px;
}

li div:hover {
  background-color: #42b983;
}

a {
  color: black;
}
</style>