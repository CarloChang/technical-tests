<script>
  import { onMount } from 'svelte';

  export let onSelectType; // Callback cuando se elige un tipo
  let tipos = [];
  let selectedType = '';

  // Diccionario de traducción de tipos
  const tipoTraducciones = {
    normal: 'Normal', fire: 'Fuego', water: 'Agua', electric: 'Eléctrico',
    grass: 'Planta', ice: 'Hielo', fighting: 'Lucha', poison: 'Veneno',
    ground: 'Tierra', flying: 'Volador', psychic: 'Psíquico', bug: 'Bicho',
    rock: 'Roca', ghost: 'Fantasma', dragon: 'Dragón', dark: 'Siniestro',
    steel: 'Acero', fairy: 'Hada'
  };

  async function fetchTipos() {
    const response = await fetch('https://pokeapi.co/api/v2/type');
    const data = await response.json();
    tipos = data.results.map((type) => ({
      name: type.name,
      translatedName: tipoTraducciones[type.name] || 'Desconocido' // Traducción o 'Desconocido' si no está en la lista
    }));
  }

  onMount(fetchTipos);
</script>

<div class="mb-4 text-center">
  <label for="tipo" class="block text-white text-lg mb-2">Filtrar por tipo:</label>
  <select
    id="tipo"
    bind:value={selectedType}
    on:change={() => onSelectType(selectedType)}
    class="p-2 rounded bg-white text-black"
  >
    <option value="">Todos</option>
    {#each tipos as tipo}
      <option value={tipo.name}>{tipo.translatedName}</option>
    {/each}
  </select>
</div>
