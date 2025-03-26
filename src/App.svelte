<script>
  import './app.pcss';
  import CartaPokemon from './componentes/ListaPokemon.svelte';
  import { onMount } from 'svelte';

  let pokemonList = [];
  let activeView = 'home'; // Estado para controlar qué vista se muestra
  let limit = 20; // Límite inicial de Pokémon

  // Cargar Pokémon
  async function fetchPokemon() {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}`);
    const data = await response.json();
    pokemonList = data.results.map((pokemon, index) => ({
      ...pokemon,
      id: index + 1, // +1 en el ID para coincidir con el índice de la API
    }));
  }

  // Función para cargar más Pokémon
  function loadMorePokemon() {
    limit += 20; // Incrementar el límite en 20
    fetchPokemon(); // Volver a cargar Pokémon con el nuevo límite
  }

  // Cambiar la vista activa
  function setActiveView(view) {
    activeView = view;
    if (view === 'pokemon-types') {
      fetchPokemon(); // Cargar Pokémon si se selecciona la vista correspondiente
    }
  }
</script>

<!-- Estilo de fondo y diseño -->
<main class="min-h-screen bg-gradient-to-br from-red-500 text-white p-4">
  <header class="text-center py-8">
    <h1 class="text-4xl font-bold">Bienvenido a la Pokédex</h1>
    <p class="mt-2 text-xl">Explora el mundo de los Pokémon</p>
  </header>

  <!-- Menú de botones -->
  <div class="flex justify-center space-x-4 mb-8">
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('home')}
    >
      Inicio
    </button>
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('pokemon-types')}
    >
      Ver Pokémon por tipos
    </button>
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('sorted-by-size')}
    >
      Ordenar por tamaño
    </button>
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('pokemon-details')}
    >
      Ficha de un Pokémon
    </button>
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('tables')}
    >
      Listados como tablas
    </button>
    <button
      class="bg-white text-white px-4 py-2 rounded hover:bg-gray-200 transition"
      on:click={() => setActiveView('charts')}
    >
      Listados como gráficos
    </button>
  </div>

  <!-- Vistas dinámicas -->
  {#if activeView === 'home'}
    <div class="text-center">
      <h2 class="text-3xl font-bold">¡Hola, Entrenador!</h2>
      <p class="mt-4">Selecciona una opción para comenzar.</p>
    </div>

  {:else if activeView === 'pokemon-types'}
    <div>
      <h2 class="text-2xl font-bold mb-4">Lista de Pokémon</h2>
      <div class="grid grid-cols-2 gap-4">
        {#each pokemonList as pokemon}
          <CartaPokemon id={pokemon.id} name={pokemon.name} />
        {/each}
      </div>
      <!-- Botón para cargar más Pokémon -->
      <div class="flex justify-center mt-4">
        <button
          class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition"
          on:click={loadMorePokemon}
        >
          Cargar más Pokémon
        </button>
      </div>
    </div>

  {:else if activeView === 'sorted-by-size'}
    <div class="text-center">
      <h2 class="text-2xl font-bold">Próximamente: Ordenar Pokémon por tamaño</h2>
    </div>

  {:else if activeView === 'pokemon-details'}
    <div class="text-center">
      <h2 class="text-2xl font-bold">Próximamente: Ficha de un Pokémon</h2>
    </div>

  {:else if activeView === 'tables'}
    <div class="text-center">
      <h2 class="text-2xl font-bold">Próximamente: Listados como tablas</h2>
    </div>

  {:else if activeView === 'charts'}
    <div class="text-center">
      <h2 class="text-2xl font-bold">Próximamente: Listados como gráficos</h2>
    </div>
  {/if}
</main>

<style>
  /* Estilos generales */
  @import url('https://fonts.cdnfonts.com/css/pokemon-solid');
  h1 {
    font-family: 'Pokemon Solid', sans-serif;
  }
</style>