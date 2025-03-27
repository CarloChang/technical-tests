<script>
  import './app.pcss';
  import CartaPokemon from './componentes/ListaPokemon.svelte';
  import SelectTipos from './componentes/SelectTipos.svelte';
  import { onMount } from 'svelte';

  let pokemonList = [];
  let filteredPokemonList = [];
  let selectedType = '';
  let activeView = 'home';

  // Obtener los Pokémon con sus detalles
  async function fetchPokemon() {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=200`);
    const data = await response.json();

    // Obtener detalles de cada Pokémon (para filtrar por tipo)
    const pokemonDetails = await Promise.all(
      data.results.map(async (pokemon, index) => {
        const detailsResponse = await fetch(pokemon.url);
        const details = await detailsResponse.json();
        return {
          id: index + 1,
          name: pokemon.name,
          types: details.types.map((t) => t.type.name), // Obtener tipos
        };
      })
    );

    pokemonList = pokemonDetails;
    filteredPokemonList = pokemonList.slice(0, 20);
  }

  // Filtrar por tipo
  function filterByType(type) {
    selectedType = type;
    if (type === '') {
      filteredPokemonList = pokemonList.slice(0, 20);
    } else {
      filteredPokemonList = pokemonList.filter((pokemon) => pokemon.types.includes(type)).slice(0, 20);
    }
  }

  onMount(fetchPokemon);
</script>

<main class="min-h-screen bg-gradient-to-br from-red-500 text-white p-4">
  <header class="text-center py-8">
    <h1 class="text-4xl font-bold">Bienvenido a la Pokédex</h1>
    <p class="mt-2 text-xl">Explora el mundo de los Pokémon</p>
  </header>

  <!-- Menú de botones -->
  <div class="flex justify-center space-x-4 mb-8">
    <button class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition" on:click={() => activeView = 'pokemon-types'}>
      Ver Pokémon por tipos
    </button>
  </div>

  {#if activeView === 'pokemon-types'}
    <div>
      <h2 class="text-2xl font-bold mb-4">Filtrar Pokémon por Tipo</h2>
      <SelectTipos onSelectType={filterByType} />

      <div class="grid grid-cols-2 gap-4">
        {#each filteredPokemonList as pokemon}
          <CartaPokemon id={pokemon.id} name={pokemon.name} />
        {/each}
      </div>
    </div>
  {/if}
</main>
