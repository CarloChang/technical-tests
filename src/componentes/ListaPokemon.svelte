<script>
  import { onMount } from 'svelte';
  import { Badge } from '../shadcn/badge';

  export let id;
  export let name = 'Desconocido';

  const tipoTraducciones = {
    normal: 'Normal', fire: 'Fuego', water: 'Agua', electric: 'Eléctrico', 
    grass: 'Planta', ice: 'Hielo', fighting: 'Lucha', poison: 'Veneno', 
    ground: 'Tierra', flying: 'Volador', psychic: 'Psíquico', bug: 'Bicho', 
    rock: 'Roca', ghost: 'Fantasma', dragon: 'Dragón', dark: 'Siniestro', 
    steel: 'Acero', fairy: 'Hada'
  };

  let details = {};
  let types = [];

  async function fetchDetails() {
    if (!id) return; // Evita llamadas innecesarias

    try {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
      const data = await response.json();
      details = { ...data }; 
      types = data.types.map((type) => tipoTraducciones[type.type.name] || 'Desconocido');
    } catch (error) {
      console.error('Error al cargar los detalles del Pokémon:', error);
    }
  }

  // Solo se ejecuta cuando cambia `id`
  $: if (id) fetchDetails();

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
    return colors[type] || 'bg-gray-300 text-black'; // Si no encuentra el tipo, usa un color por defecto
  }
</script>

<!-- Tarjeta de Pokémon -->
<div class="bg-white shadow-md rounded-lg p-4 text-center">
  <img
    src={details.sprites?.other?.['official-artwork']?.front_default || 'https://via.placeholder.com/150'}
    alt={name}
    class="w-32 h-32 mx-auto mb-4"
  />
  <h2 class="text-xl font-bold text-gray-800">{name}</h2>
  <p class="mt-2 text-gray-600 flex justify-center gap-2">
    {#each types as type}
      <Badge class={getBadgeColor(type)}>{type}</Badge>
    {/each}
  </p>
</div>
