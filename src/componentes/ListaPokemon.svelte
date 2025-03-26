<script>
  export let id;
  export let name = 'Desconocido';

  // Mapeo de traducciones
  const tipoTraducciones = {
    normal: 'Normal',
    fire: 'Fuego',
    water: 'Agua',
    electric: 'Eléctrico',
    grass: 'Planta',
    ice: 'Hielo',
    fighting: 'Lucha',
    poison: 'Veneno',
    ground: 'Tierra',
    flying: 'Volador',
    psychic: 'Psíquico',
    bug: 'Bicho',
    rock: 'Roca',
    ghost: 'Fantasma',
    dragon: 'Dragón',
    dark: 'Siniestro',
    steel: 'Acero',
    fairy: 'Hada',
  };

  let details = {};
  let types = []; // Array para almacenar los tipos traducidos

  async function fetchDetails() {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
    details = await response.json();
    types = details.types.map((type) => tipoTraducciones[type.type.name] || 'Desconocido');
  }

  fetchDetails();
</script>

<div class="bg-white shadow-md rounded-lg p-4 text-center">
  <!-- Imagen del Pokémon -->
  <img
    src={details.sprites?.other['official-artwork']?.front_default || 'https://via.placeholder.com/150'}
    alt={name}
    class="w-32 h-32 mx-auto mb-4"
  />
  <!-- Nombre -->
  <h2 class="text-xl font-bold text-gray-800">{name}</h2>
  <!-- Tipos -->
  <p class="mt-2 text-gray-600">
    Tipo:
    {#each types as type}
      <span class="bg-gray-200 text-gray-800 px-2 py-1 rounded-full mr-1">{type}</span>
    {/each}
  </p>
</div>