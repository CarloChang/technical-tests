<script>
  import { onMount } from 'svelte';
  import { Badge } from '../shadcn/badge';
  import { writable } from 'svelte/store';

  export let id;
  export let name = 'Desconocido';

  const tipoTraducciones = {
    normal: 'Normal', fire: 'Fuego', water: 'Agua', electric: 'Eléctrico', 
    grass: 'Planta', ice: 'Hielo', fighting: 'Lucha', poison: 'Veneno', 
    ground: 'Tierra', flying: 'Volador', psychic: 'Psíquico', bug: 'Bicho', 
    rock: 'Roca', ghost: 'Fantasma', dragon: 'Dragón', dark: 'Siniestro', 
    steel: 'Acero', fairy: 'Hada'
  };

  let imageUrl = ''; // Imagen del Pokémon

  // Variables globales para el modal
  export let modalPokemon = writable(null);
  export let showModal = writable(false);

  async function loadImage() {
    if (!id) return;
    
    try {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
      const data = await response.json();
      imageUrl = data.sprites.other['official-artwork'].front_default || 'https://via.placeholder.com/150';
    } catch (error) {
      console.error('Error al cargar la imagen:', error);
      imageUrl = 'https://via.placeholder.com/150'; // Imagen por defecto en caso de error
    }
  }

  async function fetchDetails() {
    if (!id) return;

    try {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
      const data = await response.json();
      
      const types = data.types.map((t) => tipoTraducciones[t.type.name] || 'Desconocido');
      const weaknesses = await fetchWeaknesses(data.types.map(t => t.type.name));

      modalPokemon.set({
        name,
        height: data.height / 10,
        weight: data.weight / 10,
        types,
        weaknesses,
        image: data.sprites.other['official-artwork'].front_default || 'https://via.placeholder.com/150'
      });

      showModal.set(true);
    } catch (error) {
      console.error('Error al cargar los detalles del Pokémon:', error);
    }
  }

  async function fetchWeaknesses(types) {
    try {
      let weaknessesSet = new Set();
      for (let type of types) {
        const response = await fetch(`https://pokeapi.co/api/v2/type/${type}`);
        const data = await response.json();
        data.damage_relations.double_damage_from.forEach(t => {
          weaknessesSet.add(tipoTraducciones[t.name] || 'Desconocido');
        });
      }
      return Array.from(weaknessesSet);
    } catch (error) {
      console.error('Error al obtener debilidades:', error);
      return [];
    }
  }

  function getBadgeColor(type) {
    const colors = {
      Normal: 'bg-gray-200 text-gray-800', Fuego: 'bg-red-500 text-white',
      Agua: 'bg-blue-500 text-white', Eléctrico: 'bg-yellow-500 text-black',
      Planta: 'bg-green-500 text-white', Veneno: 'bg-purple-500 text-white',
      Tierra: 'bg-yellow-700 text-white', Volador: 'bg-sky-500 text-white',
      Psíquico: 'bg-pink-500 text-white', Bicho: 'bg-lime-500 text-black',
      Roca: 'bg-stone-500 text-white', Fantasma: 'bg-indigo-700 text-white',
      Dragón: 'bg-indigo-500 text-white', Siniestro: 'bg-gray-800 text-white',
      Acero: 'bg-gray-500 text-white', Hada: 'bg-pink-300 text-black'
    };
    return colors[type] || 'bg-gray-300 text-black';
  }

  // Reactivar la carga de imagen cuando cambie el ID del Pokémon
  $: id, loadImage();

  // Cargar la imagen del Pokémon al montar el componente
  onMount(loadImage);
</script>

<!-- Tarjeta de Pokémon -->
<div class="bg-white shadow-md rounded-lg p-4 text-center cursor-pointer transition transform hover:scale-105 hover:shadow-xl"
  on:click={fetchDetails}>
  <img
    src={imageUrl}
    alt={name}
    class="w-32 h-32 mx-auto mb-4"
  />
  <h2 class="text-xl font-bold text-gray-800">{name}</h2>
</div>

<!-- Modal de Detalles (se maneja globalmente) -->
{#if $showModal}
  {#await $modalPokemon then pokemon}
    <div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
      <div class="bg-gray-900 text-white p-5 rounded-lg shadow-lg max-w-xl w-full relative">
        <button class="absolute top-2 right-2 text-white hover:text-red-500" on:click={() => showModal.set(false)}>✖</button>
        <h2 class="text-2xl font-bold text-center mb-4">{pokemon.name}</h2>

        <img class="w-32 h-32 mx-auto" src={pokemon.image} alt={pokemon.name} />

        <div class="mt-4 text-center">
          <p><strong>Altura:</strong> {pokemon.height} m</p>
          <p><strong>Peso:</strong> {pokemon.weight} kg</p>
        </div>

        <div class="mt-4 text-center">
          <p><strong>Tipos:</strong></p>
          <div class="flex justify-center space-x-2">
            {#each pokemon.types as type}
              <Badge class={getBadgeColor(type)}>{type}</Badge>
            {/each}
          </div>
        </div>

        <div class="mt-4 text-center">
          <p><strong>Debilidades:</strong></p>
          <div class="flex justify-center space-x-2">
            {#each pokemon.weaknesses as weakness}
              <Badge class={getBadgeColor(weakness)}>{weakness}</Badge>
            {/each}
          </div>
        </div>

        <button class="mt-4 w-full bg-red-500 text-white py-2 rounded hover:bg-red-600 transition" on:click={() => showModal.set(false)}>
          Cerrar
        </button>
      </div>
    </div>
  {/await}
{/if}
