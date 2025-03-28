<script>
  import { onMount } from 'svelte';

  export let onSelectType; // Función de callback cuando se elige un tipo
  let tipos = [];
  let selectedType = '';

  const tipoTraducciones = {
    normal: 'Normal', fire: 'Fuego', water: 'Agua', electric: 'Eléctrico', 
    grass: 'Planta', ice: 'Hielo', fighting: 'Lucha', poison: 'Veneno', 
    ground: 'Tierra', flying: 'Volador', psychic: 'Psíquico', bug: 'Bicho', 
    rock: 'Roca', ghost: 'Fantasma', dragon: 'Dragón', dark: 'Siniestro', 
    steel: 'Acero', fairy: 'Hada'
  };

  async function fetchTipos() {
    try {
      const response = await fetch('https://pokeapi.co/api/v2/type');
      const data = await response.json();

      // Filtramos solo los tipos válidos
      let tiposFiltrados = data.results.map((type) => ({
        name: tipoTraducciones[type.name] || null, // Si no existe en traducciones, lo ignoramos
        value: type.name, // Guardamos el valor original
      })).filter(tipo => tipo.name !== null); // Eliminamos "Desconocidos" desde el principio

      // Agregamos "Todos" al inicio
      tipos = [{ name: 'Todos', value: '' }, ...tiposFiltrados];

      console.log("Tipos cargados:", tipos); // Depuración en consola
    } catch (error) {
      console.error('Error al obtener los tipos:', error);
    }
  }

  onMount(fetchTipos);
</script>

<div class="mb-4">
  <label for="tipo" class="block text-white text-lg mb-2">Filtrar por tipo:</label>
  <select
    id="tipo"
    bind:value={selectedType}
    on:change={() => onSelectType(selectedType)}
    class="p-5 rounded bg-white text-black"
  >
    {#each tipos as tipo}
      <option value={tipo.value}>{tipo.name}</option>
    {/each}
  </select>
</div>
