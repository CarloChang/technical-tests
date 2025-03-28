<script>
  import './app.pcss';
  import CartaPokemon from './componentes/ListaPokemon.svelte';
  import SelectTipos from './componentes/SelectTipos.svelte';
  import { onMount } from 'svelte';

  let pokemonList = [];
  let filteredPokemonList = [];
  let selectedType = '';
  let sortOrder = 'default'; // Opciones: 'default', 'asc', 'desc'
  let activeView = 'home';

  // Obtener los Pokémon con sus detalles
  async function fetchPokemon() {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=200`);
    const data = await response.json();

    // Obtener detalles de cada Pokémon (para filtrar por tipo y tamaño)
    const pokemonDetails = await Promise.all(
      data.results.map(async (pokemon, index) => {
        const detailsResponse = await fetch(pokemon.url);
        const details = await detailsResponse.json();
        return {
          id: index + 1,
          name: pokemon.name,
          types: details.types.map((t) => t.type.name), // Obtener tipos
          height: details.height // Guardamos la altura
        };
      })
    );

    pokemonList = pokemonDetails;
    applyFilters();
  }

  // Filtrar y ordenar la lista
  function applyFilters() {
    let filtered = selectedType === ''
      ? [...pokemonList] // Copiamos la lista original
      : pokemonList.filter((pokemon) => pokemon.types.includes(selectedType));

    // Aplicamos el orden seleccionado
    if (sortOrder === 'asc') {
      filtered.sort((a, b) => a.height - b.height);
    } else if (sortOrder === 'desc') {
      filtered.sort((a, b) => b.height - a.height);
    } else {
      filtered.sort((a, b) => a.id - b.id); // Orden por defecto
    }

    filteredPokemonList = filtered.slice(0, 20); // Tomamos solo los primeros 20 Pokémon
  }

  // Cuando cambia el tipo seleccionado
  function filterByType(type) {
    selectedType = type;
    applyFilters();
  }

  // Cuando cambia el orden
  function changeSortOrder(order) {
    sortOrder = order;
    applyFilters();
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
    <button class="bg-white text-black px-4 py-2 rounded hover:bg-gray-200 transition" on:click={() => activeView = 'pokemon-types'}>
      Ver Pokémon por tipos
    </button>
  </div>

  {#if activeView === 'pokemon-types'}
    <div>
      <h2 class="text-2xl font-bold mb-4">Filtrar Pokémon por Tipo</h2>
      <SelectTipos onSelectType={filterByType} />

      <!-- Selector de orden -->
      <div class="mb-4">
        <label for="sortOrder" class="block text-lg mb-2">Ordenar por tamaño:</label>
        <select
          id="sortOrder"
          bind:value={sortOrder}
          on:change={() => changeSortOrder(sortOrder)}
          class="px-10 rounded bg-white text-black"
        >
          <option value="default">Por defecto (ID)</option>
          <option value="asc">De menor a mayor</option>
          <option value="desc">De mayor a menor</option>
        </select>
      </div>

      <div class="grid grid-cols-2 gap-4">
        {#each filteredPokemonList as pokemon}
          <CartaPokemon id={pokemon.id} name={pokemon.name} />
        {/each}
      </div>
    </div>
  {/if}
</main>
